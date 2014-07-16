---
layout: post
title: Getting Started with Tessel
truncate: 80
---

#### *A programmer’s foray into embedded systems*

I’m a software guy.  I’ve taken the requisite college classes and understand how the programs I write eventually compile down to machine instructions and how they’re executed on the underlying hardware, but the idea of working directly with embedded systems for side projects at home has always been a little daunting.  I haven’t touched C, C++, or similarly low-level languages in several years, and haven’t had much cause in my professional work to look into embedded devices, but learning to work on these types of platforms has always had a kind of mysterious allure for me.

Back in October of 2013, I stumbled upon a crowd-funded project named [Tessel](https://tessel.io/), a microcontroller designed to be controlled wirelessly through the internet using an unconventional platform for embedded devices; JavaScript.

I’m sure hardcore embedded system developers are frothing in outrage, and I’ll not disagree that JavaScript probably isn’t the best language to use in a CPU/memory constrained production environment, but the unconventional platform sounded appealing as a developer with some experience in web development, curious about writing programs that not only interact with users through visual displays, but through direct communication with their environment.  So I decided to give it a shot.

## Getting set up

One of the biggest hassles with embedded devices is getting the environment up and running; the drivers, compiler, programming environment, not to mention getting the individual components and getting them to work with one another.  I decided to take my first crack at working with Tessel on a Windows machine; my desktop gaming computer I use at home.  First step, go to the Tessel site and install the command line utility for working with the system; a simple node.js package

{% highlight sh %}
npm install -g https://builds.tessel.io/cli/tessel-cli-current.tar.gz
{% endhighlight %}

All that’s left is to plug it in an test out the command line utility by ensuring that the software on the Tessel board is up to date.  After a couple of minutes, where Windows fetches the appropriate drivers and installs them automatically, I’m ready to run my first Tessel command:

{% highlight sh %}
tessel update
{% endhighlight %}

Success!  The online tutorial quickly introduces what I suspect is the microcontroller equivalent to “Hello, world” - making some LEDs blink.

## Developing for Tessel

The Tessel consists of a single, base board (which has our “Hello, World” LEDs on it) with four ports for adding in additional Modules, which host a range of other sensors and components designed to interact with the environment.  Most of the components are fairly simple to use: plug them into any port, download the appropriate NPM module, and you’re good to go.

One nice thing to note, the name of each module is printed directly on the board of the component, which corresponds exactly with both the NPM package used to interface with that particular module as well as a named GitHub repository hosting documentation and examples.  For instance, one of the sensors is capable of detecting ambient light and sound (in the form of coarse luminosity and decibel readings) and is called, appropriately, the ambient module.   The name of the component, ambient-attx4, is printed directly on the module, making getting started with the module as easy as

{% highlight sh %}
npm install ambient-attx4
{% endhighlight %}

and clicking on the link to [tessel/ambient-attx4](https://github.com/tessel/ambient-attx4) on GitHub. 

This highlights the biggest advantage to using Tessel.  In about twenty minutes I was able to take timed temperature and humidity readings, setup triggered events at arbitrary light and sound thresholds, and even take a selfie through the tiny camera module, albeit at an embarrassingly poor resolution.  The documentation is extremely consistent, fairly straight to the point, and easy to get started with, using tools and languages I’m already familiar with.

Lastly, the system gets a big boost in usability in the development cycle department.  Code changes are as simple as saving a file and typing tessel run to package up, deploy, and run the code on the device.

## Next steps

A couple of the modules take a little bit of extra work to get started with, because they require interfacing with outside components, including the Bluetooth and Servo motor modules, so I haven’t started playing around with them yet.  I’d also like to get rid of the power cable tethering this little beast to my desktop so I can start making real use of the onboard WiFi feature.  I bought a small [rechargeable USB power supply](http://www.amazon.com/Anker-Ultra-Compact-Lipstick-Sized-ThunderBolt-Blackberry/dp/B005NF5NTK/ref=sr_1_11?ie=UTF8&qid=1377998755&sr=8-11&keywords=usb+battery) to attach to the board, which their website assure me should be more than sufficient for most uses for at least a day, even with consistent WiFi activity.  Over the next few weeks I’ll focus on trying out the different modules and keeping my eye out for better uses than taking bad selfies.

<img alt="Selfie from the Tessel" src="/assets/picture-270674081.jpg" style="">

The experience in general has ignited my interest in this space, a natural bridge from working with data analytics technologies down to the sensors that collect that data.  The explosion of easy to use microcontrollers and barebones PCs over the last fews years, first with the gain in popularity of the Arduino system, then the internet’s fascination with the Raspberry Pi computer, has lead to a wave of innovative and interesting at-home projects.  These platforms, which advertise themselves specifically as prototyping or learning platforms, may not be representative of working with production quality embedded systems, but they provide an easier path for newcomers who don’t have a background working with low-level systems.
