---
layout: post
title: "New Project: Flight Tracking"
author: Garrett Woodbridge
date: "2019-12-19 17:08:40 -0600"
tags: "adsb"
---

### Preface
Flights for Life, LLC is a non-profit that currently partners with blood institutes in Arizona, Texas, Oklahoma, and Arkansas. Their mission is to fly important blood products between smaller rural communities with private pilot volunteers. Currently, pilots call a local number to let the organization know when they are departing and their estimated travel time. After this, the organization can't communicate with the pilot and are unaware of any delays or weather that could affect the flights. Starting in January 2020, almost all pilots in the U.S. are required to have ADS-B. ADS-B transmits live location, air speed, altitude and many other important pieces of data.

### The Idea
The idea is currently three-fold.
1. Update the backend system currently used by Flights for Life that allows for easier ways to upload schedules. This would also encompass an easier method for pilots to sign up for flights and a place for location monitoring within the backend.
2. A notification system that most likely will use text messaging to save on costs. It will receive, the phone number, the cell carrier for the number, and the message and then send it.
3. Use APIs from companies such as FlightAware or FlightRadar24 to gather the ADS-B data. Determine what types of notifications are necessary and would be most useful with cost in mind.
