# Minecraft-Server
Minecraft Server Tutorial using Raspberry PI 4, Ubuntu and Docker

Should work on any system running a Debian based Linux Distro.

Mostly based on this [video](https://www.youtube.com/watch?v=eIHiRW4QH6I&t=1015s).

# Installing Ubuntu Server
## Option 1 
You can either install Ubuntu Server ISO using the official website [https://ubuntu.com/download/server](https://ubuntu.com/download/server) and then use a flasher like [Balena Etcher](https://etcher.balena.io/) to download to an SSD card or USB Drive.

## Option 2 (Recommended for RaspberryPi)
Using the [RaspberryOS Imager](https://www.raspberrypi.com/software/) you can download the Ubuntu Server ISO directly to the SD card. 

To install you use the following command

```bash
sudo apt install rpi-imager
```

Using the imager you can select device, OS Version, and SD storage.

![image](https://github.com/user-attachments/assets/ef6892b2-459a-4a69-95ee-303ccfb01c95)

You can also set up Login, Password, Wifi Network and enable SSH before transfering the image to the SD card.


After booting up the new OS you have two options, using the Raspberry Pi with a monitor and keyboard or using it remotely via SSH.

If you wish to use SSH, you need to find the Raspberry's Local IP Address, you can do that by accessing your local network router.
Then you can connect using the following command. P.S: raspberrypi is the login you defined on the Imager setup.

```bash
ssh raspberrypi@192.xxx.x.xx
```



# Installing Docker 

Step 1: Update your system

Open a terminal and ensure your package index is up to date:

```bash
sudo apt update
sudo apt upgrade -y
```

Step 2: Install required packages

Install the packages needed for Docker installation:

```
sudo apt install apt-transport-https ca-certificates curl software-properties-common -y

```
Step 3: Add Docker's official GPG key

Add Docker's official GPG key to ensure downloaded packages are genuine:

```
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -
```

Step 4: Add the Docker repository

Add the Docker repository to your APT sources:

```
echo "deb [arch=amd64] https://download.docker.com/linux/debian $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list
```

Step 5: Update the package index again

After adding the Docker repository, update the package index again:

```
sudo apt update
```

Step 6: Install Docker

Now you can install Docker:

```
sudo apt install docker-ce -y
```

Step 7: Start and enable Docker

Start the Docker service and enable it to launch at startup:

```
sudo systemctl start docker
sudo systemctl enable docker
```

Step 8: Install Docker Compose

```
sudo apt install docker-compose
```

Step 9: Verify the installation

To verify that Docker is installed and running, you can run:

```
sudo docker --version
```



# Setting Up The Server

Create a new directory for the new container 


```
mkdir mc-docker
```

### Docker Run Command

Paste this command to run the container

```
sudo docker run \
  -d \
  -it \
  --name mcserver \
  --restart=unless-stopped \
  -e MEMORYSIZE="2G" \
  -p 25565:25565 \
  -p 25575:25575 \
  -v /home/luciano/mc-docker:/data:rw \
  -i  marctv/minecraft-papermc-server:1.21.3-34
```

You can change this command as you need, here you define container name, allocated RAM, ports and game version.
See supported game versions [here](https://hub.docker.com/r/marctv/minecraft-papermc-server/tags?page=2).


After running the container, log into Minecraft, select Multiplayer->Add Server and enter the Local RaspberryPi Address. You should be able to see the server online. 
P.S: Only people in the same network can join during this step.

### Cracked Accounts 
If you are using a cracked launcher like TLauncher, you should change the server properties file.

go to mc-docker directory and edit the server.properties, using (sudo) nano or vi.

set

```
online_mode=false
```

## Useful Commands 

Start Container
```
sudo docker start mcserver
```

Stop Container (Use before shutting down the computer)
```
sudo docker stop mcserver
```

Remove Container
```
sudo docker rm mcserver
```

See Logs
```
sudo docker logs mcserver
```

# Enabling External Players 

The correct way to allow external players to join is to use Port Forwading, but since your network provider may not allow that, and my goal here is to play with friends we will set up a virtual network using ZeroTier.


### ZeroTier
You should download ZeroTier on you RaspberryPi

**Step 1: Install ZeroTier Using the Official Repository**
Download the ZeroTier installation script:

```
curl -s https://install.zerotier.com | sudo bash
```

This will automatically install the correct version of ZeroTier for your system.

Verify the installation:

After installation, check if ZeroTier is installed correctly by running:

```
zerotier-cli --version
```


**Step 2: Join a ZeroTier Network**

Once ZeroTier is installed, follow the steps from the previous message to create a network and join it.

1.Create a ZeroTier account and create a network at [ZeroTier Central](https://my.zerotier.com/).

2.Join the network on your Ubuntu server:

```
sudo zerotier-cli join <your-network-id>
```

3.Authorize your server on the ZeroTier dashboard.

**Step 3: Authorize Your Device in the ZeroTier Dashboard**

    Go to the ZeroTier Central dashboard.
    Select the network you created.
    You should see your server listed under "Members" with a status of "Offline" or "Unmanaged" until it’s authorized.
    Authorize your server by checking the box next to the device and clicking the "Save" button.

**Step 4: Find Your ZeroTier IP Address**

Your server now has a ZeroTier IP address, which your friends will use to connect to your Minecraft server.

To find the ZeroTier IP address of your server:

sudo zerotier-cli listnetworks

Look for the IP address in the 10.x.x.x range (this is the ZeroTier virtual network address).


**Step 5: Change server.properties**

edit the server.properties file in the mc-docker directory

set 
```
server-ip=0.0.0.0
```

this way the server will use both the virtual and local IPs.

**Step 6: Friends**

Your friend should now install ZeroTier on their Operating System and request to join your network.
They should enter Minecraft->Multiplayer->Add Server, insert the RaspberryPi virtual IP. 
They should now be able to enter your server.


# BONUS: Saving Map Files

Create another Github repository to keep the file saves (i dont recomend forking this one to keep things organized).

On the raspberrypi create a backups folder 

```
mkdir backups
cd backups
```

inside backups folder, clone the Github repository you just created for the map files.
```
git clone https://github.com/user/insert_repo_name/.../ .
```
Now everytime you want to save the map you should follow this routine:
```
cd ~/backups

git pull origin master

cd ~/mc-docker

sudo cp -r ~/mc-docker/world ~/backups/
sudo cp -r ~/mc-docker/world_nether ~/backups/
sudo cp -r ~/mc-docker/world_the_end ~/backups/

cd ~/backups

sudo git add . 
sudo git commit -m "Backup"

sudo git push origin master
```
