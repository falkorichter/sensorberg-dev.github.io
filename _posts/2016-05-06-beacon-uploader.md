---
layout: post
title: "Beacon Uploader"
date: 2016-05-06
comments: true
tags: beacon management platform uploader csv
---

We have been working hard to help you manage beacons more effectively. Today we are happy to announce that you can now create multiple beacons at once without filling countless browser forms and endlessly hitting the save button. This process can now be automated using our CSV Beacon Upload Tool.

# Check it out
<a href="https://manage.sensorberg.com/#/beacon/upload">
  <img src="/images/beacon-uploader.png" alt="CSV Beacon Uploader: Beacon Management Platform" style="width: 100%;">
</a>

[https://manage.sensorberg.com/#/beacon/upload](https://manage.sensorberg.com/#/beacon/upload)

# How it works
The Beacon Upload Tool takes a CSV file, goes through each line, and if all of the information in a given line is valid, a new beacon is added to your Sensorberg account based on that information. The concept is pretty simple: each line of your csv describes a single beacon. The fields that can be filled in using the Beacon Upload Tool are as follows:

- name
- description
- proximityId
- major
- minor
- latitude
- longitude


The Beacon Upload Tool is smart enough to generate valid entries for a beacon's `proximityId`, `major` and `minor`. The only field we actually require you to fill in is `name`, the rest is optional.

# Acceptable Formats
We recommend using the CSV template below as a base for creating your CSV data file. Note that the order of the columns in your file matters. The columns must be defined in the first line of your CSV, and they must match the example below.

Please use decimal values for `latitude` and `longitude`: e.g. `52.51948`. Do not use military format coordinates, because our system does not understand them. If you need to convert your military formatted coordinates to decimal format, you may be able to do so using this [GPS Conversion Tool](http://www.findlatitudeandlongitude.com/gps-coordinates-converter/#.VwZwQRN97y8).

Along the same lines please pay attention to your delimiters.  Meaning that if there are commas in your descriptions, or in your numbers please use semicolons as the delimiter for your file.

# CSV template example
{% highlight csv %}
name,description,proximityId,major,minor,latitude,longitude
Entrance Beacon,South Entrance parking,73676723-7400-0000-ffff-0000ffff0001,62169,12870,52.51946,13.39851
Registration Beacon,Check-in desk,73676723-7400-0000-ffff-0000ffff0001,62169,12873,52.51948,13.39855
{% endhighlight  %}

So the next time you have a box full of new beacons to register, give the [Beacon Upload Tool](https://manage.sensorberg.com/#/beacon/upload) a try and let us know about your experience.

Happy Uploading.

Sensorberg Tech