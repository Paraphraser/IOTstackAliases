# Useful aliases for working with IOTstack

When working with [IOTstack](https://github.com/SensorsIot/IOTstack), it is useful to automate many common tasks such as:

* Navigating to a container's "services" directory (eg to edit its environment file);
* Launching a shell **inside** a container (for general nosing around);
* Launching a specific process **inside** a container (eg the InfluxDB command line interface);
* Executing common tasks (like "up" and "down") without having to first change your working directory to `~/IOTstack`;
* Extending commands to fill gaps. For example, terminating a single container instead of taking down your entire stack;
* and so on…

Although you could create a scheme of shell scripts to accomplish most of these goals, aliases and shell functions are usually simpler and, often, the only way to accomplish some goals.

Note:

* This GitHub repository used to be a [gist](https://gist.github.com/Paraphraser/7612d3c780d284a502bd1f158c5186e8). Unfortunately, the copy-and-paste method of acquiring the aliases would occasionally cause problems when the original Unix `0x0A` line-endings (LF) were replaced with DOS/Windows `0x0D 0x0A` line-endings (CR+LF). The alias file would then fail. Providing the alias file via Git should reduce the likelihood of that happening.  

## Prerequistes

Make sure you are running an up-to-date version of `docker-compose` (1.29.1 or later):

```
$ docker-compose version
docker-compose version 1.29.2, build unknown
docker-py version: 5.0.0
CPython version: 3.7.3
OpenSSL version: OpenSSL 1.1.1d  10 Sep 2019
```

IOTstack installs `docker-compose` using `apt install`. That results in version 1.21.0, which is obsolete, and can't be upgraded using `apt upgrade`. If you discover that version 1.21.0 is installed on your system, you can upgrade like this:

```
$ cd ~/IOTstack
$ docker-compose down
$ sudo apt -y remove docker-compose
$ sudo pip3 install -U docker-compose
```  

Then logout and login again.

See also:

* [Installing Docker for IOTstack](https://gist.github.com/Paraphraser/d119ae81f9e60a94e1209986d8c9e42f) (gist).
* If you run into trouble with the `pip3` command, see [scripting-iotstack-installations](https://gist.github.com/Paraphraser/d119ae81f9e60a94e1209986d8c9e42f#scripting-iotstack-installations) in that same gist.

## Installation

### Step 1

Login to your Raspberry Pi and clone this repository. The recommended commands are:

```bash
$ mkdir -p "$HOME/.local"
$ git clone https://github.com/Paraphraser/IOTstackAliases.git "$HOME/.local/IOTstackAliases"
```

### <a name="step2"> Step 2 </a>

Test the result like this:

```bash
$ . "$HOME/.local/IOTstackAliases/dot_iotstack_aliases"
```

Notes:

* The `.` followed by a space at the start of the command is called a *source* statement. It tells the shell to process the file in-line, like an `#include` statement in a programming language.

	*Sourcing* a file is different to *executing* a file. If you check, you will see that `dot_iotstack_aliases` does not have execute permission. Neither does the file start with `#!/bin/bash`. This is intentional. Although you *can* add execute permission and then execute the file as a command, it will not produce the correct result. It **must** be sourced.
	
* The "dot\_" prefix on the filename is intended to remind you that the file should be *sourced*.  

If you have done the first two steps correctly, you should get output like this:

```bash
Useful Docker aliases:
       Grafana: GRAFANA_SHELL
        Influx: influx, INFLUX_SHELL
     Mosquitto: MOSQUITTO_SHELL
       NodeRed: NODERED_SHELL, NODERED_DATA
        Docker: BUILD     | DPS       | DNET      {<container> …}
                PULL      | REBUILD   | RECREATE  {<container> …}
                RESTART   | TERMINATE | UP        {<container> …}
                DOWN, I, S, T, V
```

If you get any error messages, go back and check your work. 

### Step 3

To apply `dot_iotstack_aliases` each time you login, add this *source* statement:

```bash
. "$HOME/.local/IOTstackAliases/dot_iotstack_aliases"
```

to one of the following files:

* ~/.bashrc
* ~/.profile

What's the difference? I'm glad you asked:

* `.profile` is called when you `ssh` into your Raspberry Pi but not when you open a terminal session in VNC.
* `.bashrc` is called when you open a terminal session in VNCbut not when you `ssh`.

You might be thinking, "I access my Raspberry Pi using both `ssh` and VNC so that means I should add the *source* statement to both files." Well, yes and no. There is a wrinkle. The `.profile` that you get by default already sources `.bashrc`, like this:

```bash
# if running bash
if [ -n "$BASH_VERSION" ]; then
   # include .bashrc if it exists
   if [ -f "$HOME/.bashrc" ]; then
      . "$HOME/.bashrc"
   fi
fi
```

If you have **not** removed those lines from `.profile` then `.bashrc` is the correct place to add the source statement to apply `dot_iotstack_aliases`. If you **have** removed those lines from `.profile` then you may need to add the source statement to both files.

Here's an example of editing `.bashrc` using `vi`:

```bash
$ vi ~/.bashrc
G
o
. "$HOME/.local/IOTstackAliases/dot_iotstack_aliases"
[ESC]
:wq
$
```

In words:

1. Launch "vi" with the target file.
2. Press <kbd>G</kbd> to jump to the end of the file.
3. Press <kbd>o</kbd> to open a new line after the last line in the file, and go into insert mode.
4. Type the source statement as-is and press <kbd>return</kbd> at the end of the line. Make sure you pay attention to these key points:

	- The line starts with a period (`.`) followed by a space;
	- There are **no** other spaces on the line; and
	- Make sure you match the spelling **exactly**.Unix is case-sensitive.

5. Press <kbd>esc</kbd> to exit insert mode.
6. Press <kbd>:</kbd> to move to command mode, <kbd>w</kbd> to write the in-memory buffer to the target file, and <kbd>q</kbd> followed by <kbd>return</kbd> to leave "vi".

Use the `tail` command to confirm your editing:

```bash
$ tail -1 ~/.bashrc 
```

### Step 4

Test your work. Do **NOT** log-out of your existing terminal session. Instead, open a new terminal session.

> How you do this will depend on how you connect to your RPi. If you are using `ssh` then start a new `ssh` session from your host computer. If you are using VNC then launch a new terminal session from the GUI. Ditto if you have gone "all the way" and have connected a keyboard, mouse and screen to your RPi.

Worst case is that you will be unable to login to the new terminal session. This is why you should never log-out of an existing terminal session until you are 100% certain that changes made to either `.bashrc` or `.profile` are working. If you can't launch a new session, you just use the existing session to track down and nail any bugs. Rinse, repeat.

> This advice goes double if you ever need to edit `/etc/profile` !!

In the **new** terminal session, you should expect to see the same list of aliases shown in [Step 2](#step2).

## Using the aliases and shell functions

I create most of my aliases using all-caps names. I think this makes it easier for auto-completion to do its work but, it's your system, so change it if you don't like it.

The key advantage of these aliases is that they work from anywhere. You don't have to `cd ~/IOTstack` first.

### General-purpose aliases and shell functions

The general-purpose aliases (and functions) are the ones listed under the "Docker" heading above. They are described here in alphabetical order.

#### Alias: <a name="aliasBUILD"> `BUILD` {container …} </a>

```bash
$ docker-compose up -d --build {CONTAINER …}
```

* Similar to [`UP`](#aliasUP) in that it adds the `--build` flag to re-build the local images for containers that are built with a `Dockerfile`.
* Differs from [`REBUILD`](#aliasREBUILD) in that no attempt is made to pull down any later base image from DockerHub.
* Without arguments, every service definition in your `docker-compose.yml` that includes a `build` directive will be re-built by running its Dockerfile.
* With a list of named containers, it restricts itself to just those containers.
* Should usually be followed by:

	```bash
	$ docker system prune
	```

#### Function: <a name="funcDNET"> `DNET` {container …} </a>

```bash
$ docker ps --format "table {{.Names}}\t{{.Ports}}"
```

* The column list provided by the `--format` argument focuses on the container's networking aspects (ports etc).
* Has the advantage that the information for each container usually fits on one line in the terminal window.
* Optional arguments limit the display to named containers. Names are interpreted as wildcards.

#### Alias: <a name="aliasDOWN"> `DOWN` </a>

```bash
$ docker-compose down
```

* Takes down your entire stack.
* The `down` command can't take arguments, which is why [`TERMINATE`](#aliasTERMINATE) needs to exist.

#### Function :<a name="funcDPS"> `DPS` {container …} </a>

```bash
docker ps --format "table {{.Names}}\t{{.RunningFor}}\t{{.Status}}"
```

* The column list provided by the `--format` argument focuses on whether a container is running or in a restart loop.
* Has the advantage that the information for each container usually fits on one line in the terminal window.
* Optional arguments limit the display to named containers. Names are interpreted as wildcards.

#### Alias: <a name="aliasI"> `I` </a>

```bash
$ cd ~/IOTstack
$ ls
```

* Changes the working directory to, and lists, the top-level IOTstack directory.
* See also [`S`](#aliasS), [`T`](#aliasT) and [`V`](#aliasV).

#### Alias: <a name="aliasPULL"> `PULL` {container …} </a>

```bash
$ docker-compose pull {CONTAINER …}
```

* Tells `docker-compose` to check for more-recent images on DockerHub and download any newer images.
* Only applies to service definitions with an `image` directive.
* Without arguments, it tries to pull everything in your `docker-compose.yml`.
* With a list of named containers, it restricts itself to just those containers.
* Should usually be followed by:

	```bash
	$ UP
	$ docker system prune
	```

#### Alias: <a name="aliasREBUILD"> `REBUILD` {container …} </a>

```bash
$ docker-compose build --no-cache --pull {CONTAINER …}
```

* Can be thought of as a more powerful form of [`BUILD`](#aliasBUILD). It forces the download of any newer base image from DockerHub **and** forces the rebuild of the local image by applying the `Dockerfile`.
* Without arguments, it processes all service definitions with a `build` directive.
* With a list of named containers, it restricts itself to just those containers.
* Should usually be followed by:

	```bash
	$ UP
	$ docker system prune
	$ docker system prune
	```
	
	The first `prune` removes the older local image, the second `prune` the older base image.

#### Alias: <a name="aliasRECREATE"> `RECREATE` {container …} </a>

```bash
$ docker-compose up -d --force-recreate {CONTAINER …}
```

* Does a full tear down and reconstruction of the container. Unlike [`RESTART`](#aliasRESTART), it guarantees that the container starts the same as the image.
* Without arguments, it recreates all running services.
* With a list of named containers, it restricts itself to just those containers.

#### Alias: <a name="aliasRESTART"> `RESTART` {container …} </a>

```bash
$ docker-compose restart {CONTAINER …}
```

* A restart sends a signal to the container to restart itself. It's closer to restarting the process inside the container than tearing down and reconstructing the container. Unlike [`RECREATE`](#aliasRECREATE), any changes made inside the container persist.  
* Without arguments, it restarts all running services.
* With a list of named containers, it restricts itself to just those containers.

#### Alias: <a name="aliasS"> `S` </a>

```bash
$ cd ~/IOTstack/services
$ ls
```

* Changes the working directory to, and lists, the IOTstack *services* directory.
* See also [`I`](#aliasI), [`T`](#aliasT) and [`V`](#aliasV).

#### Alias: <a name="aliasT"> `T` </a>

```bash
$ cd ~/IOTstack/.templates
$ ls
```

* Changes the working directory to, and lists, the IOTstack *templates* directory.
* See also [`I`](#aliasI), [`S`](#aliasS) and [`V`](#aliasV).

#### Alias: <a name="aliasTERMINATE"> `TERMINATE` {container …} </a>

```bash
$ docker-compose rm --force --stop -v {CONTAINER …}
```

* Without arguments, it terminates all running services. The difference between this form and a a [`DOWN`](#aliasDOWN) is that the latter also destroys the internal networks.
* With a list of named containers, it restricts itself to just those containers.

#### Alias: <a name="aliasUP"> `UP` {container …} </a>

```bash
$ docker-compose up -d {CONTAINER …}
```

* Without arguments, it brings up your entire stack.
* With a list of named containers, it restricts itself to just those containers. 

#### Alias: <a name="aliasV"> `V` </a>

```bash
$ cd ~/IOTstack/volumes
$ ls
``` 

* Changes the working directory to, and lists, the IOTstack *volumes* directory.
* See also [`I`](#aliasI), [`S`](#aliasS) and [`T`](#aliasT).

### Container-specific aliases

#### Alias: <a name="aliasSHELL"> `«container»\_SHELL` </a>

This opens a shell within the container. For example:

```bash
$ INFLUX_SHELL
```

expands to:

```bash
$ docker exec -it influxdb bash
```

Opening a shell within a container means that the next thing you see is a prompt from the shell running inside the container, and with the **container's** view of the file system. `INFLUX_SHELL` will result in a prompt like this:

```bash
root@95b20550cb8a:/#
```

where the "#" indicates "you are running as root" (which is another way of saying "you don't need to use `sudo` for anything"). To get out of a container shell, either press <kbd>control</kbd>+<kbd>d</kbd> or type `exit` and press <kbd>return</kbd>.

Another benefit of using an alias scheme for common tasks is that you don't have to remember things like "most containers use `bash` but some, like Mosquitto, don't have `bash` so you need to use `ash` or `sh`, while others, like Portainer and Portainer-CE don't have any shell at all.

Notes:

* If you are trying to develop a "«container»\_SHELL" alias of your own and find that `bash` doesn't work, try replacing `bash` with `ash`, and then `sh`.

* If you're wondering about the `-it` option on «container»\_SHELL aliases, think of it as "interactive terminal". It's really `-i` (keep STDIN connected) and `-t` (allocate a pseudo-TTY for output). It is how `docker exec` knows to wait for human interaction, and why you have to type `exit` or press <kbd>control</kbd>+<kbd>d</kbd> to get out of a container. 

#### Alias: <a name="aliasInflux"> `influx` { argument …} </a>

The `influx` alias is an exception to my all-caps naming convention. It expands to:

```bash
$ docker exec -it influxdb influx -precision=rfc3339
```

It has the same effect as:

```
$ docker exec -it influxdb bash
# influx -precision=rfc3339
```

Or, in words:

1. Open a shell into the influxdb container, then
2. From inside the influxdb container, execute the `influx` command.

The `influx` command is InfluxDB's Command Line Interface.

The `-precision=rfc3339` option tells `influx` to display timestamps in human-readable form rather than nanoseconds since 1/1/1970. The timestamps are in UTC but you can tell `influx` to convert them to your local time by adding a `tz()` function to queries, like this:

```sql
SELECT * from «measurement» WHERE «criteria» tz('Australia/Sydney') 
```

You can append any other arguments supported by the `influx` CLI to the `influx` alias and the fact that `docker exec` is involved becomes entirely transparent. For example, all of these are valid:

```bash
$ influx
$ influx -database=«database name»
$ influx -type=flux
$ influx -database=«database name» -type=flux
```

#### Alias: <a name="aliasNODEREDDATA"> `NODERED_DATA` </a>

This alias expands to:

```bash
$ cd ~/IOTstack/volumes/nodered/data
$ ls
```

I often have the need to go into Node-Red's persistent data store to see what's what and you may need to do that as well.
