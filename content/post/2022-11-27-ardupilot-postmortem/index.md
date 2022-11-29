---
title: 'Ardupilot Postmortem'
url: '/post/ardupilot-postmortem'
type: post
author: scottyob
date: 2022-11-27
tags:
 - rc
 - ardupilot
image: /post/ardupilot-postmortem/crash.jpg

---

***NOTE:  This post is expected to be edited once I have more opinions from the community and get closer to a concrete conclusion of what happened.***

Last week, I took a few extra days off from work to give myself a nice long break over Thanksgiving week.

I spent a lot of time finishing an RC plane we had some replacement parts for. This one was going to be autonomous. I spent a few days soldering the flight controller and electronics, gluing the plane together, measuring and trimming everything perfectly. It was perfectly balanced, perfectly weighted. We finally took it out for its maiden flight today, Sunday November 27.  Sadly, this story ends in pieces.

This post is a postmortem of the incident to make sense of what happened.  I was flying using Ardupilot and had an SD card in recording the data, so, hopefully the "black box" telemetry data will give me a starting point to analyze what went wrong.

### Plane Hardware
* **Plane**: [Bixler 3](https://hobbyking.com/en_us/h-king-bixler-3-glider-1550-pnf.html)
* **Flight Controller**: [Holybro Kakute F7](https://ardupilot.org/copter/docs/common-holybro-kakutef7aio.html)
* **GPS, Compass**: [Beitian BN-880](https://ardupilot.org/copter/docs/common-beitian-gps.html)
* **Ardupilot**: Arduplane V4.3.1

### Tools & Files
* [UAV Logviewer](https://ardupilot.org/dev/docs/common-uavlogviewer.html)
* Ardupilot **[LOG FILE](/post/ardupilot-postmortem/log.bin)**

### Theories & Investigation

The flight controller has a BMP280 barometer to measure the altitude quickly, and a ICM20689 IMU for acceleration and gyro.  There is also a slower-updating GPS, so tbetween these indtruments, I expect fairly acurate altitude and position readings.  The plane behaved erratically twice in the short two-minute flight.  After combing through the available data, I suspect two failures during this flight.  What followx is a timeline of the events of the flight and notes on each incident.

### Events in Flight

#### 1. Bad Altitude Readings Pre-Launch
The first noteworthy item is that the altitude readings from the barometer and accelerometer are bogus.  I seem to be getting more realistic readings from the GPS.  Going forward, any screenshots will be from the GPS altitude.

<video width="100%" loop="true" controls autoplay muted>
    <source src="/post/ardupilot-postmortem/altitude.mp4" />
</video>

![altitude](/post/ardupilot-postmortem/Altitude.jpg)

The altitude readings are super interesting here.  You can tell even before I launch the plane, the GPS altitude readings are between -13m and 0m.  Not sure what this altitude is (above sea level?  Above ground level?? Either way, this flight took place very nearly at sea level.)

More concerning, the barometer altitude is between -10 and -70m in the ~1 minute before launch on the ground.  The calculated AHRS (Attitude Heading Reference System) altitude at somewhat similar, -9 and -80  🫤

#### 2. Launch

The maiden launch itself in manual mode was nominal.  You can hear me saying "Good luck little plane".  It needed more than luck it seems!
<iframe height="315" style="width: 100%; max-width: 560px; display: block; margin: auto" src="https://www.youtube.com/embed/D03_c0LWJhM" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

#### 3. Fly-By-Wire Test

At ~1 minute 22 seconds into the flight, I turned the flight mode from manual to [Fly by wire A](https://ardupilot.org/plane/docs/fbwa-mode.html), which should have had the effect of assisting the plane in straight line.  Looking at the radio stick input, I did not change the heading, but the autopilot sent this into a steep dive at this time.  A near miss where we avoided a crash here.

![FBW](/post/ardupilot-postmortem/fbw.jpg)

##### (BAD) Theory - Reversed Elevator?
![nosedive](/post/ardupilot-postmortem/Nosedive1.jpg)
12:24 is interesting here. If we look at the time we engage the autopilot, we can see the pitch heading down into the ground, before our roll even begins to change.  It ends up to a point where it's almost completely nose down before we save it by switching to manual flight mode.

What's interesting is that the pitch continues to fall, and the AETR elevator gets more and more positive.  Is it possible that we're trying to correct the nose down and instead making it worse?  Could the elevator be reversed?

I think this can however be **disproven** due to the radio sticks playback showing the correct elevator inputs during my climb out and general flying (as you can see below on my launch).
![Disproven](/post/ardupilot-postmortem/disproven.jpg)

### 4. Crash (elevator failure?)

This event makes me believe that I had an elevator failure during the flight. In a panic I "checked" my autopilot settings, and accidentally turned it on.

**Events:**
* Nominal flight approaching turn
* Banks left to enter turn
* Starts pitching down, control toggles are pulling back on the elevator, manual flight mode still engaged
* Plane is not responsive and is still pitching down
* After loosing much altitude, in a panic, RTL is engaged
* Plane starts pitching nose down further once RTL is engaged (reversed elevator theory?) causing the epic crash with almost full speed, full nose down

<video width="100%" controls autoplay muted>
    <source src="/post/ardupilot-postmortem/crash.mp4" />
</video>





### Lessons
* Don't fly with so much throttle.  Mistakes happen a *lot* faster than it could otherwise happen
* Test the FBWA modes on the ground.  Ensure that the elevator, ailerons and rudder is behaving as expected in relationship to pitch, roll movements.
* Be sure to glue in everything!  I'm not convinced a servo could have come loose during this flight, though unlikely considering it to be the elevator that's unresponsive.  Perhaps elevator could have been "stuck" or something?  Loose screw on the servo?  Who knows.
* When packing up a catastrophic crash, take stock of failed components, what servo is for what.  Re-create postmortem to analyze suspect failed components.


