# DevOps Ninja

This is a collection of some very useful command-line commands that eases the life of a DevOps Engineer.  
Please Feel free to fork and/or PR if you have any additions.

**Checking ports**

- Show port and PID - `netstat -tulpn`
- Show process and listening port - `ss -ltp`
- Show ports that are listening - `ss -ltn`
- Show real time TCP and UDP ports - `ss -stplu`
- Show all established connections `lsof -i`
- Show listening connections - `lsof -ni | grep LISTEN`

**Check a public IP**

- `curl http://whatismyip.org/`
- `curl ifconfig.me`
- `curl icanhazip.com`

**Return the IP of an interface**

- `ifconfig en0 | grep --word-regexp inet | awk '{print $2}'`
- `ip add show eth0 | awk '/inet/ {print $2}' | cut -d/ -f1 | head -1`
- `ip -br a sh eth0 | awk '{ print $3 }'` (returns netmask)
- `ip route show dev eth0 | awk '{print $7}'`
- `hostname -I` (return ip only)

**Replace all occurrences of string in a directory**

- Find and replace string - `grep -rl "oldstring" ./ | xargs sed -i "" "s/oldstring/newstring/g"`

**Dig**

- Check domain with specific NS - `dig <domain.com> @<ns-server>`
- Get NS records for a site - `dig <domain.com> ns`

**Disk checks**

- Sort disk usage by most first - `df -h | tail -n +2 | sort -rk5`
- Check the size of a top level dicectory - `du -h --max-depth=1 /tmp/`
- Top 50 file sizes - `du -ah / | sort -n -r | head -n 50`
- Show directory sizes (must not be in root directory) - `du -sh *`
- Check disk usage per directory - `du -h <dir> | grep '[0-9\.]\+G’`
- Look for growing directories - `watch -n 10 df -ah`
- Ncurses based disk usage - `ncdu -q`
- Colorized output of du - `du -x --max-depth=1|sort -rn|awk -F / -v c=$COLUMNS 'NR==1{t=$1} NR>1{r=int($1/t*c+.5); b="\033[1;31m"; for (i=0; i<r; i++) b=b"#"; printf " %5.2f%% %s\033[0m %s\n", $1/t*100, b, $2}'|tac`

**Docker**

- Dokcer resources usage - `docker info`
- know how much space is taken by a particular container `docker container ls -s`
- Know how much spaces is used by Docker Root Dir `du -h --max-depth=1 /var/lib/docker`
- Docker storage usage `docker system df`
- Docker list volumes `docker volume ls`
- Docker list images that are locally stored with the Docker Engine `docker image ls`
- Docker inspect volumes `docker volume inspect VOLUME NAME`
- Remove a group of images - `docker images | grep "<none>" | awk '{print $3}' | xargs docker rmi`
- Remove all untagged containers - `docker rm $(docker ps -aq --filter status=exited)`
- Remove all untagged images - `docker rmi $(docker images -q --filter dangling=true)`
- Remove old (dangling) Docker volumes - `docker volume rm $(docker volume ls -qf dangling=true)`
- Docker remove redundant objects at once `docker system prune`
- Install on Ubuntu - `curl -sSL https://get.docker.com/ubuntu/ | sudo sh`
- Get stats from all containers on a host - `docker ps -q | xargs docker stats`
- Tail last 300 lines of logs for a container - `docker logs --tail=300 -f <container_id>`
- Build an image from the Dockerfile in thecurrent directory and tag the image `docker build -t myimage:1.0 .`
- Pull an image from a registry `docker pull myimage:1.0`
- Retag a local image with a new image name and tag `docker tag myimage:1.0 myrepo/myimage:2.0`
- Push an image to a registry `docker push myrepo/myimage:2.0`
- Run a container from the Alpine version 3.9 image, name the running container “web” and expose port 5000 externally, mapped to port 80 inside the container `docker container run --name web -p 5000:80 alpine:3.9`
- Stop a running container through SIGTERM `docker container stop web`
- Stop a running container through SIGKILL `docker container kill web`
- List the networks `docker network ls`
- Shell Script to Install Docker on Ubuntu

  ```sh
  #!/bin/bash
  set -e
  #Uninstall old versions
  sudo apt-get remove docker docker-engine docker.io containerd runc
  #Update the apt package index:
  sudo apt-get update
  #Install packages to allow apt to use a repository over HTTPS:
  sudo apt-get install -y \
      apt-transport-https \
      ca-certificates \
      curl \
      gnupg-agent \
      software-properties-common
  # Add docker's package signing key
  curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
  # Add repository
  sudo add-apt-repository -y \
    "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
    $(lsb_release -cs) \
    stable"
  # Install latest stable docker stable version
  sudo apt-get update
  sudo apt-get -y install docker-ce
  # Enable & start docker
  sudo systemctl enable docker
  sudo systemctl start docker
  # add current user to the docker group to avoid using sudo when running docker
  sudo usermod -a -G docker $USER
   # Output current version
  docker -v
  ```

