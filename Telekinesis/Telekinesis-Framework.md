# Telekinesis Client/Server Framework

## Concept

Telekinesis provides a framework for developing GUI applications that
have a client and server side component. Those applications are visually
merged and presented to the end user in such a way that the end user's
“user experience” is the same as if the user was interacting with a
strictly server side application. Telekinesis mediates the communication
between those server side and client side application parts.

You can imagine Telekinesis as a rubber band between two application parts
(one client side, one server side). All communication between those
application counterparts goes "through" that rubber band.

### Server & Client Side TeKi-aware Applications and Services

Terminology: a server side (GUI) application utilizing the Telekinesis
Framework is called a TeKi-aware Application. The client side counterpart
of that TeKi-aware Application we name "the corresponding TeKi-aware
Service".

As a reference implementation you can imagine a server side media player
GUI (TeKi-aware application) and a client side video overlay
(corresponding TeKi-aware service). The media player GUI
"remote-controls" the client side video overlay. The video overlay
receives its video stream from the server. All these interactions are
mediated through Telekinesis.

The Telekinesis framework consists of two main components. The server
side and the client side Telekinesis component ("TeKi Server" and "TeKi
Client"). On both ends you can plug-in Telekinesis-aware applications and
their corresponding services (e.g. the media player GUI and the video
overlay).

Normally,  a TeKi-aware application is launched server side by the
session user. The TeKi-aware application (e.g. the aTelePlayer) is split
into "tasks" (e.g. the GUI controls and "state keeper" mechanisms) that
run server side and "some task(s)" (e.g. video and/or audio playback)
that run(s) client side.

The communication between those two application parts is handled through
the Telekinesis Framework, most of all by the Telekinesis "TeKi Server"
and the Telekinesis "TeKi Client".

Whenever a TeKi-aware application is launched server side, Telekinesis
mediates the launch-up of the corresponding service on the TeKi Client 
side.

The interaction between the client side part and the server side part of
a TeKi-aware application and its service is subject to the design of the
respective application itself. Telekinesis requires an initial connection
handshake and handles resume / suspend events after a well-defined IPC
protocol (see Control Channel IPC section at the end of this document),
but the  actual data that gets exchanged between the  TeKi-aware
application and its service is arbitrary (except there  being a size
limit and a limit in numbers for transmitted "jabs" of control data).

Saying this, once the application has properly registered with TeKi
Server, Telekinesis will simply forward any sort of control data dumped
into it. This works in a bi-directional manner and it is designed to be
real time capable. This also implies that the data required for "syncing"
the  server's TeKi-aware application state with the corresponding client
side TeKi-aware service should be designed in a bandwidth-friendly
manner.

### TeKi-aware Applications and Data Streaming / Web-Proxying

TeKi-aware applications can additionaly request data streams and/or
web-proxying streams from the TeKi Server. E.g., a TeKi-aware media
player GUI makes no sense, if we don't provide means of transporting a
video stream from server to client.

In Telekinesis, data streaming and web-proxying have lower priority than
control data transmissions. Neither of these data streams are latency
sensitive.

### Server Side TeKi Server

TeKi Server is the server side core component of Telekinesis. It should
be launched whenever a remote (desktop) environment is initiated, and
remain running for as long as the environment is up and running.
Regardless of it  being in a suspended or user interactive state.

There is one "TeKi Server" instance per remote desktop instance. By
default, the TeKi Server instance and related server side TeKi-aware
applications are owned by the "user" and covered by said user's,
permissions and limitations. (Same applies to the corresponding
TeKi-aware services running on the client side).

For "per user" virtualized environments, where a more restrictive
environment may be desired, TeKi Server may eventually be capable of
running under a dedicated TeKi system user account, as to be able to
limit user actions.

TeKi Server waits on a so called "Application Socket" for the user to
launch TeKi-aware applications from within the remote (desktop) session.

This  "Application Socket" (a Unix file socket) is a permanent socket. It 
gets opened at the beginning of the user's remote (desktop) session (i.e.
with TeKi Server startup) and cleaned up once the (desktop) session
terminates (i.e. when TeKi Server exits). It only accepts new connections
from TeKi-aware applications if the session is in a user interactive
state. However, already connected TeKi-aware applications are kept
connected during non-interacive phases of the remote (desktop) session.

When a TeKi-aware application is launched, it connects to the
"Application Socket" and thus gets "hooked together" with its
corresponding TeKi-aware service on the TeKi Client's side.

