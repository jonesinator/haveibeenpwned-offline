# haveibeenpwned-offline

A simple bash script that downloads the
[haveibeenpwned.com password lists](https://haveibeenpwned.com/Passwords), and
installs a script (`is-pwned`) that can search the password lists offline. The
integrity of the downloaded files is verified to make sure that the downloads
were not corrupted.

## Dependencies

These scripts require `7zr` and `wget` in order to download and extract the
password lists. If you don't have these packages, they can be installed by your
distribution's package manager, usually under the names `p7zip` and `wget`
respectively. For example, in Debian one could use
`apt-get install -y p7zip wget` to install the dependencies.

## Platforms

This known to work on Debian 9, Ubuntu 17.04, and Windows 10 (via Cygwin). It
should work on anything that has coreutils, bash, wget, 7zip, and sha1sum. The
script will likely work on MacOS, but it has not been tested there.

## Installation

The installation is performed by a bash script. By default files are placed in
paths that normally require root access (`/usr/local/bin` and
`/usr/local/share`) so to install into this common location the following
one-liner can be used:

```bash
$ wget -qO - https://raw.githubusercontent.com/jonesinator/haveibeenpwned-offline/master/install-is-pwned | sudo bash
```

If you'd like to install as a regular user, the paths can be changed, as in the
following set of commands:

```bash
$ export IS_PWNED_SCRIPT_PATH=~/is-pwned
$ export IS_PWNED_DATA_PATH=~/is_pwned
$ wget -qO - https://raw.githubusercontent.com/jonesinator/haveibeenpwned-offline/master/install-is-pwned | bash
```

Finally, a Vagrantfile has been provided if you'd like to install the script in
a sandbox.

```bash
$ git clone https://github.com/jonesinator/haveibeenpwned-offline
$ cd haveibeenpwned-offline
$ vagrant up
```

Obviously, if you are paranoid you can download the script, inspect it, and
then run it instead of using the one-liners.

The password lists take up quite a bit of room, so be sure you have plenty of
free disk space before running the installer. If downloads are interrupted the
installer tries to be smart about it and only downloads the files that were not
fully downloaded. Resuming interrupted downloads is not possible because the
haveibeenpwned.com servers do not permit it.

## Usage

Once the script is installed, you simply execute the `is-pwned` script. You'll
be prompted for your password twice (to verify that it was typed correctly).
Once the password has been entered the SHA1 sum of the password is calculated
and the password list is searched for that hash. The exit status of `is-pwned`
will be `0` if the password is not in the database, and it will be `1` if the
password has been pwned.

### Examples

```bash
$ is-pwned
Password to check:
Password to check (again):
Checking database...(this could take a while)
This password is not in the database.
$ echo $?
0
```

Vagrant Example (assuming you're in the same directory as the Vagrantfile):

```bash
$ vagrant ssh -c is-pwned
Password to check:
Password to check (again):
Checking database...(this could take a while)
This password has been pwned!
Connection to 127.0.0.1 closed.

$ echo $?
1
```

## Updating

The install script can simply be run again to make sure all password lists have
been downloaded as more password lists are added in the future. This can even be
placed in a cron job to make sure the password lists are always up to date. The
install script only downloads files if necessary. If no new files have been
added then nothing is downloaded. If new files have been added then only the new
files are downloaded since the old files should still be cached in the
filesystem.

For vagrant one can update using the following commands (assuming you're in the
same directory as the Vagrantfile):

```bash
$ git pull
$ vagrant up --provision
```
