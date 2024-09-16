# Dev setup for cal.com in a proxmox LXC
#### In an effort to help out others, I am sharing my steps to set up Cal.com in a Proxmox LXC (or) Debian 12 Enviornment.

For now the steps to reproduce, but I'm sure once we get this tested we can turn it into a build script. For now I'll make is as copy/paste able as possible.

**1. Setup your LXC: **(The yarn build process is a little demanding so make sure you give it enough resources to start. You can trim it down after.)
- I used the Template for debian-12-standard_12.2-1_amd64.tar.zst
  - Recommending minium of:
    - Disk Size: It will use up 10GB right from the start so 16->32GB?
    - 4 Cores
    - 4GB Ram
    - 1GB Swap

In the console: (sudo not needed at console)     
```bash
apt update
apt upgrade
```
Add repositoy for yarn:     
```bash
curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add - echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list
```
Add repositoy for Docker:
```bash
apt-get install ca-certificates curl
install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
chmod a+r /etc/apt/keyrings/docker.asc
echo   "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian \ $(. /etc/os-release && echo "$VERSION_CODENAME") stable" |   tee /etc/apt/sources.list.d/docker.list > /dev/null
apt update
apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```
Test if you want...
```bash
docker run hello-world
```
Install remaining requirements:
```bash
apt install postgresql
apt install yarn
apt install git
```
Now you can begin setting up cal.com
```bash
git clone https://github.com/calcom/cal.com.git
cd cal.com
yarn
```
> Use openssl rand -base64 32 to generate a key and add it under NEXTAUTH_SECRET in the .env file.
    Use openssl rand -base64 32 to generate a key and add it under CALENDSO_ENCRYPTION_KEY in the .env file.    Use openssl rand -base64 32 to generate a key and add it under CALENDSO_ENCRYPTION_KEY in the .env file.
	
.env.example is a hidden file but it's there.  See it with "ls -a"
```bash
cp .example.env .env
openssl rand -base64 32
openssl rand -base64 32
nano .env
```
Add the Values then run:
```bash
    yarn dx
```
Set up first user:
```bash
    yarn db-studio
```
Edit one of the dummy users. I did Owner 1 (ID 16) 
Encrypt your password with BCrypt https://bcrypt-generator.com/

Log in...

