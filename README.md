# Minecraft-Server
Minecraft Server using Raspberry PI 4, Ubuntu and Docker

Based on this [video](https://www.youtube.com/watch?v=eIHiRW4QH6I&t=1015s)

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


# Setting Up The Server 

To verify that Docker is installed and running, you can run:

```
sudo docker --version
```
