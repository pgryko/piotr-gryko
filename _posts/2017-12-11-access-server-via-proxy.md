---
layout:     post
title:      Fun with ssh reverse tunnels
date:       2017-12-11 12:32:18
summary:    How to ssh into a server with a changing ip address 
categories: ssh
thumbnail: jekyll
tags:
 - ssh
 - networking
---



# How to ssh into a server with a changing ip address

Problem: I have a linux machine behind a firewall, with a changing ip address. I need to be able ssh into it 

Solution: Setup a reverse ssh tunnel using autossh and systemd

## Network configuration

We have a server with a dynamic ip address hidden behind a firewall.
Normally if the server's ip address was static, we chould first ssh through
the jumphost
```console
$ ssh user@jumphost.io
```
And then into the server
```console
$ ssh user@serverA
```
Or in one line using proxy command
```console
$ ssh -o ProxyCommand='ssh user@jumphost.io' user@serverA
```
```
             +------------+            +-----------+
             |            |            |           |
             |   Jumphost |            |  Server A |
             |+----------+|            |           |
  Your +---->++----->----+++----SSH+-->+           |
  Machine    |+----------+|            |           |
             +------------+            +-----------+
```
However if the ip address of serverA keeps changing, we won't know what to ssh into
for second ssh jump.

There are two possible solutions:
1) Setup a dynamic dns client (only works if ServerA has an ip address directly accessible from the internet)
2) Setup a reverse proxy through a jumphost (works for servers behind a firewall)

## Reverse proxy through a jumphost
This involves keeping an live ssh connection from Server A to the Jumphost, leaving an open port tunnel available for reverse ssh.

```
             +------------+            +-----------+
             |            |+----SSH+-->|           |
             |   Jumphost |            |  Server A |
             |+----------+|            |           |
  Internet <-++-+PROXY<--++<SSH Tunnel--+          |
             |+----------+|            |           |
             +------------+            +-----------+

```

Any machine that's able to ssh into the jumphost, is able to ssh into the server.

### Manual Configuration

This can be done manually by:

1) Adding an ssh key so that ServerA can ssh into the Jumphost
2) From Server A
```console
$ SSH -NR 10022:127.0.0.1:22 jumphost.io
```
This sets up a port forwaring from port 10022 on the jumphost to port 22 on ServerA.

It is now possible to access ServerA from the jumphost via port 10022, i.e from jumphost
```console
$ ssh -p 10022 user@localhost
```

Will get you into Server A

### Auto-reconnecting the tunnel

The previous tunnel will continue to work untill the connection drops, at which point the tunnel will fail. We can use autossh to keep a connection alive.

From ServerA run:
```console
$ autossh -M 0 -o "ExitOnForwardFailure=yes" -o "ServerAliveInterval 30" -o "ServerAliveCountMax 3" -NR 10022:127.0.0.1:22 jumphost.io
```
The ssh tunnel will now reconnect, should the connection drop.

### Keeping the connection live

The auto ssh script can now be added as a systemd process on serverA - this means that the tunnel is started and mainted when serverA reboots.

1) Create a public/private keypair for root user on serverA and add the public key to authorized users on the jumphost.

2) Create the system file

```console
$ sudo nano /etc/systemd/system/sshproxy.service
```
3) Add the following to it

```code
[Unit]
Description=AutoSSH reverse tunnel service for umphost.io 100022 -> 22
After=network.target

[Service]
Environment="AUTOSSH_GATETIME=0"
ExecStart=/usr/bin/autossh -M 0 -o "ExitOnForwardFailure=yes" -o "ServerAliveInterval 30" -o "ServerAliveCountMax 3" -NR 10022:127.0.0.1:22 my.jumphost.io -i /home/root/.ssh/id_rsa

[Install]
WantedBy=multi-user.target
```
4) Enable the systemd services
```console
$ sudo systemctl daemon-reload
$ systemctl start autossh-jump-rtunnel.service
$ systemctl enable autossh-jump-rtunnel.service
```