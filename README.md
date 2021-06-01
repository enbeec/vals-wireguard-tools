# vals-wireguard-tools

## About

These scripts are written with Ubuntu and FreeBSD in mind but the latter is much more of a special case. In general though, you should be fine server-wise with any platform that is compatible with `wg-quick` so we can use simple .conf files for all peers. Keep in mind that in Wireguard, a server is basically just another node even if you're like me -- too lazy to mesh it and happy with a hub and spoke. In our case, our server is a node that happens to be up all the times, routing between nodes and (if you feel fancy) providing DNS via `dnsmasq`.

## Workflow

Start on your server (not literally, just with an ssh shell) by installing `wireguard`. If your distribution doesn't create `/etc/wireguard` (`/usr/etc/local/wireguard` on FreeBSD since it separates system packages from non-system packages much more cleanly) create it and make sure it's got secure permissions. If you don't know what that means, stop and do some digging on file permissions on UNIX and come back when you know how to use `chmod` -- you don't want to leave your private keys lying around and I will not be saying anything about hardening beyond **don't leave your keys readable by anyone but root**. Okay.. rant over.

Now generate your server keys. You can do this the `wireguard` way by running `wg genkey | tee privatekey | wg pubkey > publickey` or if you're in a rush and can't quite remember that try slightly misusing `client-keygen`:

```
client-keygen server
mv server/* .
rmdir server
```

From there, client scripts (`set-keepalives` and `client-keygen`) are intended to run from the folder where your clients are stored. A client is a directory that contains a publickey, privatekey and conf file. Use `keygen` to generate the first two and then (for now) write the conf yourself. Here's what that all looks like:

*assume a server located at wg.example.com:5511 routing the network 10.1.2.0/24 named jeff. also a client, steve*

```
$ pwd 
/etc/wireguard
$ mkdir clients
$ cd clients
$ keygen steve
$ cd steve
```

And then create `steve.conf` that looks like:

```
[Interface]
Address = 10.1.2.3
PrivateKey = <insert contents of wireguard/clients/steve/privatekey> 

[Peer]
Endpoint = wg.example.com:5511
PublicKey = <insert contents of wireguard/publickey>
```

And then if you want to add a keepalive of, say, 25 seconds to all your clients (which is just steve):

```
# assuming we are still in wireguard/clients/steve
cd ..
set-keepalives 25
```

And then you remember that you don't wanna add that yet because you want to know when it's making a difference so you'll probably just enable it on the client manually (again, if you're like me):

```
set-keepalives 0
```
