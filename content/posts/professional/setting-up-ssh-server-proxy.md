+++
title = "Setting up an SSH server as a proxy"
date = "2017-09-09"
categorie = "linux"
+++

Recently I setup an SSH server at home. This allows me, besides the obvious, to create an SSH tunnel to encrypt my connections and circumvent restrictions. This is particularly useful on public Wi-Fi networks. These networks may block certain services and other people using the network might be [less](http://lifehacker.com/5906233/do-i-really-need-to-be-that-worried-about-security-when-im-using-public-wi-fi) friendly.

### Installing
Your Linux server most likely already has openssh-server installed. If it hasn't,
installing SSH server is easy. Because I use CentOS the examples will reflect that.
```bash
$ sudo yum -y install openssh-server
```
Start the sshd service:
```bash
$ sudo systemctl start sshd
```
Making sure it starts when the machine boots
```bash
$ sudo systemctl enable sshd
Created symlink from /etc/systemd/system/multi-user.target.wants/sshd.service to /usr/lib/systemd/system/sshd.service.
```
Verifying the above:
```bash
$ systemctl status sshd
● sshd.service - OpenSSH server daemon
   Loaded: loaded (/usr/lib/systemd/system/sshd.service; enabled; vendor preset: enabled)
   Active: active (running) since za 2017-09-09 18:18:35 UTC; 4s ago
     Docs: man:sshd(8)
           man:sshd_config(5)
 Main PID: 2413 (sshd)
   CGroup: /system.slice/sshd.service
           └─2413 /usr/sbin/sshd -D -u0

sep 09 18:18:35 localhost.localdomain systemd[1]: Starting OpenSSH server daemon...
sep 09 18:18:35 localhost.localdomain sshd[2413]: Server listening on 0.0.0.0 port 22.
sep 09 18:18:35 localhost.localdomain sshd[2413]: Server listening on :: port 22.
sep 09 18:18:35 localhost.localdomain systemd[1]: Started OpenSSH server daemon.
```
That's it. As a note, if you previously setup firewall rules to block incoming connections make sure to allow incoming traffic on port 22. I will post the iptables rules that I use in a moment.

### Configure sshd
Before editing `/etc/ssh/sshd_config` we need to generate an SSH key. Github has a good [tutorial](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/) on this. Follow it to setup your SSH key. Use a different key for every machine that is going to connect to your SSH service. Once setup you can copy the key to the server with:
```bash
$ ssh-copy-id wouter@server
```
Replace `server` with the hostname or ipadress of your ssh server and `user` with your user. You will be prompted to login with your password before the key gets copied. When the process is finished try logging in again to verify if it's working. Please refer to the Github [tutorial](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/) if you run in to problems.

There are some configuration settings you can apply that harden your ssh service. Use your favourite editor to edit `/etc/ssh/sshd_config`, I use vi.
```bash
$ sudo vi /etc/ssh/sshd_config
```
Add, edit or uncomment
```
PubkeyAuthentication yes
PasswordAuthentication no
ChallengeResponseAuthentication no
AllowTcpForwarding yes
```
This will *disable* password authentication, one-way-authentication and *enables* public key authentication and traffic forwarding. Keys are easily managed, unsniffable and uncrackable. The same can't be said about passwords.

The next one is up for discussion, allowing root access. Personally I disallow it, but `PermitRootLogin without-password` is a safe setting. This means `root` can only login with public key authentication. As mentioned, I've set it to `PermitRootLogin no`

Another good practice is to use a non-standard port for your ssh service. I haven't done it but this will significantly reduce the amount login tries from scriptkiddies. When choosing a port, have a look at what services [nmap](https://nmap.org/) scans for on a default port scan.
To change to another port edit `/etc/ssh/sshd_config`:
```
Port 12345
```
Save your changes and verify the SSH configuration
```bash
$ sudo sshd -t
```
This should return no errors. When it doesn't you can restart the SSH daemon
```bash
$ sudo systemctl restart sshd
```
The new configuration is now active.
### Setting up iptables
Since I run my ssh service on the default port I am a target for [scriptkiddies](https://en.wikipedia.org/wiki/Script_kiddie) trying to gain access to my machine. And since I want to tunnel my traffic when on public Wi-Fi I can't scope the access down to a few IP's.

CentOS comes with `firwalld`. I don't like `firewalld` so I disable it.
```bash
$ sudo systemctl stop firewalld
$ sudo systemctl disable firewalld
Removed symlink /etc/systemd/system/dbus-org.fedoraproject.FirewallD1.service.
Removed symlink /etc/systemd/system/basic.target.wants/firewalld.service.
$ sudo systemctl mask firewalld
Created symlink from /etc/systemd/system/firewalld.service to /dev/null.
```
Disabling the service deletes the symlink, so the unit file itself is not affected, but the service is not loaded at the next boot, when systemd reads /etc/systemd/system.

However, a disabled service can be loaded, and will be started if a service that depends on it is started; enable and disable only configure auto-start behaviour for units, and the state is easily overridden.

A masked service is one whose unit file is a symlink to /dev/null. This makes it "impossible" to load the service, even if it is required by another, enabled service.

When you mask a service, a symlink is created from /etc/systemd/system to /dev/null, leaving the original unit file elsewhere untouched. When you unmask a service the symlink is deleted.

With `firewalld` out of the way, I can apply my iptables script to setup the firewall.

```bash
#!/bin/bash
iptables -F                     # flush rules
iptables -X                     # delete user added chains
iptables -P FORWARD DROP        # drop forwarding
iptables -P INPUT DROP          # drop incoming
iptables -P OUTPUT ACCEPT       # allow outgoing

# define custom chains
iptables -N ssh_init
iptables -N ssh_throttle

iptables -A INPUT -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT # accept previously allowed traffic
iptables -A INPUT -m conntrack --ctstate INVALID -j DROP # invalid packets get dropped
iptables -A INPUT -i lo -j ACCEPT # allow all traffic on the loopback interface

# ssh init
iptables -A INPUT -s 0/0 -p tcp --dport 22 --syn -j ssh_init
iptables -A ssh_init -m recent --name ssh_input_trap --rcheck --seconds 60 --hitcount 3 --rttl -j DROP
iptables -A ssh_init -m recent --name ssh_input_trap --set -j RETURN

# ssh throttle
iptables -A INPUT -s 0/0 -p tcp --dport 22 --syn -j ssh_throttle
iptables -A ssh_throttle -m connlimit --connlimit-above 3 -j DROP
iptables -A ssh_throttle -m limit --limit 3/m --limit-burst 1 -j ACCEPT

```
To make the rules persistent you need the `iptables-services` package since we disabled `firewalld`.
```bash
$ yum -y install iptables-services
```
After installation you can run
```bash
$ sudo service iptables save
```
>note: If you run `docker` restart the docker service first before saving the rules. This will recreate the docker iptables rules

These rules set the policy to drop all incoming packets except for the SYN packet on port 22. A SYN starts a connection, you'll usually only see it when the connection's being established. When someone sends three SYN packets in a minute, they get dropped. When they make more than three connections in a minute, they will get dropped. When the SYN is accepted a connection is established and the traffic will be allowed.

With these rules in place we can start using our SSH service.

### Setting up an SSH tunnel
```bash
$ ssh -D 3000 -f -C -q -N wouter@miles
```
Let see what this command does.  
`-D 3000` This allocates a socket to listen on port 3000.  
`-f` Requests ssh to go to background just before command execution.    
`-C` Requests compression of all data.  
`-q` Quiet mode.  
`-N` Do not execute a remote command.  
`wouter@miles` user and the server.

With the tunnel up and running you need configure your browser to use it. In `Firefox` go to `preferences > advanced > network` and tick `manual proxy configuration`. Next set the `SOCKS Host` to `localhost` and `port` to `3000`. Also tick the `proxy dns when using socks5` box. `Firefox` is now ready to use the tunnel.
