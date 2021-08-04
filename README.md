# FlexFarmer As a Service
Run FlexFarmer as a service - written for Ubuntu Server
Your mileage (Distro) may vary

Visit [FlexPool.io](https://flexpool.io) to join the pool! (Note that I am in no way affiliated with FlexPool other than pointing my NFT's at their address in hopes of getting some mojos in return

Check [r/flexpool](https://www.reddit.com/r/Flexpool/) for a download link of FlexFarmer

Or - visit the [Discord](https://discord.gg/ck74hAum)

## **Before you get started, please cd into your flexfarmer directory and stay there for the duration**

# **Create the user and group that will run the service**

`sudo useradd -rms /bin/nologin flexfarmer`

# **Create the service**

`sudo vim /etc/systemd/system/flexfarmer.service`

# **Paste this into service file**

```
[Unit]
Description=https://github.com/F1DNA/FlexFarmerAsAService
After=network.target

[Service]
Type=simple
User=flexfarmer
Group=flexfarmer
ExecStart=/usr/local/bin/flexfarmer -c /home/flexfarmer/.config/flexfarmer/config.yml
StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=flexfarmer
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

If you are not familiar with VI/VIM, to save and exit type `:wq` and then press enter/return

# **Move (or copy) the flexfarmer python file into /usr/local/bin**

`sudo cp flexfarmer /usr/local/bin/flexfarmer`

# **Create the directories for the config.yml**

`sudo -u flexfarmer mkdir -m 0700 -p /home/flexfarmer/.config/flexfarmer`

# **Move (or copy) the config file**

`sudo cp config.yml /home/flexfarmer/.config/flexfarmer/config.yml`

# **Create the syslog config file**

`sudo vim /etc/rsyslog.d/flexfarmer.conf`

# **Past this into config file**

```
if $programname == 'flexfarmer' then /var/log/flexfarmer.log
& stop
```

If you are not familiar with VI/VIM, to save and exit type `:wq` and then press enter/return

# **Syslog needs to be able to write to this**

```
sudo touch /var/log/flexfarmer.log
sudo chown syslog:adm /var/log/flexfarmer.log
```

# **Create log rotation config file (Unless you like filling drives)**

`sudo vim /etc/logrotate.d/flexfarmer`

# **Paste in the following**

```
/var/log/flexfarmer.log {
    daily
    rotate 7
    copytruncate
    notifempty
    missingok
}
```
If you are not familiar with VI/VIM, to save and exit type `:wq` and then press enter/return

**Optional: you can change daily out for weekly or monthly and change the value of rotate to however many of those days, weeks or months you want to keep**

# **Restart rsyslog**

`sudo systemctl restart rsyslog`

# **Restart sysdemctl daemon to load your new service**

`sudo systemctl daemon-reload`

# **Time to make sure flex farmer is not running**

`ps aux | grep flexfarmer`

# **Use the PID from the output above**

![image](https://user-images.githubusercontent.com/61926834/128134430-72d57c29-a6f8-4398-ba1d-829675b1d736.png)
Note that it will show up at least twice because "flexfarmer" is in your grep command, you want the other one and the PID (Process ID) is what is circled here

`sudo kill <PID>` without the <>

# **Start your new service**

`sudo service flexfarmer start`

# **Verify it is running and there are no errors**

`service flexfarmer status`

![image](https://user-images.githubusercontent.com/61926834/128134704-1d616da2-721b-4c46-8151-ee0d4ee6a7e5.png)

Should show as active and the logs below should not be showing any errors.  If it is active but has errors, the service is setup properly and your config.yml probably has something wrong with it.  Don't be alarmed by the "Invalids" - look a little closer, no invalid plots, this is a good thing!

# **Enable auto start of service**

```
sudo systemctl enable flexfarmer.service
sudo systemctl start flexfarmer
```
# **Optional: Test autostart:**

`sudo shutdown -r now`

After reboot, do `service flexfarmer status` and see that everything is A'OK

# **So where is the output now?**

If you notice in the service file you created, you pointed it to syslog.  Then later, you told rsyslog to extract that information and send it to /var/log/flexfarmer.log

You can:

`cat /var/log/flexfarmer.log`

But that just grabs anything up until the moment you `cat` it

`tail -f /var/log/flexfarmer.log`

Ah, that's better, it is "following" the log now but I want to see more before I `tail` 'd it.  Okay, one more for you.

`tail -f -n +0 /var/log/flexfarmer.log`

This will show you the entire log plus continue to follow it.

Admittedly, syslog mucks things up a bit in regards to how pretty the output is from the FlexFarmer program.  There is an option in the config.yml to send that output to a log file of your choice if you prefer the pretty.

# **But how do I update FlexFarmer now?**

Download new bundle

`tar -xvzf flexfarmer-linux-*`

cd into flexfarmer directory

`sudo cp flexfarmer /usr/local/bin/flexfarmer`

Check the config example/templtates and compare to your existing config file should there be anything new to add to your config file, if there is then:

`sudo vim /home/flexfarmer/.config/flexfarmer/config.yml`

Then restart the service:

`sudo systemctl service restart flexfarmer.service`


# **Thanks to u/rnovak for revieweing, making a few suggestions and testing!**

  Please visit his blog - [Robert Novak on system administration](https://rsts11.com/)
  You can also find him in the FlexPool.io Discord under the name @thetasigma06 - please don't tag him, just checkout the [Chia Channel](https://discord.gg/TJhbFP7Y) and join the convo, he'll see you!
  
Speakering of Robert, he had a great idea to add to this:

## **BONUS: Have more than 1 NFT pointed to FlexPool?**

When you create the flexfarmer.service file, consider something like this:

```
NFT#1 - flexNFTXXX.service
NFT#2 - flexNFTZZZ.service
```

Where you somehow differentiate between your 2x NFT's - maybe last 6 of the Launcher ID or something?  I'd advise againts flexNFT1.service and the like cause then you have to reference something else to know which one you need.

Then, and this should be obvious but...  you will need 2x config.yml files so prettymuch same thing, nftXXXconfig.yml with all the matching "stuff" inside - be sure when creting the service files, you update the ExecStart= line to point to the correct config file for that NFT.
  
  
**Should you feel compelled, you may send a tip in the form of XCH to:**

xch1u2jhwq9q37v822ejvjytu289th4wad8azuyu7m0gmjuzzzd76dkqx06phs

