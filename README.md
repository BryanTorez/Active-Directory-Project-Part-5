<h1>SOC Automation (Home Lab) - Part 5</h1>

<p align="center">
<img src="https://snipboard.io/NsxnJW.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<h2>Description</h2>
<br />
<p align="center">
Welcome to the final part of the series of the active directory project. If you haven't seen the previous parts where we go over how to build out a diagram for this lab, install our virtual machines, configure our tools, and active directory, I highly recommend you watch those first to get up to speed.
<br />
<br />
<img src="https://snipboard.io/Jx5qo2.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
Today's objective is to use Kali Linux to perform a brute-force attack onto our users, giving you a chance to see what this looks like by using Splunk to query for this activity. Afterwards, we will set up and install Atomic Red Team and run a test together. So in the future, you know how to use Atomic Red Team to generate telemetry and detect similar attacks in the future. Also, if you have a machine that does not have at least 16 gigs of RAM. Now, will be a good time to change both the DCs' and target machines ram to 2 gigs, instead of four. Let's get started.
<br />
<br />
<img src="https://snipboard.io/YekHRr.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
On our Kali Linux machine, the Kali default credentials are "Kali" and "Kali" for the password. We want to set up a static IP address of '192.168.10.250' as per our diagram. To do this, we can right-click the ethernet icon at the top and select "Edit connections".
<br />
<br />
<img src="https://snipboard.io/p4MwtN.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="https://snipboard.io/X8cLx0.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="https://snipboard.io/b2q8Zy.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
We want to select the first profile which in my case is called "Wired connection 1". At the bottom left, select the gear icon and then select "ipv4 settings". Under the drop-down, it is currently set to "Automatic (DHCP)". 
<br />
<br />
<img src="https://snipboard.io/yk7vdH.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="https://snipboard.io/qpaoDJ.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="https://snipboard.io/8gQj3R.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
You want to click on that and select "Manual". Click on "Add". For the IP address, we'll type in '192.168.10.250". The "Netmask" will be '24' and the "Gateway" is '192.168.10.1'. For the DNS server, it will be '8.8.8.8'. Then, we'll hit "Save". I'll close this out.
<br />
<br />
<img src="https://snipboard.io/0A7GOd.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="https://snipboard.io/IryCEd.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="https://snipboard.io/NZlab4.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
Right-click and click on "Open Terminal Here". Now, type in "ip a" and you might notice that our IP has not changed yet. If you take a look over here it is '192.168.10.20'. To reflect these changes, we can simply click on the ethernet icon and click "Disconnect". This will disconnect our network interface.
<br />
<br />
<img src="https://snipboard.io/9BbC48.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="https://snipboard.io/G34h9U.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="https://snipboard.io/NjFnh3.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="https://snipboard.io/TlMhAK.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
We can click on it again and select our profile of "Wired Connection 1". Clear out the screen and now let's type in "ip a" again. We do get our IP address of '192.168.10.250'. Just like anything else, let's verify our connectivity by pinging google.com.
<br />
<br />
<img src="https://snipboard.io/jS2luz.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="https://snipboard.io/wuiNO5.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="https://snipboard.io/3Rcefg.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="https://snipboard.io/BiNFvG.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
Now, let's try pinging our Splunk server which is '192.168.10.10" and we get connectivity. Now, let's update and upgrade our repositories by typing in "sudo apt-get update && sudo apt-get upgrade -y". Type in "Kali" for the password and depending on your download speeds this might take some time.
<br />
<br />
<img src="https://snipboard.io/iY1tDF.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="https://snipboard.io/ZM0Hlt.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="https://snipboard.io/dHJCy1.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
Once the update and upgrade are finished, we can start setting up our attack by first creating a new directory called "ad-project". To do that, we'll type in "mkdir" and I'll type in "ad-project". This will create a directory on our desktop and all of the files that we will create and use will be put into this directory.
<br />
<br />
<img src="https://snipboard.io/4aSB0q.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="https://snipboard.io/1rc6mG.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="https://snipboard.io/GgxvBY.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
For our attack today, I'll be using a tool called Crowbar, and we can install this by typing in "sudo apt-get install -y crowbar". Type in the password and it should go out and install our tool. So this is the tool that we will use to perform brute force attacks and we can target either our domain controller or our target machine.
<br />
<br />
<img src="https://snipboard.io/jXqQK9.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="https://snipboard.io/oiARx9.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="https://snipboard.io/KYtsfa.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
I have to say this, please do not target any assets that you do not have permission to do so. Only use this for educational purposes and use this for your labs on machines that you own. Now, that we have this tool installed we want to use a popular word list called Rockyou that comes with Kali Linux.
<br />
<br />
<br />
<br />
This is found under the directory "/user/share/wordlists/". If I type in "ls" we can see that there is a file called "Rockyou.text.gz" We can unzip this file using gunzip, so let's type in "sudo gunzip rockyou.txt.gz" and type in "ls" again.
<br />
<br />
<img src="https://snipboard.io/hXAWRm.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="https://snipboard.io/ezdiuE.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="https://snipboard.io/SlqIdX.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
Now, we should see "rockyou.text". Let's copy this file onto our "ad-project" folder by typing in "cp rockyou.txt ~/Desktop/ad-project". I'll hit "Tab" for auto-completion. Hit "Enter" and now let's change into our "ad-project directory".
<br />
<br />
<img src="https://snipboard.io/kIOBMz.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="https://snipboard.io/B9eRJA.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="https://snipboard.io/CmwAXI.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
I'll type in "clear" to clear out the screen. Then, I'll type "ls -lh". We see the file size of "rockyou.txt". Which is about 134 Megabits, so this contains quite a bit of password, and we don't want to use all of it for the sake of this demo. So instead, we'll use the first 20 lines.
<br />
<br />
<img src="https://snipboard.io/1IOBDZ.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
To do that, I'll use the command "head -n 20" to specify that I only want to use 20 lines. Then, point it to "rockyou.txt". Hit "Enter" and I'll see 20 lines. I'll clear this out and I'll output it to a file called "password.txt". 
<br />
<br />
<img src="https://snipboard.io/QUXNFb.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="https://snipboard.io/GJAT56.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
We can do this by putting in a greater than sign, like so, "head -n 20 rockyou.txt > passwords.txt". Hit "Enter" and type in "ls". We should see a "password.txt" and we can open this up by typing in "cat passwords.txt". Hit "Enter" and we'll see our 20 lines.
<br />
<br />
<img src="https://snipboard.io/HfIly9.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="https://snipboard.io/4m9dbn.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="https://snipboard.io/BcpSY1.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="https://snipboard.io/JnUMbp.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
Now, as an attacker, you would probably do a lot of recon. Probably, set up some basic active directory attacks, but let's just say in our scenario we know we want to target a certain password, so that's what I'll do. I'll type in "nano password.txt" to edit it and at the very bottom I'll type in a super secure password. Then, I'll save it out by holding cntrl + x. Type in "y". Then "Enter".
<br />
<br />
<img src="https://snipboard.io/qY89EX.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="https://snipboard.io/N5zuKv.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="https://snipboard.io/C4Kse0.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
Now, if we cat out "password.text" again, we should see our super secure password at the bottom. Now, before we launch the attack, let's head over to our Windows target machine. On our Windows target machine, we want to enable remote desktop and to do that we want to search up for "PC". So we'll go ahead and type in "PC" and we'll click on "Properties". 
<br />
<br />
<img src="https://snipboard.io/xJ8h5l.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="https://snipboard.io/bhjTKN.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
Once we open that up, we can scroll down and click on "Advanced system settings". We'll log in with the administrator account. Once we have this opened, we want to click on the "Remote" tab.
<br />
<br />
<img src="https://snipboard.io/UWgMAD.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="https://snipboard.io/djkVUG.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="https://snipboard.io/YUyI4m.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
At the bottom, you want to select "Allow remote connections to this computer" and we'll select "Select Users". Click on "Add" and we'll put in our two users. So one is going to be "Jenny Smith" and I'll click on "Check Names". The second is going to be "Terry Smith". So "T.Smith". Finally, check names again. Once those are good to go, we can click on "OK".
<br />
<br />
<img src="https://snipboard.io/ayExZ0.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="https://snipboard.io/BTP7ru.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="https://snipboard.io/LPXbcO.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="https://snipboard.io/B35hIZ.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
So now we have enabled remote desktop onto our target machine. If you want, you can enable remote desktop on your active directory server, but we're going to just skip on that for now and just only focus on our target machine.
<br />
<br />
<br />
<br />
Let's head back over to our Kali Linux machine. I'll go ahead and clear the screen. To use our tool, we'll just type in "crowbar -h". This way, we can see the help menu and see what kind of options are available for us.
<br />
<br />
<img src="https://snipboard.io/Ga9ePC.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="https://snipboard.io/TFwpUr.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
If we scroll to the top, we can see that the description "Crowbar is a brute force tool that supports OpenVPN, remote desktop protocol, SSH private keys, and VNC Keys." The service that we're interested in here is remote desktop protocol.
<br />
<br />
<img src="https://snipboard.io/Et93Sz.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
This is why we're using this tool and why we enabled RDP. The command that I'll use with Crowbar is the following. I'll use "-b" to specify my service and in my case, it is "RDP" which is remote desktop protocol. I'll also type in "-u". Then, I'll specify the account of interest.
<br />
<br />
<img src="https://snipboard.io/XIVGAd.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
For this example, let's just say we are targeting "Terry Smith", so that is "tsmith". Then, I'll type "-C" to specify a password list. We created one earlier and that was called "passwords.txt". Lastly, I'll type in "-s" to specify my source IP, and the target machine's IP was '192.168.10.100'. 
<br />
<br />
<img src="https://snipboard.io/EAesRS.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
We must specify a CIDR notation. I will use the "/32" CIDR notation, instead of "/24". This is because I only want to target this one IP. Again, this goes back to networking and if you aren't sure what I'm talking about, do take some time to learn more about networking fundamentals.
<br />
<br />
<img src="https://snipboard.io/g65kyl.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
I'll hit "Enter" to run the command. Once we execute Crowbar, it will start trying out all the passwords that are listed under the "passwords.txt" file. Eventually, as we can see, we have an RDP success with the username of "tsmith" and the password.
<br />
<br />
<img src="https://snipboard.io/8QoN91.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
So let's now head over to Splunk and see what telemetry we had generated. Now that we're in Splunk, we'll select "Search and Reporting" and because we did generate the attack, we know that this occurred within the past 15 minutes. We also know that the targeted user was "tsmith".
<br />
<br />
<img src="https://snipboard.io/TvLP6n.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
So let's narrow down our search and only look for events related to "tsmith". So I'll type in "index=endpoint" and then I'll click on the time and I'll select "Last 15 minutes". I'll also add in "tsmith" and hit "Enter".
<br />
<br />
<img src="https://snipboard.io/NSi7uF.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="https://snipboard.io/BoslOJ.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="https://snipboard.io/3lsE9Z.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
So right away, we have 23 events and if we scroll down just a little bit, we have a field name called "EventCode". Now, if we click on that. We see a total of 20 counts for the event ID of '4625'.
<br />
<br />
<img src="https://snipboard.io/t9MCWq.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="https://snipboard.io/s2EQx4.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="https://snipboard.io/h4UNLS.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
Now, if we were to search what that means. We can type in "event id 4625". We can click "Ultimate Windows Security" and it'll say that an account failed to log on. So this means that there were 20 failed attempts to log into "tsmith's" account.
<br />
<br />
<img src="https://snipboard.io/QLa3ti.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="https://snipboard.io/PpXlHK.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="https://snipboard.io/X5s8Q0.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
Which is correct, if you recall our "password.txt" file, there was a total of 21 passwords. One of them being the correct one. Now, if I were to just select '4625', it would automatically update our query and search for it. As you can see with the 20 events here. If we scroll down, I want you to take a look at the time.
<br />
<br />
<img src="https://snipboard.io/cxzj6C.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="https://snipboard.io/Mt7z9W.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="https://snipboard.io/ZAg4HT.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
So it says '1:35:33 AM' and you might notice that all of these events are happening pretty much at the same time, which can be a clear indication of brute force activity. Now, if we take a look at event code '4624', we have one event and if you're not sure what '4624' is, we can head back over to Ultimate Windows security.
<br />
<br />
<img src="https://snipboard.io/uczgsG.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="https://snipboard.io/H8U62T.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="https://snipboard.io/JRqgQf.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="https://snipboard.io/7Y6v1K.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
Then, we'll type in '4624' and it'll say an account was successfully logged on. Head back over to our Splunk, we can expand this event by clicking on "Show all 70 lines" and if we were to scroll down just a bit we do happen to see our workstation name as Kali, as well as its IP address that is trying to log in from.
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
This does indeed belong to our Kai Linux machine. Next, let's install Atomic Red Team on our target machine and then we can run some test on it. To do that, we'll have to open up Powershell with administrator privileges. So we'll search for Powershell and "Run as administrator".
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
We'll log in using the administrator account and then we'll run the following command "Set-ExecutionPolicy Bypass CurrentUser" and then hit "Enter". We want to type in "y" and then hit "Enter". 
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
Next, we'll install the Atomic Red Team framework, but before we do that let's set an exclusion for the entire "C:drive". As Microsoft Defender will detect and remove some of the files from Atomic Red Team. So to do that, we'll click on the up arrow and click on "Windows security".
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
Click on "Virus and threat protection" and click on "Manage settings". Under "Exclusions", we want to click "Add or remove exclusions". Then, click on "Add an exclusion" and select "Folder".
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
Select "This PC". Scroll down and select our "C:drive" and then click on "Select Folder". We'll have to log in again as administrator and we should see an exclusion for the C drive. Now, we can start installing Atomic Red Team.
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
To install Atomic Red Team, you want to run the following command and I'll put this in the description down below. This will go out and grab Atomic Red Team and install it onto your target machine. When you see this, you want to press "Y" and hit "Enter" to install the dependencies
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
Once Atomic Red Team is completed, head over to the "C:drive" and then click under the directory "AtomicRedTeam". Here we will see what are called "atomics". If we go into that, we see a bunch of technique IDs and these map back to Mitre Attack's framework.
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
So if we head over to the Mitre Attack's framework and scroll down to the "Matrix for Enterprise". If we highlight any of them, we notice that there is a technique ID of 'T1190'. So if that is something we wanted to test, we can take a look and see if there's any 'T119'
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
There doesn't appear to be any, so that's okay. We can try and test out maybe a "Persistence" tactic. Perhaps, a "Create account". So 'T1136'. We can take a look and see if there are any 'T136' and we do happen to see three of them here.
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
So if we go and look at our Mitre Attack framework, we see that one is "local account". Two, is "domain account", and three is "cloud account". Looking at our atomics, we have all three options.
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
In this example, let's go ahead and use the first one. Which is "local account". So I'll go ahead and clear the screen. To use the command, we type in "Invoke-AtomicTest" and then we'll type in the technique ID which was 'T1136.001'. I'll hit "Enter".
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
So this will automatically generate telemetry based on creating a local account. I want you to take a look at the username that was created, which is "NewLocalUser". Now that our Command has finished running, we can go and look into Splunk and search specifically for "NewLocalUser".
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
We'll type in "index=endpoint NewLocalUser". So we don't get any events, that's kind of odd isn't it but in a way that's actually a good thing cuz this now tells you that you're blind to this activity. If an attacker compromised your system and created a local account with your current settings, you will not be able to detect that activity.
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
This is why I love Atomic Red Team. It will identify the gaps and visibility for you. It will also generate the telemetry to see if you can actually detect that activity. Let's do another example, we'll go into Mitre Attack and this time, I will select "Command and Scripting Interpreter".
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
So this one is 'T1059' and we do have a couple of them. We have one all the way up until seven. So I think that's all of the sub techniques there. We can expand that, and there's Powershell. Which I know for sure we should be able to see, so why don't we do that?
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
We'll execute that technique. So I'll clear out the screen. I'll type 'T1059.00' and hit "Enter". So Defender is catching some threats. We see a Powershell command here, "-exec bypass -noprofile". Perhaps that is something we can search for in Splunk. So let's go ahead and open up Splunk and take a look.
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
I'll change the time to "Last 15 minutes". Look at that, we do see a "NewLocalUser". So it just took some time, but the concept still applies. So we can detect that activity, however if you happen to see no events, even after waiting for a bit, then that means you have a gap in visibility.
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
Now, if we were to look at Powershell. Scrolling down we do see an event. It is quite hard to see, but I'll zoom in. We see "-exec bypass -noprofile $XmL" and if you recall in our Powershell window, that is this command right here. So this means that we can build alerts to detect on this activity in the future if we wanted to, along with creating a new local user account. Now, that we know that the event actually generates, it just takes a little time. 
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
Before we end, there is one last thing I wanted to talk about. That is with Kali Linux. In my example, I used "tsmith" with the password of this password. If you created a different account with a different password and you want to follow along, you need to make sure that your password is listed in the "passwords.txt" file.
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
Since as you can see at the bottom, my password is right there. So if your password to "tsmith" or to another user account was let's just say "summer2024!". Then, you need to make sure you put that into your "passwords.txt" file, otherwise this tool wouldn't be able to brute force successfully. So that's just one quick note.
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
If you're new to Virtual box and you don't know what a snapshot is, it's essentially a backup. So if I did happen to break something and my Kali Linux no longer works. For example, I can revert it back to a snapshot to a known good state. 
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
To take a snapshot, you simply click on "Machine" at the top and select "Take Snapshot". Then, you can create a name and a description and hit "OK". Now, to revert to a snapshot, there are a lot of different options. You can simply click on "close" and then just check "Restore Current snapshot". Then, that's it.
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
<br />
If you have followed along, you have successfully completed the active directory project and I hope that you had as much fun as I did creating this. We went over a ton of material and I know it can get pretty technical very quickly. I cannot stress this enough, if you want to become an amazing blue teamer, you must understand how Mitre Attack works. As this will provide you with some guidance on what an attacker might do, once they compromise in environment. It would mean a lot to me if you shared this project with other aspiring analysts who want to get some hands-on experience. Thank you so much for watching, and I truly hope that this has helped you in any way. Remember to stay curious and do things differently.
