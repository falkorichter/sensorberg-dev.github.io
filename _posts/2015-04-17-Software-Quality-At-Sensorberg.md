---
layout: post
title: "Software quality at Sensorberg"
date: 2015-04-17 16:20:00 +1
comments: true
tags: continous-integration
---

# Software quality is important for us

We´re trying to keep you in the loop about the quality of our software. We have public continuous integration jobs for the majority of our open projects. Here is an overview of all the integration jobs and what they are doing:

## [Resolver](https://travis-ci.org/sensorberg-dev/resolver)  <img src="https://travis-ci.org/sensorberg-dev/resolver.svg?branch=master" alt="Resolver Microservice Build Status" style="float:right">

* compile the project
* run all tests

<!--more-->

## [Android SDK](https://travis-ci.org/sensorberg-dev/android-sdk) <img src="https://travis-ci.org/sensorberg-dev/android-sdk.svg?branch=master" alt="Android SDK Build Status" style="float:right">

* compile the project
* run all tests in an Android emulator

## [Android SDK Bootstrapper](https://travis-ci.org/sensorberg-dev/android-sdk-bootstrapper) <img src="https://travis-ci.org/sensorberg-dev/android-sdk-bootstrapper.svg?branch=master" alt="Android SDK Bootstrapper Build Status" style="float:right">

* compile the project

## [Android SDK Samples](https://travis-ci.org/sensorberg-dev/android-sdk-samples)  <img src="https://travis-ci.org/sensorberg-dev/android-sdk-samples.svg?branch=master" alt="Android SDK Samples Build Status" style="float:right">

* compile the project

## [Developer Hub Website Project](https://travis-ci.org/sensorberg-dev/sensorberg-dev.github.io)  <img src="https://travis-ci.org/sensorberg-dev/sensorberg-dev.github.io.svg?branch=master" alt="Developer Hub Website Project Build Status" style="float:right">

* generate the page
* check all links
* check image alt tags

## Travis files

Here is a snapshot of our current travis-ci configuration files:

<script src="https://gist.github.com/sensorberg-admin/7f39b593fd47b5b56aac.js"></script>