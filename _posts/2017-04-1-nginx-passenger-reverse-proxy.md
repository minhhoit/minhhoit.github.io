---
layout: post
title: Putting Passenger Standalone behind an Nginx or Apache reverse proxy
comments: true
categories:
    - "nginx"
    - "ruby"
tags: [deploy, nginx, ruby, passenger]
---

In some cases, you may want to put Passenger Standalone behind a reverse proxy. The reverse proxy can for example be Nginx, Apache or HAProxy. The reverse proxy listens on port 80 (or any other port you want) and is responsible for accepting HTTP requests. The reverse proxy then determines which Passenger Standalone instance should handle the request, and forwards the request to that instance.

There are two reasons why you may want to have this kind of reverse proxy:

You already have an existing Nginx or Apache installation, but you want to use Passenger Standalone instead of using Passenger's Nginx or Apache integration modes.
You have multiple Passenger Standalone instances, and you want all of them to be accessible on the same port, just with different host names.

## starting all applications
---
Passenger Standalone behind a reverse proxy requires three steps. First, you must start all Passenger Standalone instances that you want. Each one must be listening on a different port, because two applications can't listen on the same port. Suppose that you have two applications, `/webapps/foo` and `/webapps/bar`. Here's how you may start them:
<pre>
<code>
# Start foo on port 4000

$ cd /webapps/foo
$ passenger start --daemonize --address 127.0.0.1 --port 4000

# Start bar on port 4010

$ cd /webapps/bar
$ passenger start --daemonize --address 127.0.0.1 --port 4010
</code>
</pre>
Notice the fact that we pass `--address 127.0.0.1`, which tells Passenger Standalone to only listen for requests that originate from the local machine. This is because the reverse proxy, not Passenger Standalone, is supposed to be responsible for receiving external HTTP requests. In this guide, we are teaching you how to run the reverse proxy on the same machine only, so limiting Passenger Standalone in this manner improves security.

## Step 2: install and configure Nginx

The next step is to install Nginx to do the following:
+ To listen on port 80.
+ To forward requests to either 'foo' or 'bar', depending on the request's HTTP host name.

Here is how you can install Nginx:

| Debian, Ubuntu | <code>sudo apt-get install nginx-extras</code> |
| Red Hat, CentOS, Fedora, ScientificLinux, Amazon Linux | Enable **EPEL**, then run as root: <br/> <code>sudo yum install nginx</code> |
| Mac OS X (Homebrew) | <code>brew install nginx</code> |
| Other operating systems | Please install Nginx from [the Nginx website](http://www.nginx.org/). |

Open the Nginx configuration file:

| Debian, Ubuntu | /etc/nginx/nginx.conf |
| Red Hat, CentOS, Fedora, ScientificLinux, Amazon Linux | /etc/nginx/nginx.conf |
| Mac OS X (Homebrew) | /usr/local/etc/nginx/nginx.conf |
| Other operating systems | It depends on how you installed Nginx, but it's usually `$PREFIX/conf/nginx.conf`, where `$PREFIX` is the prefix you installed Nginx to. |

Add virtual host entries for your applications foo and bar. While making the virtual host entries, you must determine what host names foo and bar should respond to. Let's say that foo should respond to `www.foo.com` and bar should respond to `www.bar.com`. Then the following entries will tell Nginx to listen on port 80, and to handle requests for the domains `www.foo.com` and `www.bar.com` differently.

<pre>
http {
    ...

    # These are some "magic" Nginx configuration options that aid in making
    # WebSockets work properly with Passenger Standalone. Please learn more
    # at http://nginx.org/en/docs/http/websocket.html
    map $http_upgrade $connection_upgrade {
        default upgrade;
        ''      close;
    }

    server {
        listen 80;
        server_name www.foo.com;

        # Tells Nginx to serve static assets from this directory.
        root /webapps/foo/public;

        location / {
            # Tells Nginx to forward all requests for www.foo.com
            # to the Passenger Standalone instance listening on port 4000.
            proxy_pass http://127.0.0.1:4000;

            # These are "magic" Nginx configuration options that
            # should be present in order to make the reverse proxying
            # work properly. Also contains some options that make WebSockets
            # work properly with Passenger Standalone. Please learn more at
            # http://nginx.org/en/docs/http/ngx_http_proxy_module.html
            proxy_http_version 1.1;
            proxy_set_header Host $http_host;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection $connection_upgrade;
            proxy_buffering off;
        }
    }

    # We handle bar in a similar manner.
    server {
        listen 80;
        server_name www.bar.com;

        root /webapps/bar/public;

        location / {
            # bar is listening on port 4010 instead of 4000, we
            # change the URL here.
            proxy_pass http://127.0.0.1:4010;

            proxy_http_version 1.1;
            proxy_set_header Host $http_host;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection $connection_upgrade;
            proxy_buffering off;
        }
    }
}
</pre>

Once you're done editing the Nginx configuration file, restart Nginx:

| Debian, Ubuntu | `sudo /etc/init.d/nginx restart` |
| Red Hat, CentOS, Fedora, ScientificLinux, Amazon Linux | `sudo service nginx restart` |
| Mac OS X (Homebrew) | 1. Run `sudo kill $(cat /usr/local/var/run/nginx.pid)` <br/> 2. If you installed the Nginx launchd plist that Homebrew provides (see `brew info nginx` to learn more), then you don't have to do anything, and launchd will automatically restart Nginx. Otherwise, you have to manually start Nginx again: `sudo /usr/local/bin/nginx`. |
| Other operating systems | It depends on how you installed Nginx, but it's usually as follows: <br/>1. Lookup the PID of the Nginx master process using ps aux. <br/>2. Run sudo kill `<PID>` <br/>3. Start Nginx again: `sudo $PREFIX/sbin/nginx`, where `$PREFIX` is the prefix you installed Nginx to. |

## Step 3: making all apps start at system boot

Once you restart the machine, the reverse proxy will no longer be able to serve 'www.foo.com' or 'www.bar.com' because the Passenger Standalone instances that host them are no longer running. You must therefore configure the system to start Passenger Standalone at system boot.

For example, you can put this in `/etc/rc.local` to make the system start foo and bar at system boot:

<pre>
#!/bin/sh
# If you installed Passenger from tarball, add its `bin` directory to PATH.
#export PATH=/path-to-passenger/bin:$PATH

cd /webapps/foo
passenger start --daemonize --port 4000 --user someusername1

cd /webapps/bar
passenger start --daemonize --port 4010 --user someusername2
</pre>

---

**Reference**
[https://www.phusionpassenger.com/library/deploy/standalone/reverse_proxy.html](https://www.phusionpassenger.com/library/deploy/standalone/reverse_proxy.html)