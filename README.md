<h1>SOC Automation (Home Lab) - Part 1</h1>

<p align="center">
<img src="https://snipboard.io/6rb2ue.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<h2>Description</h2>
<br />
<p align="center">
Welcome to the final part of the series of the active directory project. If you haven't seen the previous parts where we go over how to build out a diagram for this lab, install our virtual machines, configure our tools, and apptive directory, I highly recommend you watch those first to get up to speed.
  
Today's objective is to use Kali Linux to perform a brute-force attack onto our users, giving you a chance to see what this looks like by using Splunk to query for this activity. Afterwards, we will set up and install Atomic Red Team and run a test together. So in the future, you know how to use Atomic Red Team to generate telemetry and detect similar attacks in the future. Also, if you have a machine that does not have at least 16 gigs of RAM. Now, will be a good time to change both the DCs' and target machines ram to 2 gigs, instead of four. Let's get started.

On our Kali Linux machine, the Kali default credentials are "Kali" and "Kali" for the password. We want to set up a static IP address of '192.168.10.250' as per our diagram. To do this, we can right-click the ethernet icon at the top and select "Edit connections".

We want to select the first profile which in my case is called "Wired connection 1". At the bottom left, select the gear icon and then select "ipv4 settings". Under the drop-down, it is currently set to "Automatic (DHCP)". 

You want to click on that and select "Manual". Click on "Add". For the IP address, we'll type in '192.168.10.250". The "Netmask" will be '24' and the "Gateway" is '192.168.10.1'. For the DNS server, it will be '8.8.8.8'. Then, we'll hit "Save". I'll close this out.

Right-click and click on "Open Terminal Here". Now, type in "ip a" and you might notice that our IP has not changed yet. If you take a look over here it is '192.168.10.20'. To reflect these changes, we can simply click on the ethernet icon and click "Disconnect". This will disconnect our network interface.

We can click on it again and select our profile of "Wired Connection 1". Clear out the screen and now let's type in "ip a" again. We do get our IP address of '192.168.10.250'. Just like anything else, let's verify our connectivity by pinging google.com.

Now, let's try pinging our Splunk server which is '192.168.10.10" and we get connectivity. Now, let's update and upgrade our repositories by typing in "sudo apt-get update && sudo apt-get upgrade -y". Type in "Kali" for the password and depending on your download speeds this might take some time.

Once the update and upgrade are finished, we can start setting up our attack by first creating a new directory called "ad-project". To do that, we'll type in "mkdir" and I'll type in "ad-project". This will create a directory on our desktop and all of the files that we will create and use will be put into this directory.

For our attack today, I'll be using a tool called Crowbar, and we can install this by typing in "sudo apt-get install -y crowbar". Type in the password and it should go out and install our tool. So this is the tool that we will use to perform brute force attacks and we can target either our domain controller or our target machine.

I have to say this, please do not target any assets that you do not have permission to do so. Only use this for educational purposes and use this for your labs on machines that you own. Now, that we have this tool installed we want to use a popular word list called Rockyou that comes with Kali Linux.

This is found under the directory "/user/share/wordlists/". If I type in "ls" we can see that there is a file called "Rockyou.text.gz" We can unzip this file using gunzip, so let's type in "sudo gunzip rockyou.txt.gz" and type in "ls" again.

Now, we should see "rockyou.text". Let's copy this file onto our "ad-project" folder by typing in "cp rockyou.txt ~/Desktop/ad-project". I'll hit "Tab" for auto-completion. Hit "Enter" and now let's change into our "ad-project directory".

I'll type in "clear" to clear out the screen. Then, I'll type "ls -lh". We see the file size of "rockyou.txt". Which is about 134 Megabits, so this contains quite a bit of password, and we don't want to use all of it for the sake of this demo. So instead, we'll use the first 20 lines.

To do that, I'll use the command "head -n 20" to specify that I only want to use 20 lines. Then, point it to "rockyou.txt". Hit "Enter" and I'll see 20 lines. I'll clear this out and I'll output it to a file called "password.txt". 

We can do this by putting in a greater than sign, like so, "head -n 20 rockyou.txt > passwords.txt". Hit "Enter" and type in "ls". We should see a "password.txt" and we can open this up by typing in "cat passwords.txt
". Hit "Enter" and we'll see our 20 lines.

Now, as an attacker, you would probably do a lot of recon. Probably, set up some basic active directory attacks, but let's just say in our scenario we know we want to target a certain password, so that's what I'll do. I'll type in "nano password.txt" to edit it and at the very bottom I'll type in a super secure password. Then, I'll save it out by holding cntrl + x.

