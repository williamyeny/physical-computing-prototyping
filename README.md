# Physical Computing Prototyping Assignment

## Introduction
One thing that I've always wanted was to remotely lock/unlock my dorm room. One of these many IOT solutions is called the **Lockitron**. The **Lockitron Bolt** is the thing to the left:

![Lockitron](https://i.imgur.com/gqgpjeN.jpg)

However, the Lockitron Bolt was initially developed to be just a keyless entry system using Bluetooth. The thing to the right is called the **Lockitron Bridge**, which allows your Lockitron Bolt to connect to the internet and lets you lock/unlock your door from everywhere, making this an IOT device.

## Some observations

An interesting question to propose is "why not integrate WiFi capabilities in the Lockitron Bolt instead of creating two separate devices?" Upon further research, the Lockitron Bolt operates solely over Bluetooth Low Energy. This means the Lockitron Bolt can operate over battery power, while adding a WiFi module may make battery power unviable due to the increased energy consumption. It is feasible to use power from a wall outlet, but connecting something on a door to an outlet is unpractical and ugly at best and dangerous at worst.

## The nitty gritty tech

When I googled the above image of the Lockitron, I noticed the URL of the image: 

![URL](https://i.imgur.com/L5ISmR0.png)

Upon further inspection, the page which contains this image is the official Lockitron website. My intuition is that the Lockitron servers are hosted using **AWS**. However, this could easily be wrong; *a lot of sites use S3 just to host content, and companies often have different servers for their landing/product page and the actual service for the product.*

Moving on, Lockitron has its own app for locking/unlocking doors. Logically, this means Lockitron does have its own servers as opposed to solely using a third party service. This existence of the **[Lockitron API](https://api.lockitron.com/)** further proves this. Let's head to the [api.lockitron.com](https://api.lockitron.com/), since that's where all the physical Lockitron operation is handled, as opposed to the [lockitron.com](https://lockitron.com), which is just the landing page.

At first glance, it looks like any API documentation page, probably generated by an API development tool like [Swagger](https://swagger.io/). There's no clear indication of where it's hosted, at least on the page itself. Let's dive a little deeper and open Chrome Developer Tools. Here's what the "Network" tab looks like:

![network](https://i.imgur.com/kFnsLxp.png)

It seems to be loading the usual resources; fonts, Javascript files and others. Let's click on the actual page request (the first one):

![page](https://i.imgur.com/27xBMYJ.png)

Immediately, there are some things that are of interest to us, particularly `Server:nginx + Phusion Passenger 5.1.2`. A quick Google search of Phusion Passenger takes us to the [official repository](https://github.com/phusion/passenger):

![repo](https://i.imgur.com/sUXdSP2.png)

So now we know that Lockitron's web server **Phusion Passenger and nginx**. We can also narrow down the app server language to either **Ruby, Python or Node.js**.

While knowing the tech behind Lockitron is great, where is it being hosted? A quick look through the Phusion Passenger docs reveals that there seems to be a lot of support for certain hosts.

![docs](https://i.imgur.com/fiS0avU.png)

So we know that it is extremely probable that Lockitron uses **Amazon Web Services, Cloud 66, Digital Ocean** or **Heroku** to host their service.

However, I believe this is as far as I can narrow it down just by looking around. Let's find out what platform Lockitron is hosted on for sure by taking the easy way out and using a [host lookup tool](https://hostadvice.com/tools/whois/#api.lockitron.com):

![do](https://i.imgur.com/oM1wcwM.png)

Huh, that was easy. Now we know for sure that `api.lockitron.com` is hosted on DigitalOcean. Assuming we haven't missed anything, we now can construct a flowchart of the data flow:

![flowchart](https://i.imgur.com/T1JBnPF.png)

## Improvements

I'm going to be honest here; coming from a web developer, this data flow is pretty much perfect. There's minimal third party or outsourced web services in which the Lockitron's core ability depends on, and even if the highly stable DigitalOcean hosting fails, it would be trivial to host it with a different platform. Plus, using an open API allows developers to integrate Lockitron actions in their own applications, which is great.
