# Research and Notes

# Virtualbox Images
- <http://www.vagrantbox.es/>

# Vagrant VirtualBox Image CentOS 6.4 x86_64Minimal
- <https://github.com/2creatives/vagrant-centos/releases/download/v0.1.0/centos64-x86_64-20131030.box>

# Vagrant and Ansible
- <http://docs.ansible.com/guide_vagrant.html>

# Research and Notes
- [Install dmg on Mac with commandline] (http://commandlinemac.blogspot.com/2008/12/installing-dmg-application-from-command.html)
- [Test if an app is install on Mac] (http://stackoverflow.com/questions/6682335/how-can-check-if-particular-application-software-is-installed-in-mac-os)
- [Ansible and Vagrant] (http://julien.ponge.org/blog/scalable-and-understandable-provisioning-with-ansible-and-vagrant/)
- [Test if xcode] (http://railsapps.github.io/xcode-command-line-tools.html)
- [Ansible Playbooks - including Virtualbox and Vagrant] (https://github.com/MWGriffin/ansible-playbooks)
- [Developing Ansible Modules] (http://docs.ansible.com/developing_modules.html)


Ansible
----
- [Accelerated Mode] (http://www.ansibleworks.com/docs/playbooks_acceleration.html)
- [Provisioning Ansible] (http://docs.vagrantup.com/v2/provisioning/ansible.html)
- [Modules] (http://www.ansibleworks.com/docs/modules.html)
- [Best Practices - include Modules in ./library] (http://docs.ansible.com/playbooks_best_practices.html)

Todo
----
- <http://docs.ansible.com/playbooks_best_practices.html#operating-system-and-distribution-variance>
- <http://docs.ansible.com/playbooks_conditionals.html>
- <http://commandlinemac.blogspot.com/2008/12/installing-dmg-application-from-command.html>
- <http://docs.ansible.com/developing_modules.html>

Mac OSX Parsing
----
- <http://docs.python.org/2.7/library/plistlib.html>
- <http://docs.python.org/2/library/subprocess.html#module-subprocess>

```
import json
from plistlib import readPlist
import StringIO

in_file = StringIO.StringIO(plist)
plist_dict = readPlist(in_file)

print json.dumps(plist_dict)
```

Video's - Tutorials
----
- [Vagrant Video] (http://www.youtube.com/watch?v=Im4wNqlolqQ)
- [Vagrant with Ansible] (http://www.youtube.com/watch?v=BTAgQ9-LD5o) 

# Notes
Mac OSX Version
```
sw_vers -productVersion 
```


SSH
----
Add these setting to a minimal CentOS for Virtualbox
```
echo "PermitRootLogin without-password" >> /etc/ssh/sshd_config
echo "UseDNS no" >> /etc/ssh/sshd_config
```


Nginx
----
```
###############
# Install nginx
###############

export NGINX_REPO_FILE=/etc/yum.repos.d/nginx.repo
touch $NGINX_REPO_FILE
chmod 644 $NGINX_REPO_FILE
chown root:root $NGINX_REPO_FILE


echo "[nginx]" > $NGINX_REPO_FILE
echo "name=nginx repo" >> $NGINX_REPO_FILE
echo 'baseurl=http://nginx.org/packages/centos/$releasever/$basearch/' >> $NGINX_REPO_FILE
echo "gpgcheck=0" >> $NGINX_REPO_FILE
echo "enabled=1" >> $NGINX_REPO_FILE

# Verify it worked
cat $NGINX_REPO_FILE
yum repolist

#install nginx 
yum -y install nginx.x86_64
```

selinux 
----
```
sestatus
setenforce 0 #to disable
setenforce 1 #to enable

# /etc/sysconfig/selinux 
#change from
#SELINUX=enforcing 
SELINUX=disabled
# Fix selinux context
# http://blog.rem.co/blog/2013/02/19/selinux-allowing-ssh-public-key-authentication/
# Check: /var/log/audit/audit.log
# command to fix context: restorecon -R -v /root/.ssh


# If issues investigate in
# /var/log/messages
#/var/log/secure
chown root:root /root/.ssh/
chmod 700 /root/.ssh/
ssh-keygen
cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
chown root:root /root/.ssh/authorized_keys
chmod 600 /root/.ssh/authorized_keys


```

virtualbox
----
- Machine Setup with SSH and local networking <http://webees.me/installing-centos-6-4-in-virtualbox-and-setting-up-host-only-networking/>


# Setup git PATH for non-terminal programs.sh
```
#!/bin/sh

append_path () {
  input="$1"
  value="$2"
  if ! echo $input | /usr/bin/egrep -q "(^|:)$value($|:)" ; then
     if [ "$3" = "after" ] ; then
        echo $input:$value
     else
        echo $value:$input
     fi
  else
    echo $input
  fi
}

append_plist_var() {
  name="$1"
  append_value="$2"
  default_value="$3"
  current_value="`defaults read $HOME/.MacOSX/environment ${name}`"
  [ ! "$current_value" ] && current_value="$default_value"
  new_value="`append_path "$current_value" "$append_value" after`"
  defaults write $HOME/.MacOSX/environment "$name" "$new_value"
  if [ "$current_value" == "$new_value" ]; then
    echo "No change to $name in ~/.MacOSX/environment.plist"
  else
    echo "Variable $name in ~/.MacOSX/environment.plist changed from '$current_value' to '$new_value'"
    echo "You will need to log out of your Mac OS X user account and log back in for changes to take effect."
  fi
}

append_plist_var PATH "/usr/local/git/bin" "/usr/bin:/bin:/usr/sbin:/sbin:/usr/local/bin:/usr/X11/bin:/opt/local/bin"

pushd "$HOME"

popd
```