- Shell Script to Install Docker on Centos

  ```sh
     #!/bin/bash
     #Get Docker Engine - Community for CentOS + docker compose
     set -e
     #Uninstall old versions
     sudo yum remove docker docker-common docker-selinux docker-engine-selinux docker-engine docker-ce
     #Update the packages:
     sudo yum update -y
      #Install needed packages
     sudo yum install -y yum-utils device-mapper-persistent-data lvm2
  # Configure the docker-ce repo:
  sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
  # Install the latest docker-ce
  sudo yum install docker-ce
  # Enable & start docker
  sudo systemctl enable docker.service
  sudo systemctl start docker.service
  # add current user to the docker group to avoid using sudo when running docker
  sudo usermod -a -G docker $(whoami)
  # Output current version
  docker -v
  ```

- Shell Script to Install Docker on AWS linux

  ```sh
      #!/bin/bash
      #Get Docker Engine - Community for CentOS + docker compose
      set -e
      #Uninstall old versions
      sudo yum remove docker docker-common docker-selinux docker-engine-selinux docker-engine docker-ce
      #Update the packages:
      sudo yum update -y
      #Install the most recent Docker Community Edition package.
      sudo amazon-linux-extras install docker -y
      # Enable & start docker
      sudo service docker start
      # add current user to the docker group to avoid using sudo when running docker
      #sudo usermod -a -G docker ec2-user
      sudo usermod -a -G docker $(whoami)
      # Output current version
       docker -v
  ```

**Docker Compose**

- Shell Script to Install the latest version of docker-compose

  ```sh
  #!/bin/bash
  # get latest docker compose released tag
  COMPOSE_VERSION=$(curl -s https://api.github.com/repos/docker/compose/releases/latest | grep 'tag_name' | cut -d\" -f4)
  sudo curl -L "https://github.com/docker/compose/releases/download/${COMPOSE_VERSION}/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
  sudo chmod a+x /usr/local/bin/docker-compose
  sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
  # Output the  version
  docker-compose -v
  ```

**Find**

- Exlcude directories in find - `find /tmp -not \( -path /tmp/dir -prune \) -type p -o -type b`

**Git**

- Remove deleted files from repo - `git rm $(git ls-files --deleted)`
- Reset git repo (dangerous) - `git reset --hard HEAD`
- Reset and remove untracked changes in repo - `git clean -xdf`
- Ignore certificates when cloning via HTTPS - `git config --global http.sslVerify false`
- Pull changes and remove stale branches - `git pull --prune`
- Grab the diff of a previous version of a file - `git diff HEAD@{1} ../../production.hosts`
- Grab the diff of a staged change - `git diff --cached <file>`
- Undo a commit to a branch - `git reset --soft HEAD~1`
- View files changed in a commit - `git log --stat`
- Pull latest changes stashing changes first - `git pull --autostash`
- Make an empty commit (good for CI) - `git commit --allow-empty -m "Trigger notification"`

**Grep**

- Look through all files in current dir for word “foo” - `grep -R "foo” .`
- View last ten lines of output - `grep -i -C 10 "invalid view source” /var/log/info.log`
- Display line number of message - `grep -n “pattern” <file>`

**Iptables**

- Check nat rules for ip redirection - `iptables -nvL -t nat`

**Apache**

- Turn off “ServerSignature” and “ServerToken” on Apache

  - Edit httpd.conf or apache2.conf

  ```sh
  // Kali, Debian, Ubuntu  Linux Mint
    sudo vi /etc/apache2/apache2.conf
  // CentOS, Fedora, RHEL , Arch Linux
   sudo vi /etc/httpd/conf/httpd.conf
  ```

  - Add below settings to the file

  ```sh
     ServerSignature Off
     ServerTokens Prod
  ```

  - Restart Apache

  ```sh
  // Kali, Debian, Ubuntu Linux Mint
     sudo service apache2 restart
  //Fedora, CentOS/RHEL 7,Arch Linux
   systemctl restart httpd.service
  ```

**Nginx**

- Check installed modules - `nginx -V`
- Pretty print installed modules - `2>&1 nginx -V | xargs -n1`
- Test a configuration without reloading - `nginx -t`
- Stop all nginx processes - `nginx -s stop`
- Start all nginx processes - `nginx -s start`
- Restart all nginx processes - `nginx -s restart`
- Realod nginx configuration (without restarting) - `nginx -s reload`

**Tomcat**

- Hide tomcat stack traces (showReport) as well as the Server info:
  Add below lines at the HOST section

  ```sh
  <Valve className="org.apache.catalina.valves.ErrorReportValve"
  showReport="false"
  showServerInfo="false" />
  ```

