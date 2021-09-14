---
layout: post
title:  "[Android] WorkManager"
date:   2021-06-13 09:00:00
tags: kotlin android workmanager concurrency background asynchronous
categories: android
---

# WorkManager

1. Worker: The task itself what you want to perform in the background. Need to override the doWork() method, and implement your logic inside of the method.
2. WorkRequest: To execute some work, need to create this request instance, and pass Worker instance and Constraints instance.
	- abstract class
	- there are several implementations.
	- OneTimeWorkRequest
	- PeriodicWorkRequest
	- 
3. WorkManager: The WorkManager schedules our WorkRequest and run it.