= How to I ssh into a server behind a firewall with a changing ip address=

Problem: I have a machine behind a firewall, with a changing ip address. 

Solution: Reverse ssh tunnel

[Unit]
Description=AutoSSH reverse tunnel service for my.jumphost.io 100022 -> 22
After=network.target

[Service]
Environment="AUTOSSH_GATETIME=0"
ExecStart=/usr/bin/autossh -M 0 -o "ExitOnForwardFailure=yes" -o "ServerAliveInterval 30" -o "ServerAliveCountMax 3" -NR 10022:127.0.0.1:22 my.jumphost.io -i /home/root/.ssh/id_rsa

[Install]
WantedBy=multi-user.target

             +------------+            +-----------+
             |            |+----SSH+-->|           |
             |   Firewall |            |  SRV480   |
             |+----------+|            |           |
  Internet <-++-+PROXY<--++<SSH Tunnel--+          |
             |+----------+|            |           |
             +------------+            +-----------+


$ sudo systemctl daemon-reload
$ systemctl start autossh-jump-rtunnel.service
$ systemctl enable autossh-jump-rtunnel.service
