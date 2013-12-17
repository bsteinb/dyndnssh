# dyndnssh

dyndnssh does [dynamic dns](http://en.wikipedia.org/wiki/Dynamic_DNS) updates using SSH for authentication and tinydns to serve DNS records.

## Installation

Create a dedicated user for dyndnssh:

    # groupadd dyndnssh
    # useradd -m -g dyndnssh dyndnssh

Install dyndnssh into the home directory:

    # cd ~dyndnssh
    # git clone --no-checkout https://github.com/bsteinb/dyndnssh.git tmp
    # mv tmp/.git . && rm -rf tmp
    # git reset --hard HEAD
    # su dyndnssh -c ./dyndnssh-init

Add the following to your sshd_config:

    PermitUserEnvironment yes
    
    Match User dyndnssh
        AllowTcpForwarding no
        X11Forwarding no
        PermitTunnel no
        GatewayPorts no
        AllowAgentForwarding no

Change the login shell for dyndnssh:
    
    # usermod -s ~dyndnssh/dyndnssh-shell dyndnssh

## Configuration

### Adding dyndnssh accounts

To add an account for the public key in id.pub that can only update the hostnames HOSTNAME1 HOSTNAME2 …:

    # cd ~dyndnssh
    # ./dyndnssh-add HOSTNAME1 [HOSTNAME2 …] < id.pub

To add an unrestricted account that can update any hostname:

    # cd ~dyndnssh
    # ./dyndnssh-add < id.pub

### Getting updated hostnames into the DNS server

TODO

## Updating hostnames

On the client machine simply run

    $ ssh dyndnssh@server HOSTNAME [ADDRESS]
    
to update the DNS entry for HOSTNAME to point to ADDRESS or - if ADDRESS is omitted - the address which the SSH connection was made from. ssh's arguments -4 and -6 can be used for great justice.
