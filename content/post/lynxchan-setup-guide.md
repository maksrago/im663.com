+++
title = "How to setup LynxChan 2.2.x with Ubuntu 18.04"
date = 2019-11-02T00:00:00-04:00
tags = ["guide", "ubuntu", "nginx", "lynxchan"]
draft = false
+++

This is a comprehensive guide on setting up the [LynxChan](https://www.gitgud.io/LynxChan/LynxChan) imageboard
engine for the absolute layman, along with various additional
necessities that come with hosting an imageboard. When I was first
initially starting [16chan](https://16chan.xyz/) I found that there were very few guides
that existed at the time, and I feel that this guide can save many
users a tremendous amount of time.

**Please note:** For this guide I will be using [Namecheap](https://www.namecheap.com/) as my
domain registrar, and [DigitalOcean](https://www.digitalocean.com/) as my VPS, the steps used in
setting up your imageboard might vary slightly if you use a
different domain registrar or VPS. A great alternative to
DigitalOcean is [Cockbox](https://cockbox.org/), domains can be purchased with Bitcoin, the
same applies to Namecheap, so if you would like to setup and
imageboard without giving out any personal information it is possible.

**I plan on writing a few additional guides that will cover:**

-   How to setup a libre analytics platform (I like [Matomo](https://matomo.org/) -- A great
    alternative to Google Analytics)
-   Setting up some useful addons with LynxChan
-   How to add app support for your LynxChan site (create a module to
    run with [Dashchan](https://github.com/Mishiranu/Dashchan))
-   Setting up your site to run as a hidden service

    **A few important issues that you should know about LynxChan:**

    -   [Cloudflare](https://www.cloudflare.com/) currently breaks LynxChan. I am quite sure that
        Cloudflare's caching system is responsible for this, but even
        when disabling it I experienced random issues.  It may appear to work initially
        however I've noticed the following problems:

        -   Thread based unique ID's can get randomly assigned to
            several users.
        -   Banning a single user can cause multiple users to get banned.

        If you plan on using a [CDN](https://en.wikipedia.org/wiki/Content%5Fdelivery%5Fnetwork) or some sort of DDoS mitigation service,
        you might want to find an alternative, I haven't tested any others
        thus far.

        -   SSL through LynxChan does not work very well, and it's likely a
            problem with [Node.js HTTP2 support](https://github.com/nodejs/node/issues/29529). I attempted to use the
            documentation provided by LynxChan to setup SSL however it caused
            regular crashes of Node.js causing my site to go down and be
            extremely unreliable. It's for this reason that this guide will
            use an NGINX reverse proxy for SSL functionality.
            -   It should be noted however that some LynxChan based imageboard ([freech.net](https://freech.net/) being the only one that I know of thus far)
                that have been able to successfully run SSL off of Node
                server. I will update this guide when I can successfully get it
                configured and stable.
                -   This issue should no longer be a problem in [LynxChan's 2.3
                    release](http://lynxhub.com/lynxchan/res/1480.html) in which "Opt-in HTTP2" will be added, allowing you
                    to use SSL through Node.js successfully without all the crashes.


## Getting a domain {#getting-a-domain}

Where you purchase your sites domain shouldn't matter, for this
guide I will be using [Namecheap](https://www.namecheap.com/).


### Configuring our DNS settings {#configuring-our-dns-settings}

Upon purchasing your domain, you  will be able to modify various
settings in your **Dashboard**. From there you will want to click
the **Manage** button.

{{< figure src="https://i.imgur.com/bKmGoFb.png" >}}

Under the **Nameservers** section we need to select the dropdown,
and select **Custom DNS**. We are going to need to input the
following three nameservers for DigitalOcean:

-   `ns1.digitalocean.com`
-   `ns2.digitalocean.com`
-   `ns3.digitalocean.com`

    {{< figure src="https://i.imgur.com/mtKTxP3.png" >}}


## Getting a VPS {#getting-a-vps}

Where you purchase your VPS shouldn't matter, for this
guide I will be using [DigitalOcean](https://www.digitalocean.com/), if you use a different VPS or
want to host LynxChan locally please make sure that your machine
is running Ubuntu 18.04.

**If privacy is a serious concern for you**: I recommend instead
renting a server from [Cockbox](https://cockbox.org/) as they allow registration and
payment through Bitcoin, thus no personal information is required
in order to get your site up and running.

You can sign up using this link:
<https://try.digitalocean.com/performance/> to get $50 worth of
free credit for your first month.

Upon creating and confirming your new account, we are going to
create a new Droplet.

{{< figure src="https://i.imgur.com/WJvJSWj.png" >}}

These are the settings that I used, and the $5 a month tier should
suffice for a small imageboard. The only setting that matters here
is that we use Ubuntu 18.04.

{{< figure src="https://i.imgur.com/wIQIRYS.png" >}}

You can select the server location that works best for you.

{{< figure src="https://i.imgur.com/ZytJ1OU.png" >}}

Finally, I highly recommend enabling automatic backups, just in
case MongoDB breaks (which it can occasionaly). However, it is
optional.

{{< figure src="https://i.imgur.com/1osqIrp.png" >}}


### SSHing into our VPS {#sshing-into-our-vps}

It will take a moment to create our droplet, but after it's done
we can enter our control panel.

{{< figure src="https://i.imgur.com/0DfAYSP.png" >}}

The IP address listed in our droplet's control panel is the IP
address we will put into our SSH client. We still, however, need
to get the password for our VPS. From the control panel we can
click on **Access** to request a password to be emailed to us.

{{< figure src="https://i.imgur.com/LEKGFsv.png" >}}

{{< figure src="https://i.imgur.com/EBewlsb.png" >}}

You should receive an email from DigitalOcean shortly with the
droplets credentials.

{{< figure src="https://i.imgur.com/CGfx4r9.png" >}}

Once we get our password we can finally SSH into our DigitalOcean
droplet.

If you're on Windows I suggest using [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/) or a similar
program to connect to your server.

On \*NIX operating systems we can simply type

```bash
ssh root@yourserverip
```

In my case, the command would look like this.

```bash
ssh root@206.189.200.133
```

Upon successfully connecting to your droplet you will be prompted
for a password, you can copy the password emailed to you from
DigitalOcean and paste it into your terminal, and afterwards you
will be prompted to change your password to something
else.

Before we start installing LynxChan on our server we need to
setup our DNS settings to work properly with the domain that we purchased.


### Configuring our DNS settings on DigitalOcean {#configuring-our-dns-settings-on-digitalocean}

We are going to need to setup our DNS settings in DigitalOcean
now.

{{< figure src="https://i.imgur.com/6NVFaWY.png" >}}

Here we can add our domain that we purchased earlier.

{{< figure src="https://i.imgur.com/FTiWD3x.png" >}}

Upon adding our domain, DigitalOcean will generate the `NS` records
for us, we just need to add two records.

We are going to add an `@` A record.

{{< figure src="https://i.imgur.com/TRviOOf.png" >}}

And we are going to add a `www` A record.

{{< figure src="https://i.imgur.com/grGKsen.png" >}}

This will allow users to access our site from both typing
yourdomain.com and www.yourdomain.com.

We are done configuring DNS settings, we can finally move onto
setting up LynxChan on our DigitalOcean droplet.


## Setting up LynxChan {#setting-up-lynxchan}

We can finally start installing LynxChan on our VPS!

In order to be able to download and get LynxChan running properly
we're going to have to first download a few packages.

According to the LynxChan [Readme.md](https://gitgud.io/LynxChan/LynxChan/blob/master/Readme.md) we are going to need to
following packages

> -   [Node.js](http://nodejs.org) 12.x, I suggest installing from source code. DO NOT build the master's HEAD.
> -   [MongoDB](https://www.mongodb.org/) 4.0.x.
> -   [UnZip](http://www.info-zip.org) 6.00, this is probably already included in your distro, though.
> -   [cUrl](http://curl.haxx.se) 7.29.0, this is usually included too.
> -   [ImageMagick](http://www.imagemagick.org/script/index.php) 6.7.8-9
> -   [A front-end](https://gitgud.io/LynxChan/PenumbraLynx) that must either be placed on the \`src/fe\` directory or have it's absolute path set on the general.json file. Read the readme.md on src/be for more information about how to configure the path for the front-end.
> -   [ffmpeg](https://www.ffmpeg.org/) (Optional) 4.1 if mediaThumb setting is enabled. Requires zlib-devel on centOS to work properly when compiled from source.
> -   [Sendmail](https://www.proofpoint.com/us/open-source-email-solution) (Optional) 8.14.7 if the option to send e-mails through sendmail is enabled.
> -   [file](http://www.darwinsys.com/file/) (Optional) 5.11 if the option to validate upload mimetypes is enabled.
> -   [exiftool](https://www.sno.phy.queensu.ca/~phil/exiftool/) (Optional) 11.01 if the option to strip exif data is enabled.


### Updating our system {#updating-our-system}

We're going to start off with updating our system to make sure we
are working with the most updated packages that the system will
provide us.

```bash
sudo apt-get update
sudo apt-get upgrade
```

Afterwards we are going to reboot the system in an effort to
syncronize those packages.

```bash
sudo reboot
```


### Installing the correct version of Node.js {#installing-the-correct-version-of-node-dot-js}

We are going to start off with install Node.js, **please note** the
version provided within the existing repositories on Ubuntu is
usually not the correct version and usually causes the LynxChan
engine to break.

For that reason we are going to install Node.js through the
official Node APT repository (you can also compile a version of
Node.js that is the correct version, but the version provided by
the Node APT will suffice).

```bash
sudo apt -y install curl dirmngr apt-transport-https lsb-release ca-certificates
curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -
sudo apt -y install nodejs
```

Afterwards you can check your version of Node.js with:

```bash
node --version
```

Which should output something like this:

```bash
v12.10.0
```


### Installing the correct version of MongoDB {#installing-the-correct-version-of-mongodb}

Similarly to Node.js, usually the MongoDB package that is hosted
by default in the Ubuntu repositories is outdated and will cause
LynxChan to break, so we are going to install the correct version
from the MongoDB APT repository.

Start off by importing the GPK key for the MongoDB apt repository.

```bash
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 9DA31620334BD75D9DCB49F368818C72E52529D4
```

Next we are going to add the MongoDB APT repository to `/etc/apt/sources.list.d/mongodb.list`

```bash
echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu bionic/mongodb-org/4.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb.list
```

Next, we can install MongoDB.

```bash
sudo apt-get update
sudo apt-get install mongodb-org
```

Afterwards, you will want to enable MongoDB to run as a service
with:

```bash
sudo systemctl enable mongod
sudo systemctl start mongod
```

Finally, verify your version of MongoDB with:

```bash
mongod --version
```

You should see an Output similar to this:

```bash
db version v4.0.1
git version: 54f1582fc6eb01de4d4c42f26fc133e623f065fb
OpenSSL version: OpenSSL 1.1.0h  27 Mar 2018
allocator: tcmalloc
modules: none
build environment:
distmod: ubuntu1804
distarch: x86_64
target_arch: x86_64
```

So long as your version is some variation of `4.0.x` everything
will work fine.


### Installing the additional packages we need {#installing-the-additional-packages-we-need}

Luckily the remaining packages that we need, we can grab from the
Ubuntu repositories since they are the correct version.

```bash
sudo apt-get install git unzip imagemagick ffmpeg file
```

**Please Note:** we will not be downloading or setting up the
optional `Sendmail` and `exiftool` packages, if you need help
with setting up these packages, and their functionality I
recommend asking StephenLynx on IRC in the #lynxchan channel on
the Rizon network.


### Cloning the LynxChan repository and running our first setup {#cloning-the-lynxchan-repository-and-running-our-first-setup}

Finally we can clone, and setup LynxChan, start by cloning [this](https://gitgud.io/LynxChan/LynxChan)
repository:

```bash
git clone https://gitgud.io/LynxChan/LynxChan
```

Afterwards we can enter the directory generated by cloning the
repository, we are going to be working in the `aux` directory.

```bash
cd LynxChan/aux/
```

Next we install LynxChan, we need to run the `setup.sh` script.

```bash
./setup.sh
```

Upon running this script you will be prompted with several yes or
no prompts, you can answer `y` to all of the prompts provided. The
installation process should take a few minutes.

In order to be able to LynxChan as a service we are going to have
to create a new user by the name of `node`.

```bash
sudo adduser node
```

The only mandatory information that you need to fill out for this
user is the password, use something secure.

Now we are going to run `root-setup.sh`, this will softlink
LynxChan and allow us to run it by typing `lynxchan`, as well as
enabling LynxChan to run as a service.

```bash
sudo ./root-setup.sh
```

You will be given two prompts:

```bash
Do you wish to install the command lynxchan for all users using a soft-link? (y,n)
```

Answer yes, this will allow us to run `lynxchan` as a command
from our terminal.

```bash
Do you wish to install a init script? Requires install as a command and an
user called node on the system to run the engine, so it also must have permissions
on the engine files. (systemd, upstart, openrc, blank for none)
```

For this prompt you should type `systemd`. This will allow us to
run LynxChan as a service.

**Optional:** You can enable LynxChan to boot on startup by running
the following command:

```bash
sudo systemctl enable lynxchan
```

Finally you can add `node` to be a sudoer, with:

```bash
sudo adduser node sudo
```

And reboot your server simply by typing `reboot`.

Afterwards, we can SSH into our server as the `node` user by
typing:

```bash
ssh node@yourserverip
```

Once you have connected to your server, you simply need to run
this command in order to allow you to run the LynxChan service
through this user:

```bash
sudo setcap 'cap_net_bind_service=+ep' `which node`
```

Now you can run all of the LynxChan service commands from your
`node` user if you so desire.

For the next steps we will need to SSH back into our root account.

Awesome! If everything was installed properly you can run the
`lynxchan` command in your terminal, to which you should see
something like this:

```bash
Worker 1 booted at Sat, 26 Oct 2019 05:42:41 GMT
```

You can now view your imageboard on the IP address of your VPS
(trailed with `:8080`) or if you installed it locally you can
access it at `localhost:8080`.

Terminate the LynxChan script with `ctrl` + `c`.

Next we are going to need to generate a Root user for us to
customize and change various settings in the administrative control
panel. We can generate a user by using this command:

You should change `username` and `password` to values that you
plan on using.

```bash
lynxchan -ca -l username -p password -gr 0
```

With this command you can login at
`http://yoursite.com/login.html`.


### Setting up your sites favicon {#setting-up-your-sites-favicon}

**Please note:** Changing your sites favicon is extremely annoying
if you don't have a background in MongoDB, you can replace the
favicon in your LynxChan directory, located at
`~/Lynxchan/src/fe/static/favicon.ico`, after successfully
replacing this file with the favicon you would like to appear on
your site, we can run:

```bash
mongofiles -h localhost -d lynxchan -l path/to/your/favicon.ico put /favicon.ico
```


## NGINX reverse proxy and SSL setup {#nginx-reverse-proxy-and-ssl-setup}


### Installing a stable version of NGINX and Certbot for LetsEncrypt SSL {#installing-a-stable-version-of-nginx-and-certbot-for-letsencrypt-ssl}

In order to get the latest stable version of NGINX we need to add the
NGINX stable repository:

```bash
sudo add-apt-repository ppa:nginx/stable
sudo apt-get update
sudo apt-get install nginx
```

Afterwards, in order to get SSL setup we are going to need to
install `certbot` and `python-certbot-nginx` with:

```bash
sudo add-apt-repository ppa:certbot/certbot
sudo apt-get update
sudo apt-get install certbot python-certbot-nginx
```


### Generating some LetsEncrypt certificates for our website {#generating-some-letsencrypt-certificates-for-our-website}

LetsEncrypt (i.e. `certbot`) will allow us to generate
certificates that will enable SSL for our userbase.

We are going to run the following command.

```bash
sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com
```

You will be given the following prompts:

```bash
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Plugins selected: Authenticator nginx, Installer nginx
Enter email address (used for urgent renewal and security notices) (Enter 'c' to cancel):
```

Here you can simply enter your email.

```bash
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Please read the Terms of Service at
https://letsencrypt.org/documents/LE-SA-v1.2-November-15-2017.pdf. You must
agree in order to register with the ACME server at
https://acme-v02.api.letsencrypt.org/directory
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
(A)gree/(C)ancel:
```

Enter `A` to agree.

```bash
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Would you be willing to share your email address with the Electronic Frontier
Foundation, a founding partner of the Lets Encrypt project and the non-profit
organization that develops Certbot? We'd like to send you email about our work
encrypting the web, EFF news, campaigns, and ways to support digital freedom.
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
(Y)es/(N)o:
```

This one is up to your preference. Afterwards, certbot will run a
verification, and give you one final prompt.

```bash
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
1: No redirect - Make no further changes to the webserver configuration.
2: Redirect - Make all requests redirect to secure HTTPS access. Choose this for
new sites, or if you're confident your site works on HTTPS. You can undo this
change by editing your web server's configuration.
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Select the appropriate number [1-2] then [enter] (press 'c' to cancel):
```

I strongly recommend enabling SSL by default with
`2`. Afterwards, your certificates should be successfully generated.

Upon successfully generating our certificates will usually be generated
in `/etc/letsencrypt/live/yourdomain.com/`

I recommend running:

```bash
ls /etc/letsencrypt/live/
```

To which you should see the output of:

```bash
yoursite.com  README
```

We are going to need to know the exact location name of our
LetsEncrypt site directory for the next section.


### Configuring our certificates with our NGINX configuration {#configuring-our-certificates-with-our-nginx-configuration}

Next we are going setup our NGINX reverse proxy and point to our
LetsEncrypt certificates.

We can start by opening our default NGINX configuration file,
located at `/etc/nginx/conf.d/default.conf` with our preferred
text editor.

You can replace everything with the file with the following
configuration:

```nginx
server {
if ($host = www.yourdomain.com) {
return 301 https://$host$request_uri;
}
if ($host = yourdomain.com) {
return 301 https://$host$request_uri;
}

listen 80;
server_name yourdomain.com www.yourdomain.com;
return 404;
}

server {
listen 443 ssl;
server_name yourdomain.com www.yourdomain.com;

location / {
proxy_pass http://localhost:8080;
proxy_set_header Host $host;
proxy_set_header X-Forwarded-For $remote_addr;
client_max_body_size 50M; # max file size for users to upload
}

ssl_certificate /etc/letsencrypt/live/yourdomain.com/fullchain.pem; # managed by Certbot
ssl_certificate_key /etc/letsencrypt/live/yourdomain.com/privkey.pem;
# managed by Certbot

}
```

We can test our configuration by running:

```bash
service nginx configtest
```

If everything was done correctly you should see:

```bash
* Testing nginx configuration                                           [ OK ]
```

Now we can start our newly setup NGINX reverse proxy with

```bash
service nginx start
```

Now when start up LynxChan by simply typing `lynxchan` in our
terminal we can access our site through our domain, and see that
it has a green lock indicating that SSL has been configured.

Congratulations your LynxChan imageboard is now successfully setup!


## Setting user upload size limit {#setting-user-upload-size-limit}

From experience with assisting various new administrators, changing
the upload `Maximum size for requests (MB)` to values that exceed 8MB
may cause users to recieve various errors about their file size
uploads.

This setting can be found at `https://yourdomain.com/globalSettings.js`.

{{< figure src="https://i.imgur.com/E0fcRfW.png" >}}

In order to properly fix this, we are going to have to set a
parameter in our NGINX configuration files.

In `/etc/nginx/conf.d/default.conf`, we need to add the parameter
`client_max_body_size 50M` in the `location` block (you can change 50 to whatever value you
set in your global settings).

It should look something like this:

```bash
location / {
    proxy_pass http://localhost:8080;
    proxy_set_header Host $host;
    proxy_set_header X-Forwarded-For $remote_addr;
    client_max_body_size 50M; #Just add this line (Change the value to match the one in global settings)
}
```

Afterwards, we will also need to set the same parameter up in our
`/etc/nginx/nginx.conf` file under the `http` block. Just add
`client_max_body_size 50M;`, and save your configuration
file. Finally, you can restart you NGINX server with `service
nginx restart` and you should not experience any warnings
regarding file upload sizes.


## Additional questions? {#additional-questions}

If you have any questions regarding this guide or LynxChan in general
you can post in [this](https://16chan.xyz/meta/res/917.html) thread on 16chan, or you can email me at [admin@16chan.xyz](mailto:admin@16chan.xyz).