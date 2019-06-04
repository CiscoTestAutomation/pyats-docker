[![published](https://static.production.devnetcloud.com/codeexchange/assets/images/devnet-published.svg)](https://developer.cisco.com/codeexchange/github/repo/CiscoTestAutomation/pyats-docker)

# pyATS Dockerfile and Scripts

This is the Git repo for the official pyATS test framework Docker image. This 
repository contains Dockerfiles and scripts used to build the actual image, 
available as `ciscotestautomation/pyats` tag on Dockerhub.

## General Information

- Website: https://developer.cisco.com/site/pyats/
- Documentation: https://developer.cisco.com/site/pyats/docs/
- Dockerhub: https://hub.docker.com/r/ciscotestautomation/pyats/

## How to Use the pyATS Docker Image

#### Downloading the Image

Downloading the pyATS image in a separate step is not strictly necessary, but is
a good practise to ensure your local image is always kept up-to-date.

```
$ docker pull ciscotestautomation/pyats:latest
```

where the `latest` tag can be replace with the specific version of pyATS you 
need. 

#### Starting the pyATS Container

The pyATS docker container defaults to starting in Python interactive shell. 

```
$ docker run -it ciscotestautomation/pyats:latest
[Entrypoint] Starting pyATS Docker Image ...
[Entrypoint] Workspace Directory: /pyats
[Entrypoint] Activating workspace
Python 3.4.7 (default, Nov  4 2017, 22:21:42)
[GCC 4.9.2] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>>
```

Alternatively, you can also start the container in shell,
```
$ docker run -it ciscotestautomation/pyats:latest /bin/bash
[Entrypoint] Starting pyATS Docker Image ...
[Entrypoint] Workspace Directory: /pyats
[Entrypoint] Activating workspace
root@0c832ac21322:/pyats#
```

The pyATS virtual environment is sourced automatically, and your workspace is
preset to be `/pyats`. Note that this workspace directory (virtual environment) 
is declared to be a docker volume, so its content will persist between container
reloads.

To get out of the container, try `CTRL-D`.

#### Examples and Templates

Examples and templates are built into the image under `/pyats` default workspace
to help users on getting started. 

```
$ docker run -it ciscotestautomation/pyats:latest /bin/bash
[Entrypoint] Starting pyATS Docker Image ...
[Entrypoint] Workspace Directory: /pyats
[Entrypoint] Activating workspace
root@0c832ac21322:/pyats# easypy examples/basic/job/basic_example_job.py
```

## Customizing Your Container

You can use the following built-in mechanisms to customize your container before
startup, and have your environment setup automatically.

#### requirements.txt

To populate your newly started containers with pip packages, you can customize
your container by mounting a pip requirements file to `/pyats/requirements.txt`.
When a container is first created, this required package file is automatically 
provided to `pip` to fulfill.

```
$ docker run -it -v /your/requirements.txt:/pyats/requirements.txt ciscotestautomation/pyats:latest
[Entrypoint] Starting pyATS Docker Image ...
[Entrypoint] Workspace Directory: /pyats
[Entrypoint] Activating workspace
[Entrypoint] Installing pip packages: /pyats/requirements.txt
Collecting requests==2.12.3 (from -r /pyats/requirements.txt (line 1))
...
```

#### workspace.init

For any other customization you need to do to your container workspace, such
as pulling git repositories and setting up source code in development mode, 
you can mount a custom bash script to `/pyats/workspace.init`. This file is
automatically executed as part of container initial creation.

```

$ docker run -it -v /your/workspace.init:/pyats/workspace.init ciscotestautomation/pyats:latest
[Entrypoint] Starting pyATS Docker Image ...
[Entrypoint] Workspace Directory: /pyats
[Entrypoint] Activating workspace
[Entrypoint] Running workspace init: /pyats/workspace.init
custom initialization
...
```

For a more elaborate example of the `workspace.init` file, see `templates/` 
folder under this repository.
