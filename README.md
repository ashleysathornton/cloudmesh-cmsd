# Cloudmesh cmsd

Cloudmesh `cmsd` is a command to run cloudmesh in a container regardless
of the OS. Thus it is extremely easy to install and use in case your
machine has docker installed.

`cmsd` uses the locally installed keys in `~/.ssh` and typically
cloud configurations stored in `~/.cloudmesh/cloudmesh.yaml`. This YAML
file is created upon the first call of `cmsd` if it is not available.


### Prerequesites

* Docker
* Python 3.8 or newer
* We strongly recommended using a python virtual environment
* ssh public key in ~/.ssh/id_rsa.pub

## How to use *cmsd*

Important. You must have cms in debug off mode. to use the cmsd command

```
$ cms debug off
```


### User installation

Please use a python virtualenv as to not interfere with your system
python and activate your python venv.

Linux, osx: 

```bash
$ python3.8 -m venv ~/ENV3
$ source ~/ENV3/bin/activate
$ pip install pip -U 
```

In Windows, you can do this with 

```bash
$ python -m venv ENV3
$ ENV3\Scripts\activate
$ pip install pip -U 
```

Now you can install cloudmesh `cmsd` with 

```bash
$ pip install cloudmesh-cmsd
```
    

### Developer Source installation    

Developers install `cmsd` from source with our 
`cloudmesh-installer`

Linux, osx: 

```bash
$ python3.8 -m venv ~/ENV3
$ source ~/ENV3/bin/activate
$ pip install pip -U
$ mkdir cm   
$ cd cm  
$ pip install cloudmesh-installer -U 
$ cloudmesh-installer git clone cmsd 
$ cloudmesh-installer install cmsd   
```

Windows:

```bash
$ python -m venv ENV3
$ ENV3\Scripts\activate
$ pip install pip -U
$ mkdir cm   
$ cd cm  
$ pip install cloudmesh-installer -U 
$ cloudmesh-installer git clone cmsd 
$ cloudmesh-installer install cmsd   
```

You will see in the `cm` directory a number of cloudmesh related
repositories. One of them is `cloudmesh-cmsd` in which the cmsd command
related code is stored. The other repositories contain code that may be
used by `cloudmesh-cmsd`.

### Defult setup


To run `cmsd`, you need a configuration directory that is mounted
into the container.  The default setup is done with 

```bash
$ cmsd --setup
```

This will set up a number of configurations including a cloudmesh 
configuration YAML file in 

* masOS and Linux: `~/.cloudmesh/cloudmesh.yaml` 
* Windows `%USERPROFILE%\.cloudmesh\cloudmesh.yaml`

You are asked to enter some details that are required for the setup,
such as profile details, Mongo DB credentials.

### Custom cmsd setup 

In case you need to place the configuration files elsewhere you can
specify the location with the environment variable
`CLOUDMESH_CONFIG_DIR`. 

For macOS and Linux you set it with:

```
$ export CLOUDMESH_CONFIG_DIR=<path to CLOUDMESH_HOME_DIR>
```

For Windows you set it with:

```
> set CLOUDMESH_CONFIG_DIR=<path to CLOUDMESH_HOME_DIR>
```

> **Note:** avoid spaces:
> 
> `CLOUDMESH_CONFIG_DIR` path must not have in any spaces in it.
>  For example `C:\.cloudmesh` will work, so does 
> `C:\Users\gregor\.cloudmesh`, but not `C:\Users\gregor von Laszewski\.cloudmesh`, 
> as it includes a space in th eusername.

> **Note:** grant access:
>
> Make sure that the drive of the `CLOUDMESH_CONFIG_DIR` is granted file 
> access in Docker settings

Next, you run the setup just like in the default case. If you are running
setup on an empty `CLOUDMESH_CONFIG_DIR`,  you will be asked to enter
some details that are required for the setup, such as profile details,
Mongo DB credentials.

```  
$ cmsd --setup 
```

### Containers


It the setup installs cloudmesh into two containerscontainers. The containers are called

- `cloudmesh-cms` for the cms command
- `cloudmesh-mongo`  for the mongodb that is used by cms


Run the command

```
$ cmsd --ps
```

