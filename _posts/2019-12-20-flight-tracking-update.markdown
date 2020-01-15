---
layout: post
title: "Update #1: Flight Tracking"
author: Garrett Woodbridge
date: "2019-12-20 12:14:40 -0600"
tags: "adsb"
---

### Notification Script
Since it is Christmas break, I was able to put together a quick python script that receives a cell number, a carrier, and a message and can send it, all without using a paid api or cell plan.

{% highlight python %}
import smtplib

carriers = {
  'att':  '@mms.att.net',
  'tmobile':  '@tmomail.net',
  'verizon':  '@vtext.com',
  'sprint': '@messaging.sprintpcs.com',
  'boost':  '@myboostmobile.com',
  'cricket':  '@sms.mycricket.com',
  'metropcs': '@mymetropcs.com',
  'tracfone': '@mmst5.tracfone.com',
  'uscellular': '@email.uscc.net',
  'virgin': '@vmobl.com'
}

def send(number, carrier, message):
  # Format number for command
  formatted_number = number + '{}'

  to_number = formatted_number.format(carriers[carrier])
  auth = ('email', 'password')

  # Establish a secure session with email SMTP server
  server = smtplib.SMTP("smtp.gmail.com", 587)
  server.starttls()
  server.login(auth[0], auth[1])

  # Send message through SMS gateway
  server.sendmail(auth[0], to_number, message)
{% endhighlight %}
