---
layout: post
title: Why web apps are the future
category: Web
tags: [Web applications]
---

This short article is intended to share some of my thoughts on the future of the web.

It’s obvious that the web has had a revolutionary influence on the way we connect to our information. Without going over a long history of the web’s evolution, let’s see what the web provides us with today:

#OS independence

The web provides an OS independent way of interacting with web applications that seamlessly integrate with cloud storage. Developing something for the web means developing something for anyone with access to the web and browser of their choice. Instead of developing an application for Windows, OS X, and Linux, developing for the web allows users on all of these operating systems to communicate with one version of the app in an interface all devices understand, the browser. This is huge because it pushes more technology and implementation dependencies to the developer thus allowing quality of user experience to be managed in one place, making the result a browser consumable output for the user to interact with. No native stateful application installs provides convenience, and a better way for applications to go easier on device resources.

#User experience rivaling that of native applications

Modern web applications provide us with user experience that’s beginning to rival if not beat that of traditional native desktop applications. Web apps are pushing the limits of what can be done in the browser, turning it into an application platform for sophisticated web apps that provide a tactile UI feedback parallel to that of a native application. This is due in part to new standards including HTML5, modern front-end frameworks, web components, and single page applications to name just a few.

#Functionality rivaling that of native applications

Not only can we access web applications with a competitive user experience more universally than ever, but the applications are getting very functional, and very good. Sophisticated ways of presenting complex information to a user via single page web applications are no doubt changing the way we think about productivity in the web. In fact I happen to be writing the draft of this post in Google Docs. We need not tie ourselves down with OS and device specific implementations of applications that essentially all provide the same functionality! We are at the point where we can take a ton of our work/productivity and push it to web applications.

#Easing into the web

Luckily the web is not forcing us to do anything cold turkey. The myriad of accessibility the web provides does not in any way mean we are done with desktop computers. It simply means that the web is influencing the way we interact with them. Just look at some of the key strategies that Google has made to promote the web on our computers.

The chrome app launcher for one, gives us a desktop-like way to interface with our favorite web applications. This invites more of the web onto the desktop and lets applications registered with the [chrome web store](https://chrome.google.com/webstore/category/apps) be launched from a native client. This allows users to find and interface with your web application even easier.

![chrome app launcher]({{ site.baseurl }}/images/2016-3-22-Why-web-apps-are-the-future/chrome_app_launcher_display.png)

The incredible thing is these are all developed using the same technologies as regular modern web apps, just take a look at the [hello world](https://github.com/GoogleChrome/chrome-app-samples/tree/master/samples/hello-world) sample chrome app! In fact, any existing web application can be made into a chrome app with just a few additions. Chrome apps can also be extended with certain native modules providing unprecedented access to the filesystem and other hardware features such as Bluetooth.

![chrome app hello world]({{ site.baseurl }}/images/2016-3-22-Why-web-apps-are-the-future/chrome_app_on_ubuntu.png)

One step further gives us the chromebook which is aimed to do all of the aforementioned to an even stronger degree.

#What are we missing?

So we can get insanely good features and integration with web applications on our desktops, but what are we missing in the mobile world? From ‘mobile sites’ spawned responsive versions of sophisticated web apps, however we’re not at the point yet where web applications on a mobile browser feel quite as good and snappy as native mobile apps. We were at this point with desktops too believe it or not, and just as we’ve begun to overcome that, we are making great strides in the mobile world. Here are a couple things we’re doing to provide better web app accessibility and integration on mobile devices:

 - You can save a shortcut to a web app as an icon on your homescreen on most mobile operating systems
 - Some mobile operating systems support web app push notifications to allow a website to interact with the phone’s native OS components to provide a much better and reliable notification experience, similar to that of native mobile apps.
 - Frameworks such as [ionic framework](http://ionicframework.com/) allow developers to transform HTML and JavaScript into native mobile applications to provide a more native user experience.
 - Even [chrome apps on mobile (MCA)](https://developer.chrome.com/apps/chrome_apps_on_mobile) allows native web application integration similar to what we see on desktops, on mobile. It is currently in early developer preview as I write (3/22/16). Check out the repo [here](https://github.com/MobileChromeApps/mobile-chrome-apps)
 - Polymer [platinum](https://elements.polymer-project.org/browse?package=platinum-elements) elements give us an easy way to integrate with more of a device’s hardware to provide a better marriage between native features on your device and web applications.
 - Part of developing progressive web apps means focusing on offline-first applications. This is a great way to provide a native-feel to your web applications on any device
 
Check out [@HenrikJoreteg](https://twitter.com/HenrikJoreteg)’s tweet below discussing some big web wins:
 
![henrik tweet]({{ site.baseurl }}/images/2016-3-22-Why-web-apps-are-the-future/henrikpicture.png)
 
In short, [progressive web applications](https://developers.google.com/web/progressive-web-apps) and modern front-end frameworks are delivering huge user experiences improvements in the shape of native feeling web applications available to any platform. This, my friends, is changing the world.
 
#Where is this all going?
 
I picture a world where nearly every device acts as a portal to our webapp-centric lives. Similarly to the concept of chrome web apps, I think we'll begin to see universal support for more native ways that web apps can interface with our devices. Is it too crazy to say that one day mobile apps as we know them will become obsolete? In the long term, I think not. I don't believe current native applications can compete, in the long run, with the light weight flexibility that progressive web apps will give us on any device.

Instead I see technologies like the ionic framework as a step in the right direction. I think where we're at right now is the infacy of web app integration in ALL of our devices. One day we'll
achieve a state where web applications are about as integrated into our devices as they are in something like a chromebook. This will definitely allow us to make devices much cheaper as performance intensive tasks
will be done in the cloud, and then rendered on our devices.