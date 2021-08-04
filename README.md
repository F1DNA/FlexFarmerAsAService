# FlexFarmer As a Service
Run FlexFarmer as a service - written for Ubuntu Server
Your mileage (Distro) may vary

Visit [FlexPool.io](https://flexpool.io) to join the pool!


# **Create the user and group that will run the service**

`sudo useradd -rms /bin/nologin flexfarmer`

# **Create the service**

`sudo vim /etc/systemd/system/flexfarmer.service`

# **Paste this into service file and :wq + enter to save it**

```
[Unit]
Description=FlexFarmer as a service - F1DNA
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

# **Move (or copy) the flexfarmer python file into /usr/local/bin**

`sudo cp flexfarmer /usr/local/bin/flexfarmer`

# **Create the directories for the config.yml**

`sudo -u flexfarmer mkdir -m 0700 -p /home/flexfarmer/.config/flexfarmer`

**Move (or copy) the config file into usr/local/bin**

sudo cp config.yml /home/flexfarmer/.config/flexfarmer/config.yml

Create the syslog config file

sudo vim /etc/rsyslog.d/flexfarmer.conf

Past this into config file and :wq + enter to save it

if $programname == 'flexfarmer' then /var/log/flexfarmer.log
& stop

Syslog needs to be able to write to this

sudo touch /var/log/flexfarmer.log
sudo chown syslog:adm /var/log/flexfarmer.log

Restart rsyslog

sudo systemctl restart rsyslog

Restart sysdemctl daemon to load your new service

sudo systemctl daemon-reload

Time to make sure flex farmer is not running

ps aux | grep flexfarmer

Use the PID from the output above

sudo kill <PID>

Start your new service

sudo service flexfarmer start

Verify it is running and there are no errors

service flexfarmer status

Enable auto start of service

sudo systemctl enable flexfarmer.service
sudo systemctl start flexfarmer

How to update

Download new bundle
tar -xvzf flexfarmer-linux-*
cd into flexfarmer directory
sudo cp flexfarmer /usr/local/bin/flexfarmer
Check the config example/templtates and compare to your existing config file incase there are any adjustments that should be made and make them accordingly:
sudo vim /home/flexfarmer/.config/flexfarmer/config.yml
sudo systemctl service restart flexfarmer.service

Thanks to u/rnovak for revieweing, making a few suggestions and testing!
  Please visit his blog - [Robert Novak on system administration](https://rsts11.com/)
  
  ![image](https://user-images.githubusercontent.com/61926834/128132572-577c0d7c-f879-4677-88a6-02347bd83a83.png)

  
  