* Injecting HTTP Response with the secure header in Tomcat

  ```sh

  <filter>
      <filter-name>httpHeaderSecurity</filter-name>
      <filter-class>org.apache.catalina.filters.HttpHeaderSecurityFilter</filter-class>
      <async-supported>true</async-supported>
      <init-param>
        <param-name>antiClickJackingEnabled</param-name>
        <param-value>true</param-value>
      </init-param>
      <init-param>
        <param-name>antiClickJackingOption</param-name>
        <param-value>DENY</param-value>
      </init-param>
      </filter>
      <filter-mapping>
      <filter-name>httpHeaderSecurity</filter-name>
      <url-pattern>/*</url-pattern>
      </filter-mapping>
      <welcome-file-list>
    <welcome-file>index.html</welcome-file>
      </welcome-file-list>
  ```

**Nmap**

- Check single port on single host - `nmap -p <port> <host/IP>`
- Intrusive port scan on a single host - `nmap -sS <host/IP>`
- Top ten port on a single host - `nmap --top-ports 10 <host/IP>`

**Password generation**

- Create hash from password - `openssl passwd -crypt <password>`
- Generate random 8 character password (Ubuntu) - `makepasswd -count 1 -minchars 8`
- Create .passwd file with user and random password - `sudo htpasswd -c /etc/nginx/.htpasswd <user>`

**Removing files**

- Remove files over 30 days old - `find . -mtime +30 | xargs rm -rf`
- Remove files older than 7 day starting with 'backup' - `find . -type f -name "backup*" -mtime +7 -exec rm {} \;`

**SSH**

- Generate generic ssh key pair - `ssh-keygen -q -t rsa -f ~/.ssh/<name> -N '' -C <name>`
- AWS PEM key to ssh PUB key : ` ssh-keygen -y -f eliarms.pem > eliarms.pub

**Tail log with colored output**

- `grc tail -f /var/log/filename`

**Tmux**

- Kill a window - `tmux kill-window -t 0`
- Kill stuck tmux window - `tmux kill-window -t X`
- Create a new session <name> - `tmux new -s <name>`
- List all sessions - `tmux ls`

**ps**

- Show process tree of all PIDs - `ps auxwf`
- Show all process info and hierarchy (same as above)- `ps -efH`
- Show orphaned processes for <use> - `ps -ef|awk '$3=="1" && /pandora/ { print $2 }'`
- Show all orphaned processes (could be daemons) - `ps -elf | awk '{if ($5 == 1){print $4" "$5" "$15}}'`
- Show zombie processes - `ps aux | grep Z`

**HSTS**

- Enable HTTP Strict Transport Security protocol in Tomcat
  To enable HSTS in Tomcat, follow these steps:

  - Open the <Tomcat>/conf/web.xml file in a text editor.
  - Uncomment the httpHeaderSecurity filter definition and the <filter-mapping> section, and then add the hstsMaxAgeSeconds parameter, as shown below.

    ```sh
    <filter>
        <filter-name>httpHeaderSecurity</filter-name>
    <filter-class>org.apache.catalina.filters.HttpHeaderSecurityFilter</filter-class>
    <init-param>
      <param-name>hstsMaxAgeSeconds</param-name>
      <param-value>31536000</param-value>
        </init-param>
        <async-supported>true</async-supported>
        </filter>
        <filter-mapping>
        <filter-name>httpHeaderSecurity</filter-name>
        <url-pattern>/*</url-pattern>
        <dispatcher>REQUEST</dispatcher>
        </filter-mapping>
    ```

  - Save the file
  - Restart Tomcat

* Enable HTTP Strict Transport Security protocol in Apache
  To enable HSTS in Tomcat, follow these steps:

  - Open the <Apache>/conf/httpd.conf file in a text editor.

  - Uncomment the header module:
    LoadModule headers_module modules/mod_headers.so
  - Add a header setting in the VirtualHost section:

    ```sh
    <VirtualHost www.example.com:80>
    Header always set Strict-Transport-Security "max-age=63072000; includeSubdomains; preload"
    </VirtualHost>
    ```

- Enable HTTP Strict Transport Security protocol in IIS
  To enable HSTS in IIS, do the following:

  - Add a Strict-Transport-Security header to the web.config file under the IIS installation root directory:

    ```sh
        <system.webServer>
        <httpProtocol>
        <customHeaders>
         <add name="Strict-Transport-Security" value="max-age=31536000"/>
         </customHeaders>
        </httpProtocol>
        </system.webServer>
    ```

  - Restart IIS

- Permissions
  - Set permissions recursively on a dir (with ACL enabled)
    ```sh
    setfacl -R -m u:userid:rwx foldername
    ```
- JAVA
  - Completly remove Openjdk from redhat
    ```sh
     rpm -qa | grep openjdk | xargs  yum -y remove
    ```
