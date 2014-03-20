Quick Spin up for Chef

1) sign up for hosted chef
```
https://preview.opscode.com/signup
```
This gives you 5 free nodes.  Follow instructions they give you to get chef workstation installed/working.


2) download and install virtualbox - 
```
http://virtualbox.org
```
3) download and install vagrant - 
```
http://vagrantup.com
```
4) make your base working directory ~/dev/chef (or whatever)

5) grab the Vagrantfile from here: https://github.com/tcotav/Vagrantfiles
  -  you'll probably want Vagrantfile.linux
  -  edit the file replacing "YOURORG" with whatever organization name you used to sign up with chef in step 1
  - you can change the line: chef.node_name = "ubuntu-chef" if you feel spunky.  this is the name chef will know your vm as.

6) You're ready to vagrant and chef.  At command line:

```
vagrant up
```
This should download the virtualbox image for you, insert chef client, and get you spun up.

7) Connect to the new box when its done with:
```
vagrant ssh
```

You have a client set up.  Now you can add cookbooks to your repo (knife cookbook upload) and all sorts of other stuff, but this takes care of the initial setup