Type in "y". Then "Enter". Now, if we cat out "password.text" again, we should see our super secure password at the bottom. Now, before we launch the attack, let's head over to our Windows target machine. On our Windows target machine, we want to enable remote desktop and to do that we want to search up for "PC".

So we'll go ahead and type in "PC" and we'll click on "Properties". Once we open that up, we can scroll down and click on "Advanced system settings". We'll log in with the administrator account. Once we have this opened, we want to click on the "Remote" tab.

At the bottom, you want to select "Allow remote connections to this computer" and we'll select "Select Users". Click on "Add" and we'll put in our two users. So one is going to be "Jenny Smith" and I'll click on "Check Names". The second is going to be "Terry Smith". So "T.Smith". Finally, check names again.

Once those are good to go, we can click on "OK". Hit "Apply". Click "OK" to close out. So now we have enabled remote desktop onto our target machine. If you want, you can enable remote desktop on your active directory server, but we're going to just skip on that for now and just only focus on our target machine.


Let's head back over to our Kali Linux machine. I'll go ahead and clear the screen. To use our tool, we'll just type in "crowbar -h". This way, we can see the help menu and see what kind of options are available for us.

If we scroll to the top, we can see that the description Crowbar is a brute force tool that supports OpenVPN, remote desktop protocol, SSH private keys, and VNC Keys. The service that we're interested in here is remote desktop protocol.

This is why we're using this tool and why we enabled RDP. The command that I'll use with Crowbar is the following. I'll use "-b" to specify my service and in my case, it is "RDP" which is remote desktop protocol. I'll also type in "-u". Then, I'll specify the account of interest.

For this example, let's just say we are targeting "Terry Smith", so that is "tSmith". Then, I'll type "-C" to specify a password list. We created one earlier and that was called "password.txt". Lastly, I'll type in "-s" to specify my source IP, and the target machine's IP was '192.168.10.100'. 

We must specify a CIDR notation. I will use the "/32" CIDR notation, instead of "/24". This is because I only want to target this one IP. Again, this goes back to networking and if you aren't sure what I'm talking about, do take some time to learn more about networking fundamentals.

I'll hit "Enter" to run the command. Once we execute Crowbar, it will start trying out all the passwords that are listed under the "passwords.txt" file. Eventually, as we can see, we have an RDP success with the username of "tsmith" and the password right here.

So let's now head over to Splunk and see what telemetry we had generated. Now that we're in Splunk, we'll select "Search and Reporting" and because we did generate the attack, we know that this occurred within the past 15 minutes. We also know that the targeted user was "tsmith".

So let's narrow down our search and only look for events related to "tsmith". So I'll type in "index=endpoint" and then I'll click on the time and I'll select "Last 15 minutes". I'll also add in "tsmith" and hit "Enter". So right away, we have 23 events and if we scroll down just a little bit, we have a field name called "EventCode".

Now, if we click on that. We see a total of 20 counts for the event ID of '4625'. Now, if we were to search what that means. We can type in "event id 4625". We can click "Ultimate Windows Security" and it'll say that an account failed to log on. So this means that there were 20 failed attempts to log into "tsmith's" account.

Which is correct, if you recall our "password.txt" file, there was a total of 21 passwords. One of them being the correct one. Now, if I were to just select '4625', it would automatically update our query and search for it. As you can see with the 20 events here. If we scroll down, I want you to take a look at the time.

So it says '1:35:33 AM' and you might notice that all of these events are happening pretty much at the same time, which can be a clear indication of brute force activity. Now, if we take a look at event code '4624', we have one event and if you're not sure what '4624' is, we can head back over to Ultimate Windows security.

Then, we'll type in '4624' and it'll say an account was successfully logged on. Head back over to our Splunk, we can expand this event by clicking on "Show all 70 lines" and if we were to scroll down just a bit we do happen to see our workstation name as Kali, as well as its IP address that is trying to log in from.

This does indeed belong to our Kai Linux machine. Next, let's install Atomic Red Team on our target machine and then we can run some test on it. To do that, we'll have to open up Powershell with administrator privileges. So we'll search for Powershell and "Run as administrator".

We'll log in using the administrator account and then we'll run the following command "Set-ExecutionPolicy Bypass CurrentUser" and then hit "Enter". We want to type in "y" and then hit "Enter". 

Next, we'll install the Atomic Red Team framework, but before we do that let's set an exclusion for the entire "C:drive". As Microsoft Defender will detect and remove some of the files from Atomic Red Team. So to do that, we'll click on the up arrow and click on "Windows security".

Click on "Virus and threat protection" and click on "Manage settings". Under "Exclusions", we want to click "Add or remove exclusions". Then, click on "Add an exclusion" and select "Folder".

