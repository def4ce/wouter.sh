+++
title = "Disable SSH on your EC2 instances"
date = "2019-07-14"
category = "Amazon Web Services"
tags = ["aws"]
+++

**If you have to SSH into your servers, then your automation has failed**. 

I know, crazy...

To make the transition to this mindset easier you could just disable access to port 22 in your instance security group configuration. This will highlight the things you still need to automate while still having the ability to enable access your instances to remedy immediate operational issues.

Disabling inbound SSH will stop you from cheating. You can't just log in and quickly fix the issue. You have to re-enable access to do this and hopefully this will become annoying enough to force you to get your automation in order.

This is both a frightening and useful thing I've learned while doing automation in AWS.


as a note: `If your application relies on being able to push to a server via SSH, then disabling it might be a bad idea.`
