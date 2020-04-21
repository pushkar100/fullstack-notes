# Fullstack for Frontend Engineers v2

## Useful Commands

- **`echo $0`**: Displays the name of the shell you are using.
- **`less`**: Opens parts of a file which you can scroll and load more of (Helpful while viewing big files)
- **`head`**: Prints the *first* 10 lines of a file by default
- **`tail`**: Prints the *last* 10 lines of a file by default
- **`ping`**: Persistently hits the URL (IP). Useful in checking if site is up or down.
- **`traceroute`**: Displays all the hops the request took to reach the final server. Helpful in determining where in the network (Client, CDN, Server, etc) did the failure occur.

```shell
$ echo $0
-zsh


$ ping google.com
PING google.com (216.58.200.142): 56 data bytes
64 bytes from 216.58.200.142: icmp_seq=0 ttl=50 time=42.404 ms
64 bytes from 216.58.200.142: icmp_seq=1 ttl=50 time=43.817 ms
64 bytes from 216.58.200.142: icmp_seq=2 ttl=50 time=42.465 ms
64 bytes from 216.58.200.142: icmp_seq=3 ttl=50 time=43.090 ms
^C
--- google.com ping statistics ---
4 packets transmitted, 4 packets received, 0.0% packet loss
round-trip min/avg/max/stddev = 42.404/42.944/43.817/0.571 ms


$ traceroute google.com
traceroute to google.com (216.58.200.142), 64 hops max, 52 byte packets
 1  dlinkrouter (192.168.0.1)  2.812 ms  2.395 ms  1.671 ms
 2  10.235.0.1 (10.235.0.1)  3.508 ms  2.811 ms  2.101 ms
 3  broadband.actcorp.in (202.83.20.50)  12.214 ms  9.472 ms  9.624 ms
 4  72.14.194.18 (72.14.194.18)  44.373 ms  43.746 ms  43.224 ms
 5  74.125.242.145 (74.125.242.145)  44.104 ms
    74.125.242.129 (74.125.242.129)  45.371 ms
    74.125.242.145 (74.125.242.145)  42.982 ms
 6  216.239.54.159 (216.239.54.159)  41.432 ms
    216.239.54.197 (216.239.54.197)  42.671 ms  41.974 ms
 7  app (216.58.200.142)  41.373 ms  41.941 ms  43.774 ms
```

1. Both ping and traceroute make use of the **ICMP** protocol (N/W Layer).
2. Servers can be configured to not respond to ping and traceroute requests. So, you will not get any data in that case.

## SSH Creation

1. Move to SSH folder: `cd ~/.ssh`
2. Run SSH key generator: `ssh-keygen`. Give it a name and optionally, a passphrase.
3. Check if both the private and the public keys have been created: `ls | grep <keyname>`
4. Print out the public key that is going to be shared: `cat <keyname>.pub`

```shell
$ cd ~/.ssh

$ ssh-keygen
# Mutlistep process which asks for key name, passphrase, etc
# Assume name provided was 'fsfe'

$ ls | grep fsfe 
fsfe        # The private key
fsfe.pub    # The public key


$ cat fsfe.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCuDzA739/Dqzma3IJ6RzhnAZC8FekVuPmcc4Y2cS4cFAQ9KY8NTCvvR7hlkJjPAf+LHpujhaSok8Bqap1Wa6Pi78IUxMHtb0txG/MezUI0id+3jLck/i0b00mJmjdO/POTCbMDqwy1AdOUIhwoAWL+PK/4VpLdH/2gInjelLa8nR27SSXiUiKB18TdC+Gf4Y6stMywqT0JWPeNuJfutAZZ1+m76TaGq4UM0r/S7ZmWM9Qd0bR3b2f+fQUwJd9EAx18wV10lNY7ri4lzMG+ezRsCECzoMRakUs+1rbxAd19X08ct6MoVHvDQWESZLh3rB16DfripZ5MyW5qMoRRqIfV pushkardk@Pushkars-MacBook-Pro.local

# The above public key contents can be copy pasted elsewhere

$ cat fsfe.pub | pbcopy 
# Automatically copies the printed public key to the clipboard
```

## SSH Connection to Server

Once you have added the public ssh key to the server and it is running, you can connect (login remotely, on the CLI) to that server via the SSH client on your terminal.

Usually `root` is the default superuser that gets created for the server.

**Steps**:

1. Run **`ssh root@<ip-address>`**  (Ex: `ssh root@167.71.224.82`)

2. This might throw an error the first time. Ex: `root@167.71.224.82: Permission denied (publickey).`

3. The error is because there is no private key corresponding to the public key. Rather, ssh does not know that it has to check the private key that you created.

4. Connect by specifying the private key: **`ssh -i <ssh-pvt-key> root@<ip-address>`**

   - Example: `ssh -i ~/.ssh/fsfe root@167.71.224.82` 

