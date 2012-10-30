---
layout: post
title: "Cloudfront private streaming with Ruby on Rails 3"
date: 2012-10-30 17:27
comments: true
categories: [cloudfront streaming, ruby on rails]
author: Krishna Srihari
---

Please check below steps to setup amazon cloudfront private content streaming in Ruby on Rails 3

###Create Amazon s3 bucket to store your private contents

* Go to Amazon Simple storage service(S3)

* Click 'create bucket' button, give bucket name and click 'create' 

* Select bucket, click 'create folder' on top and give folder name

* Add your mp4 contents in newly created folder

***

###Create private streaming distribution in cloudfront

*	Go to cloud front console

*	Click 'create distribution' button

*	Select 'Streaming' and click 'continue'

*	Set  below configuration

	Origin Domain name
		Choose your bucket name in the drop down

	Restrict Bucket Access
		Select ‘Yes’

	Origin Access Identity
		Select ‘Create a new identity’  or ‘use an existing identity’ if you already created it

	Grant Read Permissions on Bucket
		Select ‘Yes, update bucket policy’

	Restrict Viewer Access (Use signed urls)
		Select ‘yes’

	Distribution State
		Select ‘Enabled’ 

* Click ‘create distribution’ and go to cloudfront home page

* Check the status of your newly created private distribution it shows ‘in progress’, wait until status shows as ‘deployed’, it may take longer time to get deployed

***

###Create cloudfront key pairs

Note: If you already created private key and key pair id skip below steps

* Sign in to the AWS management console and go to security credential page at  https://portal.aws.amazon.com/gp/aws/securityCredentials.

*	Under ‘Access credentials’, click the ‘kay pairs’ tab

*	Under ‘Amazon cloudFront key pairs’, click ‘create a new key pair’

*	Save private key and store it in safe place

*	Copy key pair id and paste it somewhere to use it later

Note: These Key pair id and private key required to access the private streaming distribution

***

###Access private streaming in Ruby on Rails

Install <https://github.com/krishnasrihari/cloudfront-private> gem and configure it as described in guide 
 