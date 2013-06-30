---
layout: post
style: text
title: Autoscaling on AWS - 2
---

This post is second in the series [Autoscaling with AWS](/2013/06/21/autoscaling-with-aws/). The point being that unless you programatically scale things up and down on AWS, it comes out costlier than other hosting options, for load profiles that look like this.

![load profiles](/img/loadprofile.png "Our load profile")

A quick recap is in order. I suggested

 * Baking your stack into the EBS based AMI (all third party code)
 * Using instance stores as much as possible, e.g. for storing code, logs, and transient data like sessions.

## Provisioning with user data

On EC2, an [AMI](https://aws.amazon.com/amis/) is a system image you could use to start a new instance. When starting a new instance, you can also specify a user data script to be run right after, this allows us to provision the instance.

![qzaidi.github.io](/img/userdata.png "Provisioning an instance via user data")

Start an instance with any of the public OS AMIs on EC2, and customize it to your taste. When done, create a private image. 

The extent of customization is a choice, between flexibility and provisioning speed. You have the option of just using a barebones OS AMI, an AMI containing the OS and the full software stack or something in between. You can

 * Put everything in the AMI, and you have to rebuild AMI's much more frequently, but the provisioning will be faster.
 * Put too few things in the AMI and setup everything else via the provisioning script. Provisioning takes longer and your ability to respond to sudden traffic spikes suffers.

The choice we have made is to keep OS and 3rd party software in the AMI (blue). For each of our server types, we have a base AMI (e.g. one for redis servers, one for application servers, and so on). Configuration and code is kept elsewhere (green), to be pulled via the user data script.

![qzaidi.github.io](/img/stack.png "Our Stack")

For example - our base AMI for the app servers consists of

- Ubuntu x64 OS
- node.js 0.10.x
- couple of native npm packages that would otherwise slow down provisioning.
- git, screen and misc. tools (e.g. apachetop,iptraf).
- nginx 1.3.7 without the config, and without the script that automatically starts it.
- termination scripts that upload logs we want to persist to s3.
- deployment bootstrap - a script that we run to deploy code from a certain git branch and execute post install.

and the startup script (userdata) will do rest of the provisioning like,

- apply security updates in the background.
- configure transient storage paths (e.g. /var/log/nginx, /data/redis). These will have to be *created after bootup*, as nothing persists on an ephermal volume.
- add fingerprint of the git server to ssh known hosts
- deploy configuration from a git repo
- deploy code via a repo from the production branch
- run post deployment script
- start nginx

With only nginx baked in, when we change the nginx configuration, we don't need to rebuild the AMI, but when we change nginx version we have to rebuild the AMI and then update autoscaling launch config. This illustrates the trade off. 

If you haven't used [vagrant](http://www.vagrantup.com) before, I would highly recommend it. Getting the provisioning script right will require some experimentation, and vagrant gives you the ability to test things locally before moving it to the cloud. They even support using AWS, although I haven't tried or used that feature ever.

The other thing to choose is the right instance type. A single autoscaling group will launch only one version of AMI and only one type of instance. There is a case for chosing an  m1.small instance, as this is will offer maximum granularity and a fine grained match between your capacity and traffic saves money. We use m1.small instances, but I wouldn't say this is an open and shut choice for 2 reasons.

- The amount of instance storage varies with instance type. You get 160 GB of free instance store with m1.small, 410 GB with medium, and 2 420 GB volumes with large instance types. 160 GB may or may not be enough for you.
- The m1.small instance gets a fixed CPU, so if you have short term cpu spikes it will perform worse than a micro instance. 

Once you have decided on the instance type and what to bake in the AMI, its time to create an AMI. We had problems when using the web console or even [ec2-create-image](https://forums.aws.amazon.com/message.jspa?messageID=218886), and used ec2-register instead. Whatever you use, make sure your AMI remembers to mount the ephermal volumes.

## VPC

Finally, if you aren't using a VPC, go for it. Apart from obvious security benefits, a VPC would allow you greater control on your internal DNS, allow assigning fixed private IPs to servers, and speed up provisioning in general. The [simplest configuration for the VPC](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_Scenario2.html) is to create a public subnet, for your public instances (e.g. NAT) , load balancers etc, and a private subnet for everything else. 

That's it for now. In the next installment, I would try to cover autoscaling policy, cloudwatch triggers, and deployment process.