### Socket Forwarding

The environment where Telekinesis gets integrated has to provide
respective means of data forwarding over channels. Telekinesis client and
server assume that they can reach each other directly. If that is not the
case  (and it is indeed not in most setups), some external tool has to
set up the socket forwarding ("socketeering") between TeKi Client and
TeKi Server:

```
Telekinesis Socketeering
------------------------

service_01 -|                            ,- app_01
service_02 -|                            |-- app_02
   ...                                   ...
service_NN -|                            |-- app_NN
            |                            |
            v                            v
            * TeKi Service Socket        * TeKi Application Socket
            | (localhost TCP port,       | (Unix socket file)
            |  Unix socket file)         |
            |                            |
    TeKi Client                       TeKi Server
    ===========                       ===========

    Control Channel Connector --T---> * Control Socket (listening)

    Data Stream Connector ------T---> * Data Stream Socket
                                           |
                                           `- per-user lighttpd

    Web Proxy Connector --------T---> * Web Proxy Socket
                                           |
                                           `- per-user tinyproxy
                                              or system-wide proxy
                                              server


Fig. 1 - Socketeering Scheme of the Telekinesis Framework

Legend:

  *  = Listening Socket
  -> = Connection Attempt
  T  = Socket Forwarding Tunnel
  
```

### Client Side TeKi Client

Once, a proper socket forwarding is in place, you can have TeKi Client
started (normally by means of a remote application/desktop client tool).

Successfully pairing TeKi Client and TeKi Server requires TeKi Client to
authenticate against TeKi Server with an authentication token. So,
whatever launches the TeKi Client will need to pass this authentication
token from server to client prior to the TeKi Client startup /
authentication. The token can be passed into TeKi Client either by means
of "ENV" or command line.

The client side TeKi will connect to the server side TeKi and stay in
constant communication for the duration of the (client side) remote
(desktop) session. A TeKi Client session starts with remote (desktop)
session startup or when resuming a remote (desktop) session. A TeKi
Client session ends when the remote (desktop) session is being terminated
or suspended.

Whereas the TeKi server "survives" non-interactive phases of a remote
(desktop) session, the TeKi Client comes up and exits, connects and
disconnects when resuming and suspending.

While connected, periodic round trip time (RTT) checks are performend and
an RTT value is computed and made avilable to TeKi-aware services and
applications, as to allow them to adopt opperations based on current
latency conditions.

The client side TeKi is stateless and all "states" are poplulated from
server originating data upon completion of service compatability
negotiation. For media playback the current playback position is reported
back to the server side, and a media player may then be able to resume at
the point that was last reported before session suspension. See prototype
demo here: https://www.youtube.com/watch?v=57AuYOxXPRU

There is exactly one "TeKi Client" instance per remote (desktop) session.
So when connected to multiple sessions, multiple TeKi Client instances
are active on the client machine. (Only relevant if running muliple
remote (desktop) session clients or using a remote (desktop) session
client with multi-session support).

After TeKi Server has successfully been connected and authenticated to,
TeKi Client opens a locally listening "Service Socket". This "Service
Socket" waits for incoming connections from spawned client side
TeKi-aware services.

A Teki-aware service gets spawned by TeKi Client whenever a TeKi-aware
application on TeKi's server side requests it.

Once the TeKi-aware service gets initiated it attempts to connect to TeKi
Client's "Service Socket". Once that has happened, TeKi-aware application
and TeKi-aware service can communicate bi-directionally over
Telekinesis's control channel.

Example: In a remote environment, a TeKi-aware media player gets launched
(this is our TeKi-aware application). If TeKi-Client and TeKi-Server are
well connected, TeKi Client will receive a request from the media player
GUI (through TeKi Server) to prepare (via a TeKi-aware service) for
showing incoming video streams in a client side video overlay frame,
exactly at the media player GUI's position.

### TeKi Client's Platform Abstraction Feature

TeKi Server must not have to deal with any aspect of the client side
system environment. Technical details like client OS, host architecture,
graphical performance must be hidden from TeKi Server entirely. Saying
this, we are required to provide platform dependent implementations of
TeKi Client.

#### Unix file sockets vs. local TCP sockets

On client platforms with propper Unix file socket support, Unix file
sockets are used everywhere. On other platforms, TeKi Client falls back
to using TCP sockets (bound to localhost only). Opening ports on
multi-user client systems potentially exposes those sockets to DoS
attacks. All connections require the connecting service to authenticate
with a token inserted into ENV before the service is initiated. The TeKi
Client code handles unauthenticated requests resiliently. Such requests
get immediately (and unpolitely) dropped with no explanation or other
form of feedback to the connecting agent whatsoever.

