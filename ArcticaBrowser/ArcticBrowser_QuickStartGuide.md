# Arctica Browser - Quick Start Guide
  
The Arctica Project's Web Browser is a browser designed for running in
remote sessions while bringing you local performance while viewing web
content.

## Concept

## Quick Test Installation

  1. Set up two virtual machines that can communicate with each other
over the network
  1. Install Xubuntu 16.04 or Debian 9 on both machines
  1. Add the Arctica Project's nightly builds package repository to both machines' APT configuration
      ```
      $ sudo editor /etc/apt/sources.list.d/arctica.list
      ```
      Put these lines in that to be created file, if you try this with Ubuntu 16.04:
      ```
      deb http://packages.arctica-project.org/ubuntu-nightly xenial  main
      deb http://packages.arctica-project.org/ubuntu-extras  xenial  main
      ```
      Or these two lines, if you try this with Debian 9:
      ```
      deb http://packages.arctica-project.org/debian-nightly stretch main
      deb http://packages.arctica-project.org/debian-extras  stretch main
      ```
  1. Retrieve the Arctica Project archive's GnuPG public key and add it to APT's trusted archives keyring (on both virtual machines):
      ```
      $ wget -qO - http://packages.arctica-project.org/archive.key | sudo apt-key add -
      ```
  1. Update the list of available packages (again on both virtual machines):
      ```
      $ sudo apt-get update
      ```
  1. Perform these steps only on "The Client" virtual machine:
 
      1. Install the Arctica Test Client (a very simple script, raw and dirty at the time of writing this):
          ```
          $ sudo apt-get install arctica-testclient-nx3
          ```
      1. Install the client-side part of the Arctica Web Browser:
          ```
          $ sudo apt-get install libarctica-browser-overlay-perl
          ```
  1. Perform these steps only on "The Server" virtual machine:
      1. Install the Arctica Test Server (again a very very simple script)
          ```
          $ sudo apt-get install arctica-testserver-nx3
          ```
      1. Install the server-side part of the Arctica Web Browser (currently, this is a very simplistic Perl script):
          ```
          $ sudo apt-get install arctica-testbrowser
          ```
  1. On "The Client" VM now logon graphically, if you haven't done so already.
  1. Launch the script ``arctica-testclient-nx3 <user>@<the-server-vm>``. This should fire up a remote desktop XFCE session, running on "The Server" VM.
  1. In the remote XFCE session, launch a Terminal window. Start Arctica's test browser from there: ``arctica-testbrowser``
  1. You should now see a browser window coming up in the remote session. See the concept section above to understand what's happening on your screen right now. 
