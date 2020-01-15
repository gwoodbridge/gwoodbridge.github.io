---
layout: post
title: "Update #2: Flight Tracking"
author: Garrett Woodbridge
date: "2020-01-05 09:58:40 -0600"
tags: "adsb"
---

### Thoughts
While thinking over all of the possible sections of the backend system, I thought it would be great to put a reporting feature in. Flights for Life and the blood institutes could see money saved compared to driving as well as average flight times between locations. Plus, the pilots could see average speed for the flights, distance covered, etc. Overall, here's all of the sections of the backend system I'm considering:
1. Login Screen: Basic login that separates out into 4 different permission groups:
   1. Flights for Life Permissions: able to see flights, adjust schedules, pull reports.
   2. Blood Institute Scheduler: able to schedule flights and see a live flight screen with their flights.
   3. Blood Institute Admin: same permissions as the scheduler except they can also pull reports and manually assign a pilot for a route.
   4. Pilot: able to pull reports related to them and sign up for flights.
2. Flight Tracking Screen: a screen that pulls data for all flights related to the current users permissions.
3. View Schedule: where a pilot can view the scheduled routes and sign up.
4. Upload Schedule: where the blood institutes can upload the schedule for their organization. They can also assign people (pilots and staff) for each flight.
5. Reports: where reports can be pulled based on the users permissions.
6. Set up alerts: this section would allow schedulers or admins to import their staff that picks up product at the airport. It would include information like their name, cell phone number, carrier, and type of notification (live tracking, eta, takeoff, landing, etc).
