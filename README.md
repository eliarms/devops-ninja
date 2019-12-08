# DevOps Ninja

This is a collection of some very useful command-line commands that eases the life of a DevOps Engineer.  
Please Feel free to fork and/or PR if you have any additions.

**Checking ports**

 * Show port and PID - `netstat -tulpn`
 * Show process and listening port - `ss -ltp`
 * Show ports that are listening - `ss -ltn`
 * Show real time TCP and UDP ports - `ss -stplu`
 * Show all established connections `lsof -i`
 * Show listening connections - `lsof -ni | grep LISTEN`

**Check a public IP**

 * `curl http://whatismyip.org/`
 * `curl ifconfig.me`
 * `curl icanhazip.com`

**Return the IP of an interface**

 * `ifconfig en0 | grep --word-regexp inet | awk '{print $2}'`
 * `ip add show eth0 | awk '/inet/ {print $2}' | cut -d/ -f1 | head -1`
 * `ip -br a sh eth0 | awk '{ print $3 }'` (returns netmask)
 * `ip route show dev eth0 | awk '{print $7}'`
 * `hostname -I` (return ip only)
 
**Replace all occurrences of string in a directory**

 * Find and replace string - `grep -rl "oldstring" ./ | xargs sed -i "" "s/oldstring/newstring/g"`

**Dig**

 * Check domain with specific NS - `dig <domain.com> @<ns-server>`
 * Get NS records for a site - `dig <domain.com> ns`

**Disk checks**

 * Sort disk usage by most first - `df -h | tail -n +2 | sort -rk5`
 * Check the size of a top level dicectory - `du -h --max-depth=1 /tmp/`
 * Top 50 file sizes - `du -ah / | sort -n -r | head -n 50`
 * Show directory sizes (must not be in root directory) - `du -sh  *`
 * Check disk usage per directory - `du -h <dir> | grep '[0-9\.]\+G’`
 * Look for growing directories - `watch -n 10 df -ah`
 * Ncurses based disk usage - `ncdu -q`
 * Colorized output of du - `du -x --max-depth=1|sort -rn|awk -F / -v c=$COLUMNS 'NR==1{t=$1} NR>1{r=int($1/t*c+.5); b="\033[1;31m"; for (i=0; i<r; i++) b=b"#"; printf " %5.2f%% %s\033[0m %s\n", $1/t*100, b, $2}'|tac`

**Docker**

 * Remove a group of images - `docker images | grep "<none>" | awk '{print $3}' | xargs docker rmi`
 * Remove all untagged containers - `docker rm $(docker ps -aq --filter status=exited)`
 * Remove all untagged images - `docker rmi $(docker images -q --filter dangling=true)`
 * Install on Ubuntu - `curl -sSL https://get.docker.com/ubuntu/ | sudo sh`
 * Get stats from all containers on a host - `docker ps -q | xargs docker stats`
 * Tail last 300 lines of logs for a container - `docker logs --tail=300 -f <container_id>`
 * Remove old (dangling) Docker volumes - `docker volume rm $(docker volume ls -qf dangling=true)`

**Find**

 * Exlcude directories in find - `find /tmp -not \( -path /tmp/dir -prune \) -type p -o -type b`

**Git**

 * Remove deleted files from repo - `git rm $(git ls-files --deleted)`
 * Reset git repo (dangerous) - `git reset --hard HEAD`
 * Reset and remove untracked changes in repo - `git clean -xdf`
 * Ignore certificates when cloning via HTTPS - `git config --global http.sslVerify false`
 * Pull changes and remove stale branches - `git pull --prune`
 * Grab the diff of a previous version of a file - `git diff HEAD@{1} ../../production.hosts`
 * Grab the diff of a staged change - `git diff --cached <file>`
 * Undo a commit to a branch - `git reset --soft HEAD~1`
 * View files changed in a commit - ` git log --stat`
 * Pull latest changes stashing changes first - `git pull --autostash`
 * Make an empty commit (good for CI) - `git commit --allow-empty -m "Trigger notification"`

**Grep**

 * Look through all files in current dir for word “foo” - `grep -R "foo” .`
 * View last ten lines of output - `grep -i -C 10 "invalid view source” /var/log/info.log`
 * Display line number of message - `grep -n “pattern” <file>`

**Iptables**

 * Check nat rules for ip redirection - `iptables -nvL -t nat`

**Nginx**

 * Check installed modules - `nginx -V`
 * Pretty print installed modules - `2>&1 nginx -V | xargs -n1`
 * Test a configuration without reloading - `nginx -t`
 * Stop all nginx processes - `nginx -s stop`
 * Start all nginx processes - `nginx -s start`
 * Restart all nginx processes - `nginx -s restart`
 * Realod nginx configuration (without restarting) - `nginx -s reload`

**Nmap**

* Check single port on single host - `nmap -p <port> <host/IP>`
* Intrusive port scan on a single host - `nmap -sS <host/IP>`
* Top ten port on a single host - `nmap --top-ports 10 <host/IP>`

**Password generation**

 * Create hash from password - `openssl passwd -crypt <password>`
 * Generate random 8 character password (Ubuntu) - `makepasswd -count 1 -minchars 8`
 * Create .passwd file with user and random password - `sudo htpasswd -c /etc/nginx/.htpasswd <user>`

**Removing files**

 * Remove files over 30 days old - `find . -mtime +30 | xargs rm -rf`
 * Remove files older than 7 day starting with 'backup' - `find . -type f -name "backup*" -mtime +7 -exec rm {} \;`

**SSH**

 * Generate generic ssh key pair - `ssh-keygen -q -t rsa -f ~/.ssh/<name> -N '' -C <name>`

**Tail log with colored output**

 * `grc tail -f /var/log/filename`

**Tmux**

 * Kill a window - `tmux kill-window -t 0`
 * Kill stuck tmux window - `tmux kill-window -t X`
 * Create a new session <name> - `tmux new -s <name>`
 * List all sessions - `tmux ls`

 **ps**
 
 * Show process tree of all PIDs - `ps auxwf`
 * Show all process info and hierarchy (same as above)- `ps -efH`
 * Show orphaned processes for <use> - `ps -ef|awk '$3=="1" && /pandora/ { print $2 }'`
 * Show all orphaned processes (could be daemons) - `ps -elf | awk '{if ($5 == 1){print $4" "$5" "$15}}'`
 * Show zombie processes - `ps aux | grep Z`
 