#### Appropriate Representation of Data

TeKi Client is responsible for determining the apropriate client side
representation of server originating content, depending on client
platform and capabilities. (E.g. on Linux client platforms video playback
may be better handled via the mpv executable, whereas on MS Windows
client platforms video performs better when displayed over GStreamer).

#### Reparenting vs. Geometry Synchronization

If the graphical transport (e.g. NXv3) and the client side platform (e.g.
some Unix OS running X11) support X11's window reparenting feature,
client-side window overlays can be directly reparented into server-side
GUI elements. Nothing to do for TeKi Client here.

Whereas, on platforms (e.g. MS Windows) and with graphical transports
(e.g. RDP, VNC) where direct X11 reparenting is not an option, TeKi
Client is responsible for correctly positioning graphical elements
rendered by the TeKi-aware service.

Example: Whenever the video player GUI gets moved/resized within a remote
desktop session, TeKi Server transmits position, size, focus and
visibility states of the expected video frame to TeKi Client. TeKi Client
then calculates the correct position and size of the video overlay
relative to client side display geometry and remote desktop session
window placement on the physical client monitor. It also checks if other
client side windows actually  hover above the remote desktop session
window. If that happens, the video overlay gets hidden immediately. This
all happens in "real time".

#### High Latency Pre-Cautions

Telekinesis continuously measures the current round trip time (RTT). If
the RTT value of the TeKi Server/Client connection rises, TeKi-aware
service should take pre-cautions against rendering flaws caused by high
latency.

Let's consider a sudden increase in latency (by whatever reason).  On
platforms where reparenting is not available, overlayed frames may then
seem as lagging behind when a TeKi-aware application (i.e. a server side
GUI) is moved or resized. In such a case, the TeKi-aware service will
"hide" the overlayed frame during motion events. This tiny behaviour
change is hardly perceivable by the user, whereas two graphical elements
(a GUI window server side, some kind of overlay client side) hunting one
another may cause the user's consternation.

### Telekinesis Session cleanup

#### Server Side

The rules are simple.

* TeKi-aware appliations clean up properly after themselves when exiting
* TeKi Server cleans up after itself
* TeKi Server "knows" how to clean up after crashed TeKi-aware applications

#### Client Side

The rules are simple, too.

* TeKi-aware service clean up properly after themselves when exiting (i.e. when a remote session is terminating or suspending)
* TeKi Clients cleans up after itself
* TeKi Clients "knows" how to clean up after crashed TeKi-aware applications
* Crashed TeKi-aware services must be re-launched by TeKi Client (after proper cleanup)
* If TeKi Client has vanished and the TeKi-aware service has become orphaned, it must exit immediately


## Required Connections ("socketeering")

Please use fig. 1 as reference when studying the below socket setup.

### Server Side (TeKi Server) Socketeering

The _server side_ TeKi at least listens on two Unix domain socket files:

* Socket 1 (listening): "The Control Socket" - for client side TeKi to connect to.
* Socket 2 (listening): "The Application Socket" - TeKi-aware applications within the remote desktop environment register with the TeKi Server instance over this socket. They connect to TeKi Server over this application socket and use it for communicating with its counterpart service on the TeKi Client side.
* optional Socket 3 (data stream http): "The Data Stream Socket". TeKi Server will bind a per-user lighttpd instance to this incoming socket for serving data streams.
* optional Socket 4 (data stream webproxy): "The Web Proxy Socket". TeKi Server will attach a per-user tinyproxy instance to this incoming socket. This socket is used for providing "web access" to a client side TeKi-aware service that requires seeing "the web" as the remote (desktop) server "sees" it.

Historically,  the initial TeKi prototype (listening on TCP) would close
the "client facing" socket whenever a session has got suspended. When
using Unix domain file sockets, a permanently open TeKi Server Control
Socket is not security issue anymore, thus recent TeKi Server versions
keep the Control Socket open permanently.

The TeKi Server is constantly in contact with attached TeKi-aware
applications (connected via the "Application Socket"). TeKi Server keeps
track of session resumption / suspension events and notifies all
TeKi-aware applications when such events occur. 

TeKi Server is also responsible for keeping track of available server
side TeKi-aware applications and for negotiating service compatability
with connecting TeKi Clients.

