# TweetMachine

Read [this post](https://blog.chiodini.org/2015/04/26/tweetmachine.html) to understand what Tweetmachine is.

The following paragraph describes software setup.

### Step by step guide
This guide assumes you're using a Debian-like distribution (e.g., Raspbian). However, it should be easily adaptable for other Linux distributions.

This guide also assumes that the current working directory is your home (`~`). If you're using a different directory, please adapt some of the following commands.

- Install git

        sudo apt-get install git 
        
- Install i2c-tools (useful, for instance, to run ```i2cdetect```) and the I2C development library (needed by wiringPi at build time)

        sudo apt-get install i2c-tools libi2c-dev
        
- Edit the kernel modules

        sudo nano /etc/modules
        
  Paste these lines at the end of the file
  
        i2c-bcm2708 
        i2c-dev

- Edit ```/etc/modprobe.d/raspi-blacklist.conf```

        sudo nano /etc/modprobe.d/raspi-blacklist.conf
        
  and comment the two following lines:
  
        # blacklist spi-bcm2708
        # blacklist i2c-bcm2708

- Edit ```/boot/config.txt```

        sudo nano /boot/config.tx
        
  Paste these lines at the end of the file

        dtparam=i2c1=on
        dtparam=i2c_arm=on
        
- Reboot (kernel should now load i2c modules by default at startup)

        sudo reboot

- Download wiringPi library and tweetmachine software

        git clone git://git.drogon.net/wiringPi
        git clone https://github.com/lucach/tweetmachine

- Apply a custom patch to wiringPi library

        cd ~/wiringPi
        git apply ~/tweetmachine/0001-Add-wiringPiI2CWriteBlock-function.patch

- Build wiringPi

        ./build

- Install python2 and tweepy

        sudo apt-get install python2.7 python-pip
        sudo pip install tweepy

- Build tweetmachine software

        cd ~/tweetmachine
        ./build

- Inside `download.py` change these values with your [Twitter Developers](https://dev.twitter.com) app keys:
  - CONSUMER_KEY
  - CONSUMER_SECRET
  - ACCESS_TOKEN
  - ACCESS_TOKEN_SECRET
  
  Be careful and *double check* the keys you've inserted. A single wrong character is enough to break the whole application.

- Inside `download.py` change the `HASHTAG` you want to follow. Here you can customize other parameters (such as the number of tweets retrieved each time and the name of the output file).

- Start `download.py` in background. The easiest (but not very flexible) way:

        ./download.py &

- Start `program` (which reads a file and sends data to the Flashmat matrices)

        ./program 1 output.txt
