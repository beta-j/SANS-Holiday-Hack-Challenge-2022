# OBJECTIVE 3 - Recover the Elfen Ring #
## 3B - Prison Escape ##

### OBJECTIVE : ###
>Escape from a container.  Get hints for this challenge from Bow Ninecandle in the Elfen Ring.  What hex string appears in the host file ``/home/jailer/.ssh/jail.key.priv``?
#  

## HINTS: ##
<details>
  <summary>Hints provided for Objective 3A</summary>
  
>-  Were you able to `mount` up?  If so, users’ `home/` directories can be a great place to look for secrets...
>-	When users are over-privileged, they can often act as root.  When *containers* have too many permissions, they can affect the host!

</details>

#  

## PROCEDURE : ##

There is no root password so just by running ``sudo su`` we can elevate our user to root privileges.  By running `fdisk -l` we can confirm that the root filesystem is on `/dev/vda`.

I used [this writeup](https://book.hacktricks.xyz/linux-hardening/privilege-escalation/docker-breakout/docker-breakout-privilege-escalation) for the next steps to create a new directory and mount `/dev/vda` to it:
```
# mkdir -p /mnt/hola
# mount /dev/vda /mnt/hola
# cd /mnt/hola/
```

The contents of the host filesystem are now all mounted to `/mnt/hola` and we are free to look through them.  This includes the home directory for the user `jailer` which includes a hidden `.ssh` directory with the `jail.key.priv` file we are tasked to look for.  The file has some cool ASCII art showing a sign-post that reads **“one step closer 082bb339ec19de4935867”** which is the answer to this objective 😊
