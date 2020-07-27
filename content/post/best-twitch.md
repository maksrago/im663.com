+++
title = "The best way to watch Twitch (Chatterino + Streamlink + VLC)"
date = 2020-05-28T00:00:00-04:00
tags = ["guide"]
draft = false
+++

As someone who is interested in a variety of different speedrun games,
and different speedrunners I often find it hard to watch several
livestreams at the same time. In the past used a tool by the name of
Livestreamer in order to pipe livestreams into VLC, and have several
instances of VLC open, however, that left a lot to be desired with the
chat experience, and the project has since been abandoned. Luckily,
since then Streamlink has become an active fork of the no longer
maintained Livestreamer.

Chatterino, an FOSS Twitch chat client enhances this experience
further with a variety of features, the most important of which,
direct piping to Streamlink.

My Twitch viewing experience now often looks like this:

{{< figure src="https://i.imgur.com/xy30LPr.png" >}}

{{< figure src="https://i.imgur.com/ajaXMLm.png" >}}

This is a guide on how to get the best experience out of Twitch,
without having to use the bloated Twitch site.


## Download VLC {#download-vlc}

This step is extremely straight forward, out of the box VLC should be
perfectly configured for your system.

**Download VLC:** <https://www.videolan.org/> and download and install the appropriate version
of VLC for your system.


## Download Streamlink {#download-streamlink}

Streamlink, the tool that will pipe Twitch streams to VLC, is a
command-line tool, and typically would require use of the command
line, however, we are going to use Chatterino in order to invoke
stream piping.

**Download Streamlink:**
  <https://github.com/streamlink/streamlink/releases/latest> (if you're
  on Windows select the .exe) Download and install Streamlink.


## Download and configure Chatterino {#download-and-configure-chatterino}

Chatterino, will in essence act as our front-end for accessing Twitch,
it brings along many features that I will recommend enabling after we
complete the installation process.

**Download Chatterino:** <https://chatterino.com/> and install the
  appropriate version of Chatterino for your system.

We now have all of the essentially components for setting up
Chatterino.

First off (although technically optional), we are going to
need to add a Twitch account, which can be done by clicking on
settings gear, clicking accounts, and by clicking add. Follow the
steps on the login page to get a oauth token that you will then paste
into Chatterino. This can be done for any number of accounts, and you
can quickly switch between accounts.


### Afterwards we can setup Streamlink to work alongside Chatterino {#afterwards-we-can-setup-streamlink-to-work-alongside-chatterino}

In the settings, under `External Tools`, you should specify what your
preferred quality for a stream is, typically if you have a good
connection you can leave it on `Source`.

Finally, for the best experience, under `Additional options`, add the
following flag `--twitch-low-latency`, this will enable the lowest
possible experience for watching Twitch


### Some other very useful settings {#some-other-very-useful-settings}

Next in no particular order are some settings that I find to be
extremely useful (under `General`):

**Interface**

-   `Theme:` -> `Dark`

**Messages**

-   `Alternate background color`
-   `Show deleted messages`

**Emotes**

-   `Animate`
-   `Stack bits`

**Chat title**

-   `Uptime`
-   `Viewer count`
-   `Category`
-   `Title`

**Miscellaneous:**

-   `Show moderation messages`
-   `Bold @usernames`
-   `Show link info when hovering`
-   `Unshorten links`

Finally, you can add a stream by clicking on the `+` button, and typing
the name of the streamer you'd like to watch, you can have dozens of
tabs open, meaning that you can easily switch between various
streams. In order to start the stream, and watch it through VLC, you
will need to click on the three vertical dots by the name of the
stream, there you will see `Open in streamlink`, your VLC should open.

You can also enable the minimal interface on VLC, under `View` >
`Minimal Interface` or by pressing `CTRL + H`.