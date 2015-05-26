---
layout: default
---

KentProjects was a final year project undertaken by [James Dryden](http://jdrydn.com),
[Matthew House](http://matthewhou.se) and [Matthew Weeks](http://matt.weeks.codes) over the course of our fourth year
at the [University Of Kent](http://cs.kent.ac.uk).

### Description

KentProjects aims to build upon, and improve the current system that final year students use to choose their projects.
Our task was to create a more streamlined solution that enables students to get into groups, choose their projects and
get them approved by the respective supervisors in as simple a way as possible. This is especially important as the
majority of students needing to choose projects will be on their year in industry placement so may not be on campus
until the start of the new year. It was designed to run in two halves; a front-end HTML5 website built with JavaScript
& Flat UI for the end-user and a back-end API for the website to interact with, written in PHP and using a MySQL
database with a sophisticated layer of caching, using Memcached, to improve performance.

### Review

We have managed to create a simple, but powerful, web application that enables staff and students to interact with a
pool of projects and organize themselves into groups. Both staff & students sign in with their existing Kent ID,
removing the complication of having a separate username & password for this application. The staff are able to view the
lists of projects, add new projects and keep track of students and the groups they have created. The students are able
to view all the projects that are available to them, they can create and join groups with other students and then
request to do a project that appeals to them. The solution is completely web based and mobile-optimised, enabling
students to organise and select their projects remotely and on their smartphones and tablets if desired!

### And More...

KentProjects was built as 2 discrete and functionally independent components; a web app (built in HTML, JS, & CSS) and
an API (built in PHP). The two are completely decoupled and, assuming the API specifications were matched exactly,
either could be replaced.

You can check out the [Web App README](./readme-web.html) and the [API README](./readme-api.html).