to see if the containers are running. Additionally, check
`CLOUDMESH_CONFIG_DIR` or ~/.cloudmesh contains the `cloudmesh.yaml`
file, dependent where you asked `cmsd` to look for it.

### Commands

To list the containers, please use

```
$ cmsd --ps
```

Run the following to verify if the configurations you entered have been 
properly reflected in the `cloudmesh.yaml` file. 

```
$ cmsd config cat
```

To initialize the cloudmesh database use

```
$ cmsd init
```

To test if things are working use

```
$ cmsd key list 
```

To stop the containers use

```
$ cmsd --stop
```

To start the containers use

```
$ cmsd --start
```

To remove the containers use

```
$ cmsd --clean
```

To login to the container via a shell use

```
$ cmsd --shell
```


### Example Usecase - Creating a vm in Chameleon Cloud 

To modify the parameters use the command

```
cmsd --gui quick
```

and make sure the MongoDB MODE is set to `running`. This is automatically
done by the setup. Make sure you add your username and password, as
well as the network id and the project id and name. Test if it works with 


```
cmsd flavor list --refresh
```

### Example Usecase - Creating a vm in AWS 

Create an AWS account and add the authentication information in the
`CLOUDMESH_HOME_DIR/cloudmesh.yaml` file. Please see the [Cloudmesh
Manual -
AWS](https://cloudmesh.github.io/cloudmesh-manual/accounts/aws.html)
form more details about AWS.

Set the cloud to `aws`

```
$ cmsd set cloud=aws 
```


```
$ cmsd key upload --cloud=aws
```

where the key name is specified by 

```
cms var key
```

or

```
cms config get cloudmesh.profile.user
```

or 

Make sure you have an ssh key generated prior to booting a vm with the
default configuration with

```
$ cmsd vm boot 
```

### MongoDB and Mongo client connections  

`cmsd` is running an official MongoDB container from
[DockerHub](https://hub.docker.com/_/mongo).

The Mongo server container is bound to `127.0.0.1:27071`. You can use
use any Mongo client to explore the database by connecting to this port.


### Using cmsd to spawn a mongo container/ service 

You can use `cmsd` to start a mongo container as a service. 

- Setup `CLOUDMESH_CONFIG_DIR` environment variable, as perviously (If not set, this would take the `~/.cloudmesh` as default)

- Clean cmsd containers 
```
cmsd --clean 
```

- Use setup commad with `--mongo` flag 
```
cmsd --setup --mongo 
```

- Now you should have a mongoDB server bound to `127.0.0.1:27071` (as a container)

- Starting, stopping containers would be done as mentioned in the previous section. 


## Manual Page

```bash
Usage:

    cmsd --help
    cmsd --setup [--mongo]
    cmsd --clean
    cmsd --version
    cmsd --update
    cmsd --start
    cmsd --stop
    cmsd --ps
    cmsd --gui COMMAND...
    cmsd --shell
    cmsd --pipe
    cmsd COMMAND...

  This command passes the arguments to a docker container
  that runs cloudmesh.

  Arguments:
      COMMAND the commands we bass along
  
  Description:
  
    cmsd --help
        prints this manual page
  
    cmsd --setup [--mongo]
        sets up cmsd containers.
        If --mongo flag is passed, only the mongo container will be
        setup.
  
    cmsd --clean
        stops and removes cmsd containers
  
    cmsd --version
        prints out the version of cmsd and the version of the container
  
    cmsd --update
        updates the cloudmesh repositories inside the cms-container
  
    cmsd --start
        starts cmsd containers
  
    cmsd --stop
        stops cmsd containers
  
    cmsd --ps
        lists the container processes
  
    cmsd --gui help
        find out which gui commands are available
    
    cmsd --gui quick
        runs cloudmesh gui on the docker container
    
    cmsd --shell
        enters the cms container and starts an interactive shell
    
    cmsd --pipe
        You can pipe commands or scripts to the cmsd container
            echo "banner a" | cmsd --pipe
    
    cmsd COMMAND
        The command will be executed within the container, just as in
        case of cms.
    
    cmsd
        When no command is specified, cmsd will be run in interactive
        mode. 
```

## Quickstart

### macOS with python 3.8.1 from python.org

1. Requirements:

   * Have a username without a space.
   * Have docker installed and accessible to the user. 
   * Have python 3.8.1 from python.org installed.
   * Create a key `~/.ssh/id_rsa` if you do not already have one 
   
   ```bash
   $ ssh-keygen
   ```

2. Install:

   In a new terminal execute

   ```bash
   $ python3.8 -m venv ~/ENV3
   $ source ~/ENV3/bin/activate
   $ pip install cloudmesh-cmsd
   $ cmsd --setup
   $ cmsd init
   $ cmsd help
   ```
   
   Output:
   
   ```
   Documented commands (type help <topic>):
   ========================================
   EOF       config     help       man        quit      ssh        vcluster      
   admin     container  host       open       register  start      version       
   aws       data       image      openstack  sec       stop       vm            
   azure     debug      info       pause      service   stopwatch  workflow_draft
   banner    default    init       plugin     set       sys      
   check     echo       inventory  provider   shell     test     
   clear     flavor     ip         py         sleep     var      
   commands  group      key        q          source    vbox 
   ```
   
   Testing banner command:
   
   ```bash
   $ cmsd banner hello
   ```
   
   Output: 
   
   ```
   banner
   ######################################################################
   # hello
   ######################################################################
   ```
   
   Testing sec command:
   
   ```bash
   $ cmsd sec rule list
   ```
   
   Output: 
   
   ```
   +-------+----------+-----------+-----------+
   | Name  | Protocol | Ports     | IP Range  |
   +-------+----------+-----------+-----------+
   | ssh   | tcp      | 22:22     | 0.0.0.0/0 |
   | icmp  | icmp     |           | 0.0.0.0/0 |
   | flask | tcp      | 8000:8000 | 0.0.0.0/0 |
   | http  | tcp      | 80:80     | 0.0.0.0/0 |
   | https | tcp      | 443:443   | 0.0.0.0/0 |
   +-------+----------+-----------+-----------+
   ```
   
## Demonstration of the different uses of cmsd

1. Commandline

   ```bash
   $ cmsd banner hallo
   
   banner
   ######################################################################
   # hello
   ######################################################################
   ```

2. Pipe

   ```
   $ echo "banner hello" | cmsd --pipe
   
   +-------------------------------------------------------+
   |   ____ _                 _                     _      |
   |  / ___| | ___  _   _  __| |_ __ ___   ___  ___| |__   |
   | | |   | |/ _ \| | | |/ _` | '_ ` _ \ / _ \/ __| '_ \  |
   | | |___| | (_) | |_| | (_| | | | | | |  __/\__ \ | | | |
   |  \____|_|\___/ \__,_|\__,_|_| |_| |_|\___||___/_| |_| |
   +-------------------------------------------------------+
   |                  Cloudmesh CMD5 Shell                 |
   +-------------------------------------------------------+
   
   cms> banner
   ######################################################################
   # hello
   ######################################################################   ```
   ```
   
3. Interactive

   ```
   $ cmsd
   start cms interactively
   
   +-------------------------------------------------------+
   |   ____ _                 _                     _      |
   |  / ___| | ___  _   _  __| |_ __ ___   ___  ___| |__   |
   | | |   | |/ _ \| | | |/ _` | '_ ` _ \ / _ \/ __| '_ \  |
   | | |___| | (_) | |_| | (_| | | | | | |  __/\__ \ | | | |
   |  \____|_|\___/ \__,_|\__,_|_| |_| |_|\___||___/_| |_| |
   +-------------------------------------------------------+
   |                  Cloudmesh CMD5 Shell                 |
   +-------------------------------------------------------+
   
   cms> banner hello
   banner
   ######################################################################
   # hello
   ######################################################################
   cms> quit
   ```
 
 4. Access container shell for development
 
    ```bash
    $ cmsd --shell

    root@docker-desktop:/cm# ls -1
    cloudmesh-aws
    cloudmesh-azure
    cloudmesh-cloud
    cloudmesh-cmd5
    cloudmesh-common
    cloudmesh-configuration
    cloudmesh-inventory
    cloudmesh-openstack
    cloudmesh-sys
    cloudmesh-test
    root@docker-desktop:/cm# 
    ```
