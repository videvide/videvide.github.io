+++
title = "How To Run Caddy Behind Cloudflare Reverse Proxy With Full TLS🔒"
date = 2025-03-05T20:28:00+01:00
tags = ["caddy", "cloudflare", "tls", "dns", "security", "reverse proxy" "go", "debian"]
categories = ["sysadmin"]
+++
### Steps to take

- Use xcaddy to create a custom Caddy build with the dns.providers.cloudflare module.
- Configure a systemd service for our Caddy build with an environment file.
- Enable Full (Strict) TLS for our domain inside the Cloudflare dashboard.
- Create an API token to read and edit our domains DNS settings.
- Use a Caddyfile to configure Caddy to serve our domain with full TLS.

### Let's get started

You can follow the [official documentation](https://caddyserver.com/docs/build#xcaddy) on how to build Caddy from source using xcaddy, but I'll walk you through how to do it on a fresh debian 12 installation.

First, [let's install go](https://go.dev/doc/install). We download the compressed binary from their site, we then make sure to remove any old installations to prevent messy builds, un-tar the binary into correct directory, and adding it to our path (you could also add it to your ~/.profile).

```bash
wget https://go.dev/dl/go1.24.1.linux-amd64.tar.gz && sudo rm -rf /usr/local/go && sudo tar -C /usr/local -xzf go1.24.1.linux-amd64.tar.gz && export PATH=$PATH:/usr/local/go/bin
```

Verify the installation.

```bash
go version
```

It should output something like this.

```bash
go version go1.24.0 linux/amd64
```

Then [install xcaddy](https://github.com/caddyserver/xcaddy?tab=readme-ov-file#install).

```bash
sudo apt install -y debian-keyring debian-archive-keyring apt-transport-https
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/xcaddy/gpg.key' | sudo gpg --dearmor -o /usr/share/keyrings/caddy-xcaddy-archive-keyring.gpg
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/xcaddy/debian.deb.txt' | sudo tee /etc/apt/sources.list.d/caddy-xcaddy.list
sudo apt update
sudo apt install xcaddy
```

Now, we can build Caddy with the [extension module](https://caddyserver.com/docs/modules/dns.providers.cloudflare).

```bash
xcaddy build --with github.com/caddy-dns/cloudflare
```

You'll end up with two files, the Caddy binary (caddy) and the installation directory (go) where all the installation packages are located. You may remove the installation directory (go), and move the binary (caddy) to /usr/bin which should be on your path.

```bash
sudo rm -rf go && sudo mv caddy /usr/bin
```

Test that it worked.

```bash
caddy version
```

It should output something like this.

```bash
v2.9.1 h1:OEYiZ7DbCzAWVb6TNEkjRcSCRGHVoZsJinoDR/n9oaY=
```

### Let's [set up Caddy as a systemd service.](https://caddyserver.com/docs/running#manual-installation)

Create a group named caddy.

```bash
sudo groupadd --system caddy
```

Create a user named caddy with a writeable home directory.

```bash
sudo useradd --system \
    --gid caddy \
    --create-home \
    --home-dir /var/lib/caddy \
    --shell /usr/sbin/nologin \
    --comment "Caddy web server" \
    caddy
```

**Note that...** The caddy user needs read permission on the Caddyfile we soon will create. If unsure, lookup file permissions on debain (or Linux) to understand how to check it.

We will now [create a systemd service for Caddy](https://caddyserver.com/docs/running#linux-service). We will use the first option from the official documentation, the **caddy.service** systemd unit file together with a **Caddyfile**. [Follow this link for the template caddy.service file](https://github.com/caddyserver/dist/blob/master/init/caddy.service) that we are using below.

We create the caddy.service file at this location.

```bash
/etc/systemd/system/caddy.service   
```

With this template file content.

```vim
# caddy.service
#
# For using Caddy with a config file.
#
# Make sure the ExecStart and ExecReload commands are correct
# for your installation.
#
# See https://caddyserver.com/docs/install for instructions.
#
# WARNING: This service does not use the --resume flag, so if you
# use the API to make changes, they will be overwritten by the
# Caddyfile next time the service is restarted. If you intend to
# use Caddy's API to configure it, add the --resume flag to the
# `caddy run` command or use the caddy-api.service file instead.

[Unit]
Description=Caddy
Documentation=https://caddyserver.com/docs/
After=network.target network-online.target
Requires=network-online.target

[Service]
Type=notify
User=caddy
Group=caddy
ExecStart=/usr/bin/caddy run --environ --config /etc/caddy/Caddyfile
ExecReload=/usr/bin/caddy reload --config /etc/caddy/Caddyfile --force
TimeoutStopSec=5s
LimitNOFILE=1048576
PrivateTmp=true
ProtectSystem=full
AmbientCapabilities=CAP_NET_ADMIN CAP_NET_BIND_SERVICE

[Install]
WantedBy=multi-user.target
```

Then under the [Service] section, we add the [option for an environment file](https://caddyserver.com/docs/running#environment-variables).

```vim
EnvironmentFile=/etc/caddy/.env
```

### Setting up Cloudflare

We now have to create the API token, and configure our domains TLS to Full (Strict).

We visit our [Cloudflare dashboard](https://dash.cloudflare.com/) to create the API token. Navigate to "Manage Account" then "Account API Token" then click "Create Token". Select "Create Custom Token", give it a name, then under permissions, create two permissions, one for "Zone:Zone:Read" and one for "Zone:DNS:Edit". Under Zone Resources select Include and choose your domain. You could also choose to limit the API token to only be used by specific IP addresses. Click "Continue to summary", double check the values, then click "Create Token". Copy the token value and paste it into your /etc/caddy/.env file, like so:

```vim
CF_API_TOKEN=super-secret-cloudflare-tokenvalue
```

Then, we need to visit our domains SSL/TLS section, which we will find by visiting "Account Home", then choosing our domain, and then navigating to SSL/TLS. Click "Configure", then choose "Full (Strict)", and click "Save".

### Final steps

Now we only need to create a Caddyfile and configure it to serve our domain with tls, and of course, start the systemd service.

Let's create the Caddyfile at /etc/caddy/Caddyfile with the environment variable containing our API token and with the cloudflare DNS as dns resolver.

```vim
example.com {
    tls {
        dns cloudflare {env.CF_API_TOKEN}
        resolvers 1.1.1.1
    }
    respond "Hello, world!"
}
```

Save the file, then run the following commands to reload the systemd daemon, and to enable the caddy service.

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now caddy
```

Verify that Caddy is running.

```bash
sudo systemctl status caddy
```

You should see something like this.

```bash
● caddy.service - Caddy
     Loaded: loaded (/etc/systemd/system/caddy.service; enabled; preset: enabled)
     Active: active (running) since Wed 2025-03-05 22:40:57 CET; 1s ago
       Docs: https://caddyserver.com/docs/
   Main PID: 6775 (caddy)
      Tasks: 7 (limit: 1107)
     Memory: 11.0M
        CPU: 65ms
     CGroup: /system.slice/caddy.service
             └─6775 /usr/bin/caddy run --environ --config /etc/caddy/Caddyfile
```

Congratulations, you should now be able to visit your domain served with full TLS!🔒
