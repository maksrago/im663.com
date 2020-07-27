+++
title = "16Lynx"
date = 2019-12-01T00:00:00-05:00
tags = ["projects"]
draft = false
+++

**Project Repository:** <https://gitgud.io/663/16Lynx>

**Live Site:** <https://www.16chan.xyz>

16Lynx is the offical frontend for both [16chan.xyz](https://16chan.xyz/) as well as
[formerlychucks.com](https://formerlychucks.com/), this project was initially a fork of the
default frontend provided by the LynxChan projected called
[PenumbraLynx](https://formerlychucks.com/). PenumbraLynx, although extemely functional,
lightweight, and somewhat feature heavy lacked many of the core
features that I wanted to provide for my community, as well as
features that I would consider essentially, such as catalog
sorting.

**16Lynx features several additions to PenumbraLynx, such as:**

-   3 additional themes (Yotsuba, Yotsuba B, and Warosu)
-   Additional embedding support for BitChute and Invidio.
-   A default theme built off of the commonly used 'Tomorrow'
    theme. (This theme is slightly different for Formerly Chuck's as it
    features more of a Simpsons twist).
-   An additional file in `~/static/` that acts as a board directory
    for DashChan which allows for DashChan support.
-   Announcements, nav, and footer information is globally pulled
    from template pages making management of said content extremely
    easy to manage for when new information is to be added.
-   Catalog sorting options by: bump order, last reply, creation date,
    and reply count.
-   Clipboard paste support for several image formats
-   Several utility features:
    -   reclicking floating menu buttons such as Side Catalog,
        Settings, and Watching will result in closing the window).
    -   Additonal top and bottom navigation controls on `board.html`,
        `thread.html`, and `catalog.html`.
    -   Reformatting of `boards.html` and `archive.html`, making
        these pages significantly more readable and usable.

This project is extremely prolific to me, because along with being
tangible (in the sense that it can be seen and used) it acts as a
community for hundreds of daily users. Code that I have written is
being used by people everyday. Alongside this, I also developed
several skills that I hadn't had in the past, specifically pertaining
to web development and collaboration. To list a few:

-   Management of an NGINX server, and maintainance/security/management
    of a service.
-   Adding hidden service support to a website.
-   Management and setup of analytics platforms (in this case [Matomo](https://matomo.org)).
-   Management of several services provided alongside 16chan, such as
    [imageboard.net](https://imageboard.net/), DashChan app support.