Select "This PC". Scroll down and select our "C:drive" and then click on "Select Folder". We'll have to log in again as administrator and we should see an exclusion for the C drive. Now, we can start installing Atomic Red Team.

To install Atomic Red Team, you want to run the following command and I'll put this in the description down below. This will go out and grab Atomic Red Team and install it onto your target machine. When you see this, you want to press "Y" and hit "Enter" to install the dependencies

Once Atomic Red Team is completed, head over to the "C:drive" and then click under the directory "AtomicRedTeam". Here we will see what are called "atomics". If we go into that, we see a bunch of technique IDs and these map back to Mitre Attack's framework.

So if we head over to the Mitre Attack's framework and scroll down to the "Matrix for Enterprise". If we highlight any of them, we notice that there is a technique ID of 'T1190'. So if that is something we wanted to test, we can take a look and see if there's any 'T119'

There doesn't appear to be any, so that's okay. We can try and test out maybe a "Persistence" tactic. Perhaps, a "Create account". So 'T1136'. We can take a look and see if there are any 'T136' and we do happen to see three of them here.

So if we go and look at our Mitre Attack framework, we see that one is "local account". Two, is "domain account", and three is "cloud account". Looking at our atomics, we have all three options.

In this example, let's go ahead and use the first one. Which is "local account". So I'll go ahead and clear the screen. To use the command, we type in "Invoke-AtomicTest" and then we'll type in the technique ID which was 'T1136.001'. I'll hit "Enter".

So this will automatically generate telemetry based on creating a local account. I want you to take a look at the username that was created, which is "NewLocalUser". Now that our Command has finished running, we can go and look into Splunk and search specifically for "NewLocalUser".

We'll type in "index=endpoint NewLocalUser". So we don't get any events, that's kind of odd isn't it but in a way that's actually a good thing cuz this now tells you that you're blind to this activity. If an attacker compromised your system and created a local account with your current settings, you will not be able to detect that activity.


This is why I love Atomic Red Team. It will identify the gaps and visibility for you. It will also generate the telemetry to see if you can actually detect that activity. Let's do another example, we'll go into Mitre Attack and this time, I will select "Command and Scripting Interpreter".

So this one is 'T1059' and we do have a couple of them. We have one all the way up until seven. So I think that's all of the sub techniques there. We can expand that, and there's Powershell. Which I know for sure we should be able to see, so why don't we do that?

We'll execute that technique. So I'll clear out the screen. I'll type 'T1059.00' and hit "Enter". So Defender is catching some threats. We see a Powershell command here, "-exec bypass -noprofile". Perhaps that is something we can search for in Splunk. So let's go ahead and open up Splunk and take a look.

I'll change the time to "Last 15 minutes". Look at that, we do see a "NewLocalUser". So it just took some time, but the concept still applies. So we can detect that activity, however if you happen to see no events, even after waiting for a bit, then that means you have a gap in visibility.

Now, if we were to look at Powershell. Scrolling down we do see an event. It is quite hard to see, but I'll zoom in. We see "-exec bypass -noprofile $XmL" and if you recall in our Powershell window, that is this command right here. So this means that we can build alerts to detect on this activity in the future if we wanted to, along with creating a new local user account. Now, that we know that the event actually generates, it just takes a little time. 

Before we end, there is one last thing I wanted to talk about. That is with Kali Linux. In my example, I used "tsmith" with the password of this password. If you created a different account with a different password and you want to follow along, you need to make sure that your password is listed in the "passwords.txt" file.

Since as you can see at the bottom, my password is right there. So if your password to "tsmith" or to another user account was let's just say "summer2024!". Then, you need to make sure you put that into your "passwords.txt" file, otherwise this tool wouldn't be able to brute force successfully. So that's just one quick note.

If you're new to Virtual box and you don't know what a snapshot is, it's essentially a backup. So if I did happen to break something and my Kali Linux no longer works. For example, I can revert it back to a snapshot to a known good state. 

To take a snapshot, you simply click on "Machine" at the top and select "Take Snapshot". Then, you can create a name and a description and hit "OK". Now, to revert to a snapshot, there are a lot of different options. You can simply click on "close" and then just check "Restore Current snapshot". Then, that's it.

If you have followed along, you have successfully completed the active directory project and I hope that you had as much fun as I did creating this. We went over a ton of material and I know it can get pretty technical very quickly. I cannot stress this enough, if you want to become an amazing blue teamer, you must understand how Mitre Attack works. As this will provide you with some guidance on what an attacker might do, once they compromise in environment.

It would mean a lot to me if you shared this project with other aspiring analysts who want to get some hands-on experience. Thank you so much for watching, and I truly hope that this has helped you in any way. Remember to stay curious and do things differently.