5. Once you do this, if the configuration file allows adding key chains, we can continue with `ssh root@<ip-address>` from the next time onwards. That is, the private key will be checked for automatically from the next time. For this, you need to have the following in your **`~/.ssh/config`** file:

   ```shell
   Host *											# The hostnames to match the rules against
    AddKeysToAgent yes         # Allows adding private keys to the keychains it checks
    UseKeychain yes            # Use keychain to check keys during connection to this host
    IdentityFile ~/.ssh/id_new # Default private key to use for this host
   ```

6. If the above configuration is not present, you may still programmatically add the new private key to the keychains to check by running the following **`ssh-add`** command: **`ssh-add -K <path-to-pvt-key>`**.

```shell
$ ssh-add -K ~/.ssh/fsfe
Identity added: /Users/pushkardk/.ssh/fsfe (pushkardk@Pushkars-MacBook-Pro.local)
```

**Note**:

The first time you login with root, you are essentially a *superuser* and this is dangerous as anything and everything can be changed. It is indicated by a **`#`** sign on the command prompt once you have ssh'ed inside (Example: `root@ubuntu-s-1vcpu-1gb-blr1-01:~#`). It is advisable to create and change to another user apart from root (superuser).

If someone hacks into your server, they can do more damage as root user as opposed to another login.

Running `whoami` will tell you the current user name on the server.

## Buying a Domain

We can buy domains from any service. A few examples include godaddy, name cheap, google, big rock, etc.

Once we have a domain name, we will have to connect it to our hosting server. That is, we want the *domain name to point to our hosting server IP* in the DNS records.

Two of the most common DNS records:

1. **A record**: Maps a name (domain or subdomain URL) to an IP address.
2. **CNAME**: Maps a name to another name. It is usually used to map sub-domain names to the actual domain. The CNAME source represents an *alias* for the target name and inherits its entire resolution chain.

The command to read the records and the mappings in the DNS is: **`dig`**

```shell
$ dig google.com
...
;; ANSWER SECTION:
google.com.		128	IN	A	216.58.200.142
...

# The above is an A record mapping google.com to 216.58.200.142


$ dig social.hotstar.com
...
;; ANSWER SECTION:
social.hotstar.com.	300	IN	CNAME	wildcard.hotstar.com.edgekey.net.
wildcard.hotstar.com.edgekey.net. 67 IN	CNAME	e4447.e27.akamaiedge.net.
e4447.e27.akamaiedge.net. 3	IN	A	106.51.146.149
...

# The above maps social.hotstar.com to another name (CNAME record) which maps to another name itself (CNAME record) which is finally mapped to IP 106.51.146.149 (A record)
```

## Domain Setup

Once you have bought a domain, we need to set it up.

**Steps**:

1. Usually, both the hosting service provider (say, digitalocean or AWS) and the domain name provider (godaddy, name cheap, etc) will have their *own DNS records* each. These records are termed as the **nameservers**. The nameservers are generally something like `ns1.digitalocean.com`.
2. We first want to create two **A records** in the **hosting** provider's DNS table.
   1. **`@`**: The mapping from `<hostname>.<tld>` to an IP (Ex: When we hit `google.com`)
   2. **`www`**: The mapping from `www.<hostname>.<tld>` to an IP (Ex: When we hit `www.google.com`) (Yes, these two are actually different mappings)
3. At this point, we have the domain names mapped to IP addresses in the hosting provider's nameserver (The DNS record book).
4. However, we will *not* be redirected to the IP on entering the URL until the domain name provider's *nameserver* points to the hosting provider's *nameserver*.
   1. This means that both the locations hold a DNS record table
   2. We have edited the hosting provider's table to include A records that map our domain name to our server's IP
   3. **Important**: We need to tell the domain name provider site to **use the nameserver of the hosting provider** instead of its own records.
5. To do this, we update the `nameserver` field in the domain name provider's management console (Ex: GoDaddy) to point to the hosting provider's nameserver (Ex: `ns1.digitalocean.com`).

*This process might take up to an hour to a day.*

This whole setup is to: 

- Edit the DNS mapping in the nameserver of hosting provider, and
- To give control of the DNS mapping from the domain name provider to our hosting server's nameserver.

## Server Setup

We want to update our server (i.e ubuntu/linux) and create new user instead of root:

**Steps**:

1. Update server: **`apt update`** (`apt` is a wrapper command that includes `apt-get` and other tools)
2. Upgrade server: **`apt upgrade`**
3. Add a new user: **`adduser <username>`** (Ex: `adduser pushkar`)
4. Make new user a *superuser* and remove root access:
   1. Make the user a super user (while you are a root user): **`usermod -aG sudo <username>`**
   2. Switch user: **`su <username>`**
   3. Verify that new user has sudo (superuser) access: **`sudo cat /var/log/auth.log`** (If the new user needs to access anything outside his/her `~` home folder, they need to be a superuser)