### Client Side (TeKi Client) Socketeering

The _client side_ TeKi at least listens on one "socket" and connects to
another "socket":

* Socket 1 (connecting): "The Control Socket" - in usual setups, the TeKi Client connects to the Control Socket of the TeKi Server.
* Socket 2 (listening: "The Service Socket" - all TeKi-aware applications have a corresponding TeKi-Aware service. The service is spawned on application request by TeKi Client. When spawned the TeKi-aware service attempts connecting to TeKi Client's Service Socket.
* optional Socket 3 (connecting on demand): Stream big data chunks from TeKi Server to TeKi Client. Data streaming is handled through a TeKi server side lighttpd instance being launched on demand by Telekinesis.
* optional Socket 4 (connecting on demand): Forward http (and alike) requests to the "internet" as seen from the TeKi server side. Web proxying is mediated through a TeKi server side tinyproxy instance being launched on demand by Telekinesis.

### The TeKi Control Channel / Socket

The TeKi Control Channel provides bi-directional means of communication
between TeKi Server and TeKi client. The TeKi Control channel is provided
to TeKi-aware applications for synchronizing server side and client side
events and/or handling server side and client side requests. Everything
poured into the TeKi Control Socket server side comes immediately out of
the TeKi Control Socket client side. And vice versa.

#### Low bandwidth consumption

The amount of control data a TeKi-aware application can "pour" into a
TeKi control socket is limited by TeKi, so TeKi-aware applications must
"limit themselves" regarding the amount of communication taking place
over the TeKi control channel. 

The exact details of this are subject to Telekinesis's Control Channel
IPC protocol (see IPC section at end of this document).

#### Persistent Forwarding

The Control Channel design expressively avoids round trips. Sent requests
don't expect a reply (or error message). Incoming events don't expect a
reply (or error message), either.

#### Multiplexing

Telekinesis manages the Control Channel like a multiplexer. Various
TeKi-aware applications/services can hand-over communication "jabs" to
Telekinesis on both ends of the channel at the same time and TeKi
projects those "jabs" to the other end of the channel and hands each
"jab" over to the correct corresponding TeKi-aware service/application
counterpart.

#### I/O

The default control channel setup is: TeKi Client connects to TeKi
Server. As TeKi Server always runs on a Unix-like platform, the TeKi
Server always listens on a Unix domain socket file. The TeKi client can
connect to Unix domain socket files (AF_UNIX, SOCK_STREAM) and/or to TCP
(AF_INET(6), SOCK_STREAM) sockets.

In normal setups, there is a channel forwarding agent between TeKi Client
and TeKi Server. This forwarding agent must provide open sockets
accordingly and provide the socket information to TeKi Server and TeKi
Client on command startup.

#### Session Take-Over

TeKi Server accepts multiple incoming connections on its Control Socket.
If there is an active connection and a new connection successfully
authenticates, the existing connection gets immediately terminated.

This is part of "glitch resilience" can be limited to only accept one
connection and never return, however.

### Data Channels

For all types of data channels, it is crucial that the used 3rd party
application listening on the server-side end of the channel has a low
memory footprint and is really light and resilient.

Imagine a terminal server solution where each user runs its own   
lighttpd streaming server and its own tinyproxy instance. We don't want
those components to go up in CPU and/or I/O consumption, at all.

All data channels (same as the control channel) must be persistent
forwarding channels. So again, we don't wait for any sort of reply on
these channels.

#### Streaming

For data streams (like a video or audio stream), one requirement is that
big chunks of data have to be "searchable" without retrieving the whole
file content. For video (and audio) streaming position seeking throughout
big files must be possible without retrieving the complete video (or
audio) file.

Another obvious requirement is: streaming performance must be as best as
possible. The best approach for streaming optimization is continuously
reconsidered. The exact approaches chosen in certain Telekinesis
versions, will always be documented with that specific Telekinesis
release.

The internal plumbing of the data streaming part is constantly evolving.
First lock down of this plumbing is expected with the first official
Telekinesis release in summer 2017.

The principles of streaming performance optimizations are rather simple,
but the required plumbing underneath is beyond the scope the document.

Data stream bandwidth can be limited in Telekinesis on a per user, a per
session and a per application basis.

#### http-Proxying

For webproxying, no special requirements exist. However, there are
actually two use cases:

* Media streaming from "the web" for some TeKi-aware mediaplayer
* Retrieving website data from "normal" websites for a TeKi-aware webbrowser application.

When web-proxying into a TeKi-aware webbrowser service (the client side
part), the majority of transmitted data consists of many (little) data
packets, originating from irregular issued HTTP(S) requests (i.e. user
clicks). Most packets are incoming packets anyway. 

However, the web proxying code in Telekinesis is also capable of handling
big chunks of (mostly) incoming data smoothly. A TeKi-aware mediaplayer
nowadays must be able to play file content and webcontent alike. So here,
the web-proxying component must deliver media data smoothly, as if being
directly connected to the content provider.

Web proxying bandwidth can be limited in Telekinesis on a per user, a per
session or a per application basis.

Same as with data streaming through Telekinesis, the internal plumbing of
the web proxying part is constantly evolving. First lock down of this
plumbing is expected with the first official Telekinesis release in
summer 2017.

### Environment Variables

* TEKI_AUTHTOKEN - Authentication token for authenticating TeKi Client against TeKi Server. This token has to be fetched from an already launched TeKi Server to the client machine by other means than Telekinesis. Once fetched, the TEKI_AUTHTOKEN environment variable has to be set to the fetched authentication token string and exported into TeKi Client's environment.

* TEKI_SESSION_ID - A unique ID string representing the server side and client side session. With the TEKI_SESSION_ID you can match running TeKi Client and Server instances, if needed.

* TEKI_CONTEXT_ID - A unique ID string representing the context that TeKi Server and TeKi Client are running in. This normally is a session ID string handed over from the remote desktop solution utilizing Telekinesis.

* TEKI_RUNTIME_DIR - Base path where all Telekinesis related files and  folders are created during runtime. On Unix hosts this variable points  to ``$XDG_RUNTIME_DIR/telekinesis``. If you override the default, always point this runtime dir to a local file system (i.e. in most cases: not inside $HOME).

*  TEKI_LOG_DIR - Normally, TeKi does not write any log files. File logging is a performance killer to Telekinesis. In debug mode, you can use this environment variable to specify, where debugging log files should be written to. This defaults to $HOME/.telekinesis/<session>/logs/.

* TEKI_CONTROL_SOCKET - Path to server-side Unix domain socket file where TeKi Client instances can throw their connection and authentication attempts at.

* TEKI_DATASTREAM_SOCKET - On demand data stream socket where Telekinesis's per-user lighttpd daemon listens on. The socket is normally off and only lights up on request.

* TEKI_WEBPROXY_SOCKET - On web proxying stream socket where Telekinesis's per-user tinyproxy daemon listens on. The socket is normally off and only lights up on request.

* TEKI_APPLICATION_SOCKET - Exported to TeKi-aware applications. Path to TeKi Server side socket file used by TeKi-aware applications when finding out on how to connect to TeKi Server.

* TEKI_SERVICE_SOCKET - Exported to TeKi-aware services. Path to TeKi Client side socket file used by TeKi-aware services when finding out on how to connect to the TeKi Client.

* likely more to come...

### Telekinesis Server

Functionalties:

* Provide initial authentication token for TeKi Client
* Handle incoming authentication request(s) from TeKi Client
* Handle session take overs
* Monitor available TeKi-aware applications, negotiate compatibility on TeKi Server initialization
* Fire up lighttpd and/or tinyproxy on request on a per-user basis and with individual configurations.
* Multiplex control channel data.
* Monitor remote (desktop) session states and react accordingly.
* Sync server side and client side GUI states, if needed.
* Monitor connection latency and report back to TeKi-aware applications.
* Clean up after TeKi-aware applications have crashed unexpectedly.
* probably more to come...

#### Command Usage

```
SYNOPSIS

  teki-server
        --ctrl-listen <sockaddr>          \
        [--datastream-listen <sockaddr>]  \
        [--webproxy-listen <sockaddr>]    \
        [...]
```

#### Files and Folders

##### At Runtime

TeKi Server creates these runtime files and folder underneath the path
found in environment variabl $TEKI_RUNTIME_DIR:

```
  <TRD>/<teki_session_id>/tokens/
  <TRD>/<teki_session_id>/tokens/auth
```

The authentication token file will get renewed once a TeKi Client has
authenticated against TeKi Server. The renewed authentication token has
to be passed to TeKi Server from the next connecting TeKi Client (e.g.
when resuming a remote (desktop) session).

```
  <TRD>/<teki_session_id>/sockets/
  <TRD>/<teki_session_id>/sockets/ctrl.sock
  <TRD>/<teki_session_id>/sockets/datastream.sock
  <TRD>/<teki_session_id>/sockets/webproxy.sock
  <TRD>/<teki_session_id>/sockets/applications.sock
```

All above sockets are listening sockets created by TeKi Server.

```
  <TRD>/<teki_session_id>/pids/
  <TRD>/<teki_session_id>/pids/teki-server.pid
  <TRD>/<teki_session_id>/pids/httpd.pid
  <TRD>/<teki_session_id>/pids/proxy.pid
  ...
```

When Telekinesis runs on top of the Arctica Framework (with debugging
flag enabled!), the complete stack of application and service related
processes can be monitored and catalogued in real time (using Arctica
BugOUT). Telekinesis in "standalone" mode only "takes notes" of parent
process PIDs. With Arctica Framework underneath, Telekinesis better
supports cleaning up after applications and services in cases when those
components crash unexpectedly (which, of course, they should not).

##### Installation Paths

TeKi-aware applications are usually normal executables, probably best
shipped in /usr/bin/ or /usr/local/bin/. Don't forget providing a
.desktop file for that application, if it is a GUI application that the
user shall start via the remote desktop environment's application menu.

The TeKi-aware (GUI) applications either uses the Telekinesis Application
API directly or they call a helper script for setting up communication
between itself and its corresponding service on the TeKi Client side. If
such a helper tool exists, please place it into here:

```
  /usr/[local/]lib/telekinesis/applications/<appname>/teki-app_<appname>
```

Every TeKi-aware application must make itself "known" to TeKi Server by
placing a JSON based info file at

```
  /usr/share/telekinesis/applications/<appname>/info
```

Syntax of the "info" file provided by TeKi-aware applications:

```
JSON { ... }
```

The syntax of the "info" file is still under development and will be
locked down with the first major and official Telekinesis release
(expected in summer 2017).

### Telekinesis Client

* Authenticate against TeKi Server
* Monitor available TeKi-aware services, negotiate compatibility on TeKi Client initialization
* Fire up TeKi-aware services on server side request and manage those running services.
* Multiplex control channel data.
* Monitor remote desktop/application client states and react accordingly.
* Sync server side and client side GUI states, if needed.
* Monitor connection latency and report back to TeKi-aware services.
* Clean up after TeKi-aware services have crashed unexpectedly.

#### Command Usage

```
SYNOPSIS

  teki-client
        --ctrl-connect <sockaddr>          \
        [--datastream-connect <sockaddr>]  \
        [--proxy-connect <sockaddr>]       \
        ...
```

#### Files and Folders

##### At Runtime

TeKi Server creates these runtime files and folder underneath the path
found in environment variabl $TEKI_RUNTIME_DIR:

```
  <TRD>/<teki_session_id>/sockets/ctrl.sock
  <TRD>/<teki_session_id>/sockets/datastream.sock
  <TRD>/<teki_session_id>/sockets/webproxy.sock
```

If TeKi Client and Server run on the same machine (remote session from
localhost to localhost), then those three files above are there, because
TeKi Server created them.

If TeKi Client and Server run on different machines, then the above three
sockets have to be forwarded from the given location to the same file
location on the remote machine running TeKi Server. TeKi Client will
attempt connecting to those three socket files.

If the TeKi Client platform does not support Unix domain sockets, then
those three files are text files, containing information about localhost
ports where the actual socket is listening (file format is JSON).

```
  <TRD>/<teki_session_id>/sockets/services.sock

  <TRD>/<teki_session_id>/pids/
  <TRD>/<teki_session_id>/pids/teki-client.pid
  <TRD>/<teki_session_id>/pids/services/<name1>.pid
  <TRD>/<teki_session_id>/pids/services/<name2>.pid
  ...
  <TRD>/<teki_session_id>/pids/services/<nameN>.pid
  ...
```

##### Installation Paths

TeKi-aware services are special executables not supposed for running
standalone. So they should be placed into

```
  /usr/[local/]lib/telekinesis/services/<servicename>/teki-service_<service-name>
```

Every TeKi-aware service must make itself "known" to TeKi Client by
placing a JSON based info file at

```
  /usr/[local/]share/telekinesis/services/<appname>/info
```

See above for the exact syntax of that "info" file.

## Control Channel IPC

The Control Channel's IPC is not subject to this document. It is under
continuous development and will not be formally "locked down" before
summer 2017. The exact IPC protocol is published with every Telekinesis
release.

The data format being used for IPC is JSON.
