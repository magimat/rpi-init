# rpi-init



## Raspbian clean image

https://www.raspberrypi.org/downloads/raspbian/

Raspbain stretch lite (no desktop)

PiFiller (http://ivanx.com/raspberrypi/)


## first login 

ssh pi@raspberrypi (192.168.11.202)

user: pi  default pw: raspberry

passwd pour changer le mot de passe


## Alias ll

```
vi .bashrc
```
uncomment line wiht: alias ll='ls -l' 
     	
ou:
```
sudo sed -i "s/#alias ll='ls -l'/alias ll='ls -l'/g" .bashrc
```


## ssh key
		
(https://www.raspberrypi.org/documentation/remote-access/ssh/passwordless.md)
```
ssh-keygen (default name, empty passphrase)
cat ~/.ssh/id_rsa.pub
```
saisir la clé sur github:  https://github.com/settings/keys


## symlink usb 

```
sudo vi /etc/udev/rules.d/99-usb.rules 
KERNELS=="1-1.4:1.0", SUBSYSTEM=="tty", SYMLINK+="insteonPLM"
```


## git 

```
sudo apt-get install git
git config --global user.email "mat.girard@gmail.com"
git config --global user.name "Mathieu Girard"
```

## Install latest node
		
https://thisdavej.com/beginners-guide-to-installing-node-js-on-a-raspberry-pi/#install-node
```
curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -
sudo apt install -y nodejs
```


## Docker

```
curl -fsSL get.docker.com -o get-docker.sh && sh get-docker.sh
sudo gpasswd -a $USER docker
newgrp docker
docker swarm init
docker login  (u: magimat p:*****)
```


## portainer

```
docker volume create portainer_data
docker run -d -p 9000:9000 --name portainer --restart always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer:arm
```


## samba

```
git clone git@github.com:magimat/rpi-samba.git
cd rpi-samba/
docker build --no-cache -t magimat/rpi-samba .
docker push magimat/rpi-samba

docker run --name rpi-samba -d --restart always -v $PWD:/data/share -p 445:445 -p 139:139 -p 137:137/udp -p 138:138/udp magimat/rpi-samba
```


## matsteon
	
```
cd ~
git clone git@github.com:magimat/Matsteon.git
cd Matsteon
docker build -t magimat/matsteon .
docker push magimat/matsteon

docker run -p 3000:80 --name matsteon --device=/dev/insteonPLM -d --restart unless-stopped magimat/matsteon
```


## matleds

```
cd ~
git clone git@github.com:magimat/Matleds.git
cd Matleds
docker build -t magimat/matleds .
docker push magimat/matleds

docker run -p 5000:80 --name matleds -d --restart unless-stopped magimat/matleds
```



## matir

```
cd ~
git clone git@github.com:magimat/Matir.git
cd Matir
docker build -t magimat/matir .
docker push magimat/matir

docker run --network host --name matir -d --restart unless-stopped magimat/matir
```
--network host pour que le discover fonctionne, port 4000 directement dans le js



## mqtt-bridge & mosca mqtt server

checkout repo
```
cd ~
git clone git@github.com:magimat/mqtt-bridge-compose.git
```

build image mosca
```
cd mqtt-bridge-compose/mosca
docker build -f Dockerfile-rpi -t magimat/mosca .
docker push magimat/mosca
```

build image mqtt-bridge
```
cd mqtt-bridge-compose/smartthings-mqtt-bridge
docker build -f Dockerfile-rpi -t magimat/smartthings-mqtt-bridge .
docker push magimat/smartthings-mqtt-bridge
```


start compose mqtt
```
cd mqtt-bridge-compose
docker stack deploy -c docker-compose.yml mqtt-bridge-service
```
