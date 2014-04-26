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
    # ./dyndnssh-conf dyndnssh dyndnssh

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

### Getting updated hostnames into tinydns

Get an instance of [tinydns](http://cr.yp.to/djbdns.html) up and running. Now run

    # cd ~dyndnssh
    # ./dyndnssh-tinydns-conf ROOT DOMAIN

where ROOT is tinydns's data directory and DOMAIN is the DNS suffix your dynamic hostnames will have. The dyndnssh user should be able to use the add-host / add-host6 scripts as well as the Makefile in ROOT to edit the data tinydns uses. The tinydns instance should be configured as an NS for DOMAIN.

### Getting updated hostnames into knot (via DNS Update)

Configure [knot](https://www.knot-dns.cz) to be the authoritative DNS server for your domain and accept DNS Update requests from the host running the dyndnssh service.
Edit the configuration variables near the top of the file `dyndnssh-knot` then put a link in place:

    # cd ~dyndnssh
    # ln -s dyndnssh-knot dyndnssh-bridge

## Updating hostnames

On the client machine simply run

    $ ssh dyndnssh@server HOSTNAME [ADDRESS]

to update the DNS entry for HOSTNAME to point to ADDRESS or - if ADDRESS is omitted - the address which the SSH connection was made from. ssh's arguments -4 and -6 can be used for great justice.

## License

dyndnssh is beerware, see file LICENSE.
