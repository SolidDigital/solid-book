# Common Shell Commands

tags: linux, commands

## Check if command exists from script

```
command -v foo >/dev/null 2>&1
```

http://stackoverflow.com/a/677212/186636

## `curl`

To display prettified JSON:

```
curl http://something.blah.yad | python -m json.tool
```

## Disk usage

```
# disk usage, human readable
du -chs /*
# disk usage, summary per directory
du -sh

# disk usage, filter by file size
find . -size +10k -exec ls -lh {} \+

# free space
df -h

free -h

# directories that use a gig or over
du -h / | grep '[0-9\.]\+G'
```

## files

Delete all files older than 90 days in a directory:

```
find ~/backups -type f -mtime +90 -delete
```

## `dpkg`

List where apt-get installed something

```
dpkg -L nginx
```

## Last reboot

```
who –b
```

## Links

Creating a symbolic link:

```
ln -s from to
```

## Means

To calculate the mean of a column of numbers in a file:

```shell
awk '{ total += $1; count++ } END { print total/count }' nocache.log
```

Awk is a program for dealing with files with columns. `$1` means the first column.
The command `total += $1; count++` runs for each row.

## `open`

* Open a url with the default browser:

```shell
open http://localhost:4000
```

This command is often useful after running a watch task or something similar. A
background process can be used to solve the problem of using a task that doesn't
exit until you are done with it:

```
(sleep 3; open http://localhost:4000)&; someSortOfWatchTask
```

The `&` symbol can be used to launch a background task.

## Ports

To get the pid of a process running on a port, use `lsof` (list open files):

This will get the pid the process(es) running on port 4000.

```
lsof -t -i:4000
```

To kill them:

```
kill $(lsof -t -i:4000)
```

## `scp`

Scp is for copying content over ssh.

You can use your ssh aliases when defining the to and from locations:

```
scp -r exampleStaging:/some/directory ~
```

## `ssh`

Open an SSH tunnel to example.com port 22 and use your local port 27018 to
map to example.com's 27017.

Something like this is useful for interacting with a locally bound process (e.g.
  mongo, mysql, elasticsearch...).

```shell
ssh -L27018:localhost:27017 user@example.com -p 22
```

You can get more complicate, for example if you want Object Rocket to think you are example.com from your machine:

```shell
ssh -L38945:something.objectrocket.com:38945 user@example.com
```

Now you can use your port 38945 as if you were on example.com... allowing cli mongo login as well as Robomongo, etc access.

You can use the -N flag for non interactive tunneling.

You can also store your connections in a sock file for easy closing later:

```shell
# open and background manually ---- not with -f flag
(ssh -N -L 9200:localhost:9200   -M -S /tmp/ssh_tunnel_9200_%h.sock $SSH_HOST_E)&

# some point later close
ssh -S /tmp/ssh_tunnel_9200_%h.sock  -O exit $SSH_HOST
```

## `sftp`

```sh
sftp -oPort=<port> <user>@<host>
# Enter password if prompted.

# E.g. upload wordpress theme directory
# Switch to themes directory on remote.
sftp> cd wp-content/themes
# Run put command with recursive flag to upload your local theme directory
# to the current remote directory.
sftp> put -r local/path/to/wp-content/themes/my-theme
```

## `tar`

There are two very common combination of flags in conjunction with the `tar` command:

```
# extract files with verbose output
tar -xvf some.file.tar.gz

# compress files with verbose output
tar -zcvf some.file.to.create.tar.gz directory-to-compress
```

If you want to delete the files as they get compressed, use the `--remove-files` flag.
This has to be the first flag used, or `tar` will think it's a file.

```
tar --remove-files a.tar.gz b
```

The `--remove-files` flag is not supported by the `tar` command that ships with
Mac, but gnu tar does:

```
brew instasll gnu-tar
```

## Number of cores

```
cat /proc/cpuinfo | awk '/^processor/{print $3}' | wc -l
```

## `chmod` (Change permissions)
Change permission on a file:
```
chmod 0755 <file>
```
* positions: user, group, everyone
* read: 4
* write: 2
* execute: 1

## `cat`
Send file contents to stdout:
```
cat <file>
```

## `grep`
Filter text that matches a regular expression pattern:
```
cat <file> | grep <pattern>

## -C for context of surrounding `n` lines
cat <file> | grep -C n <pattern>
```

## `tail`
See contents added to end of file in realtime:
```
tail -f <file>

## with grep, filter new lines that match
tail -f <file> | grep --line-buffered <pattern>
```

## `sed`
Search and replace text
```
# read from in and write to out
sed 's/search_regex/replacement_string/g' < file_in > file_out

# convert to https in a mysql dump
sed 's/http:\/\/www\.example\.com\//https:\/\/www.example.com\//g' < example.sql > example.sql
```

## `date`
Get current timestamp on server
```
date
```

## `rsync`
Sync directories. Useful over ssh.

If you want to get the content of two dirs to match, put a `/` after each one:

```
rsync -rztP ~/dir/uploads/ staging-wordpress:/var/www/vhosts/vhost.com/source/httpdocs/wp-content/uploads/
```

The `t` tag is useful, since that preserves the timestamps, so that syncing is smart.

If using ec2 (or any other network), within network rsyncs are much faster than transferring out and back into a network. For example ec2 to your computer back up to ec2 can be multiple times slower than ec2 to ec2. Using forward agent is a good way to do ec2 to ec2.

## `findmnt`

## dns

To check TTL you have to query an authorative server for the zone, or you just get the time remaining.

First get the name servers:

```bash
dig @8.8.8.8 +short NS example.com
```

Then get ttl from one of the name servers:

```bash
dig +nocmd +noall +answer @ns1.nameserverexample.com example.com
```

## Check emails with status sent

```bash
# can often be simplified to just grep status
cat /var/log/mail.log |grep -v "relay=local" |grep "relay=" |grep "status=sent"
```
