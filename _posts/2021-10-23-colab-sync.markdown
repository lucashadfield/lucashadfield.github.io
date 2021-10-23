---
layout: post
title:  "Syncing local python packages to Google Colab"
date:   2021-10-23 12:00:00 +1100
---

This post goes through how to set up a one-way sync of a local python package to a Google Colab instance using `cloudflared`, `lsyncd` and some jupyter magic.
This allows you to work on a package locally in your IDE of choice and have it automatically stay in sync with the version imported into your notebook.
Within a couple of seconds of making a change locally, your changes are auto-reloaded into your running notebook without having to re-import anything.
This example is focusing on using a python package but that's not a requirement. Ultimately we're just syncing a local directory to a remote one so this could be used in various ways, eg. syncing config files that you want your notebook code to pick up.

<video src="https://user-images.githubusercontent.com/12473153/138543324-6b3035cc-ee8a-4dd5-a375-797ab9778e4a.mp4" controls="controls" style="width: 100%;">
</video>


<br/>
# One time setup
You will need to install `cloudflared` and `lsyncd` on your **local** machine. 

- [cloudflared](https://github.com/cloudflare/cloudflared) is used to easily set up an ssh tunnel between your machine and the google colab instance
- [lsyncd](https://github.com/axkibe/lsyncd) is used to detect local file changes and sync them to the target using rsync (in this case with rsyncssh)


1. Install `cloudflared` and `lsyncd`:
   
   ```
sudo apt-get install cloudflared lsyncd
   ```
   
    Refer to the project github pages for alternative installation options.

2. Next add this to your `~/.ssh/config`
   ```shell
   Host *.trycloudflare.com
       HostName %h
       User root
       Port 22
       ProxyCommand /usr/local/bin/cloudflared access ssh --hostname %h
   ```
Replace the `/usr/local/bin/cloudflared` with the absolute path to `cloudflared` if installed elsewhere.

<br/>
# On each notebook run
Each time you run a notebook, you will need to set up cloudflared on that machine. Thankfully, [colab-ssh](https://github.com/WassimBenzarti/colab-ssh) is a package that handles all this for you.

1. In your **notebook**, simply run:

    ```python
   !pip install colab_ssh --upgrade
    
   from colab_ssh import launch_ssh_cloudflared
   launch_ssh_cloudflared(password='<SSH_PASSWORD_HERE>')
    ```

    This will return an ssh command like this:
   
   ```
   ssh <CLOUDFLARE_PREFIX>.trycloudflare.com
   ```

2. Next, you'll want to add your ssh key to the remote `~/.ssh/authorized_keys`. From your **local**, run:
    ```
   ssh-copy-id <CLOUDFLARE_PREFIX>.trycloudflare.com
    ```
    You should now be able to ssh into the colab instance without a password.

3. Now we can start up lsyncd. You need to specify the source and target directories. From your **local**, run:
    ```
   lsyncd -nodaemon -delay 1 -rsyncssh <SOURCE_DIR> <CLOUDFLARE_PREFIX>.trycloudflare.com /<PACKAGE_NAME>
    ```
    For example:
    ```
   lsyncd -nodaemon -delay 1 -rsyncssh /home/lucas/repos/demo_package training-eggs-others-criminal.trycloudflare.com /demo_package
   ```


4. Finally, back in the **notebook**, run:
   
    ```python
   import sys
   sys.path.insert(0, '/<PACKAGE_NAME>')
 
   %load_ext autoreload
   %autoreload 2
 
   import <PACKAGE_NAME>
    ```
When you import your package into the notebook, it will be pointing at the synced directory. 
Whenever you make changes locally, they are synced to colab, and when you next run a cell, colab will reload the package with all the updates.


<br/>
# Why?
I like notebooks for explorative and interactive workflows but as soon as I want to write a non-trivial function or class, I much prefer doing that in an IDE where I get all the benefits of autocomplete and code inspections.
With this approach, I can write my classes and functions in pycharm and call them interactively in a notebook.
One day, all this might not be necessary if notebooks catch up, but right now a proper IDE is still a much more fluid way for me to work.