**Note**: ` sudo` stands for "super user do". We use the `sudo` command to run other commands but by invoking super user access for that user (if available).

**Setting user permissions**

Once you have switched to the new user, we need to setup a few things:

1. Change to home directory and create the ssh folder: **`cd ~`** and **`mkdir -p ~/.ssh`** (`-p` creates the directory only if it does not exist already)

2. Create an **`authorized_keys`** file and paste the *PUBLIC* SSH key: **`vim ~/.ssh/authorized_keys`**

   1. When we setup our hosting server, the provider (Ex: Digital Ocean) did it for us automatically but for the `root` user.

   2. We need to do this manually for the user we are creating. An example of `~/.ssh/authorized_keys` is:

      ```shell
      ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCuDzA739/Dqzma3IJ6RzhnAZC8FekVuPmcc4Y2cS4cFAQ9KY8NTCvvR7hlkJjPAf+LHpujhaSok8Bqap1Wa6Pi78IUxMHtb0txG/MezUI0id+3jLck/i0b00mJmjdO/POTCbMDqwy1AdOUIhwoAWL+PK/4VpLdH/2gInjelLa8nR27SSXiUiKB18TdC+Gf4Y6stMywqT0JWPeNuJfutAZZ1+m76TaGq4UM0r/S7ZmWM9Qd0bR3b2f+fQUwJd9EAx18wV10lNY7ri4lzMG+ezRsCECzoMRakUs+1rbxAd19X08ct6MoVHvDQWESZLh3rB16DfripZ5MyW5qMoRRqIfV pushkardk@Pushkars-MacBook-Pro.local
      ```

3. Now we can `exit` out of the server and ssh login with the new user: **`ssh <username>@<ip-address>`**

   ```shell
   $ ssh pushkar@167.71.224.82 # Must work the same way in which public and private keys were matched for the root user.
   ```

**Disabling root user access**

Once the new user is a superuser and can SSH, we need to disable root access to prevent hackers from logging in user root credentials. 

1. From within the new user login, give write permissions to the authorized ssh keys file. **`chmod 644 ~/.ssh/authorized_keys`**
2. You can disable root login from the ssh daemon config: **`sudo vim /etc/ssh/sshd_config`**
3. Inside this file, change `PermitRootLogin yes` to `PermitRootLogin no`. This disables root login using SSH from now on!
4. However, the *'SSH daemon'* has been running continuously. In order for the effect to take place, we must restart the service: **`sudo service sshd restart`**
5. You may reopen the sshd config file `sudo vim /etc/ssh/sshd_config` to verify that your disabling change has persisted.

Now, ssh login with root (Ex: `ssh root@167.71.224.82`) is disabled! Throws `root@167.71.224.82: Permission denied (publickey).` on attempting it.

**Note**: Now that we are not `root` anymore, we have to use `sudo` to run commands that require super user access.

## Nginx Overview

**What is Nginx?**

- Even though we have setup a server, it still does not have any application that responds to requests. How does the server know which application must respond? Which is the actual server app? etc.
- Nginx, once running, will manage this for us. It can do a lot of things, especially:
  - *Reverse proxy*: Take one request and pass it to multiple different services (DB, Node, etc). A proxy does the opposite. It takes multiple requests and routes it through one point.
  - *Web Server*: Can implement the client-server request & response service.
  - *Proxy Server*: Can route multiple requests to a server through itself so that it has control over what the actual web server receives.
- It can do many other things like setting gzip compression levels, configuring HTTP, etc.
- It is *incredibly fast* at handling requests!

**Nginx setup**:

1. **`sudo apt install nginx`** installs it
2. **`sudo service nginx start`** starts the nginx service

Once these two are done, hitting our IP of the server in the browser must bring up the default **`Welcome to nginx!`** page. If our domain name is mapped already, hitting the domain name will also open up this page. It automatically opens port `80` for you.

**Default config introduction**:

- The config file that shows the nginx rules: **`/etc/nginx/sites-available/default`**
- This is a very big, heavily commented out file. We can open it to edit with `sudo vim` command.
- By default, the base directory for requests is defined as **`root /var/www/html;`**. The nginx welcome page resides here.
- **`server_name _;`** is the server name.
- **`location / { ... }`** routes the request path (`/`) to a block of commands that tell it what to do for this request
- Inside a location, we can have directives. One such directive is **`try_files`** which tries to serve files defined depending on what is available first in the order they are listed
- If you want to add the homepage, you may edit `/var/www/html/index.html` as nginx tries to serve index page from this folder (`/var/www/html`) by default

## NodeJS

Why do we need NodeJS when we already have Nginx which is a web server by itself?

We need NodeJS because Nginx might be good as a basic web server and serve static files well but when our application becomes complex, NodeJS really shines. It is extremely good with handling request routing, connecting to other services like databases, handling I/O, etc.


