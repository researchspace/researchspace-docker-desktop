# A ResearchSpace setup for desktop using Docker

Below is a general overview of how you can run ResearchSpace on your local machine using Docker containers. If you prefer a native/manual setup, you’ll need to install Java 11 and configure it directly on your system (see details [here](http://ziphowtoreadme)). The official [ResearchSpace documentation](http://documentation.researchspace.org) is created and maintained by Kartography CIC. 


# Overview

<!--ts-->

- [Installing Docker](#installing)
- [How to Start and Stop the ResearchSpace platform](#basic-commands-to-start-and-stop-researchspace-the-researchspace-platform)
- [Where are my Data, Images and other Files stored](#where-are-my-data-images-and-other-files-stored)
- [Troubleshooting](#troubleshooting)
    * [Fix permissions on Ubuntu](#fix-permissions-on-ubuntu)
    * [Memory Issues](#memory-issues)
    * [Port Conflicts](#port-conflicts)  
    * [Logs](#logs)
<!--te-->

# Installing Docker

The easiest way to install ```docker``` and ```docker compose``` is using [Docker Desktop](https://www.docker.com/products/docker-desktop/) available for free for personal use.
Depending on the operating system running on your local machine you can follow [these steps](docker_installations.md).
    
# How to Start and Stop the ResearchSpace platform

### Download this repository on your local machine
```git clone git@github.com:researchspace/researchspace-docker-desktop.git```

```cd yourDirectoryPath/researchspace-docker-desktop```

### Start ResearchSpace 

```docker compose up -d```

The platform takes some time to start loading the first time, so check the platform loading status with command:

```docker logs localhost-researchspace-1```

The platform has started when the logs end with the following message

```INFO:oejs.AbstractConnector:main: Started ServerConnector@40fd1a78{HTTP/1.1, (http/1.1)}{0.0.0.0:8080}```
```INFO:oejs.Server:main: Started```

If you don't see the message above, rerun the command ```docker logs localhost-researchspace-1``` until that message is displayed or an error message is shown.

Open your browser and go to [http://127.0.0.1:10214/login](http://127.0.0.1:10214). You can now access the platform with admin/admin

### Stop ResearchSpace

```docker compose down```


### .env file

Set `COMPOSE_PROJECT_NAME` in the *.env* file.

# Where are my Data, Images and other Files stored
## Data -- Blazegraph Triplestore
Your data is saved in a special file, called a journal, you can find it on your machine's local filesystem at:
```cd blazegraph/blazegraph.jnl```

## Images and other Files 
Uploaded images are stored in:  ```researchspace/runtime-data/images```

# Troubleshooting
### Fix permissions on Ubuntu
If using UBUNTU, run the following two commands; Not necessary for OS X setups. You may need to run these commands with ```sudo```
```
chmod +x ./fix-folder-permissions.sh
./fix-folder-permissions.sh
```

Don't forget to restart the service stack ```docker-compose up -d```

### Memory Issues
If ResearchSpace or the triplestore doesn't have enough memory to start, you may see `OutOfMemoryError` or time out errors. Increase allocated memory via Docker Compose or the `-Xmx` Java option.

### Port Conflicts  
Ensure the ports used by ResearchSpace (`10214`) and your triplestore (`10215`) are not in use by other apps.

### Logs
- Docker logs or the server logs will show detailed stack traces if there’s a problem.  
- If you can’t connect to the triple store, check their container's logs too.

List all running docker containers:
```docker ps -a```

View specific logs:
```docker logs <yourdockercontainerid>```
