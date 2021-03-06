## Requirements
Requires Python > 3.3

## Setup python server
### Requirements
	sudo apt install python3.5 python-pip python3-pip python3-mysql.connector
	echo https://cdn.mysql.com/Downloads/Connector-Python/mysql-connector-python-2.0.4.tar.gz >> requirements.txt
	sudo -H pip install -r ./requirements.txt
	sudo pip3 install hbmqtt

### Install
	git clone https://github.com/noah95/pro3-web
	cd pro3-web/server

Edit the settings in the file server.py
- url: mqtt server url
- fileroot: where the ekg files are placed on the server. Make sure the executing user has write access
- mysqlconfig user: database user
- mysqlconfig password: database password
- mysqlconfig host: database host
- mysqlconfig database: database name

Now we create the database structure. Replace root with your MySQL admin username
	
	mysql -u root -p < config/ekg3000.sql

Run the server
	
	python3.5 server.py

### Run script at boot
Change APPDIR, APPBIN and APPARGS in the file config/ekg3000initd.conf to match the location of the server.py file. The USER and GROUP variables should have read access to the server.py file.

	sudo cp config/ekg3000initd.conf /etc/init.d/ekg3000
	sudo chmod +x /etc/init.d/ekg3000
	sudo update-rc.d ekg3000 defaults
	sudo update-rc.d ekg3000 enable
	sudo service ekg3000 start

## Setup apache server for handling the ajaz requests
Make your web server to point to the pro3-web/server/ folder. For example:

	Listen 4280
	<VirtualHost *:4280>
	    ServerAdmin webmaster@localhost
	    DocumentRoot /home/ekg3000/pro3-web/server
	    ErrorLog ${APACHE_LOG_DIR}/ekg3000/error.log
	    CustomLog ${APACHE_LOG_DIR}/ekg3000/access.log combined
	    ServerName example.com
	    ServerAlias www.example.com
	</VirtualHost>

into /etc/apache2/sites-available/ekg3000.conf.

## Install mosquitto on rpi
	http://bohdan-danishevsky.blogspot.ch/2015/10/building-python-35-on-raspberry-pi-2.html

	wget http://repo.mosquitto.org/debian/mosquitto-repo.gpg.key
	sudo apt-key add mosquitto-repo.gpg.key
	cd /etc/apt/sources.list.d/
	sudo wget http://repo.mosquitto.org/debian/mosquitto-wheezy.list
	cd
	sudo apt-get update
	sudo apt-get install mosquitto
