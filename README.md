# ResearchSpace for Desktop using Docker

Below is a general overview of how you can run ResearchSpace on your local machine using Docker containers. If you prefer a native/manual setup, you’ll need to install Java 11 and configure it directly on your system (see details [here](https://documentation.researchspace.org/resource/rsp:Documentation_Download)). The official [ResearchSpace documentation](http://documentation.researchspace.org) is created and maintained by Kartography CIC. 


# Overview

<!--ts-->

- [Installing Docker](#installing)
- [Installing the ResearchSpace platform](#installing-the-researchspace-platform)
- [Configuring your docker setup using the .env file](#configuring-your-docker-setup-using-the-env-file)
- [Backup Data, Images and other Files](#backup-data-images-and-other-files)
- [Troubleshooting](#troubleshooting)   
    * [Memory Issues](#memory-issues)
    * [Port Conflicts](#port-conflicts)  
    * [Logs](#logs)
<!--te-->

# Installing Docker

The easiest way to install ```docker``` and ```docker compose``` is using [Docker Desktop](https://www.docker.com/products/docker-desktop/) available for free for personal use.
Depending on the operating system running on your local machine you can follow [these steps](docker_installations.md).


# Installing the ResearchSpace platform

## Download this repository on your local machine
```git clone git@github.com:researchspace/researchspace-docker-desktop.git```

```cd yourDirectoryPath/researchspace-docker-desktop```

## Fix permissions on Ubuntu
If using UBUNTU, run the following two commands; Not necessary for OS X setups. You may need to run these commands with ```sudo```
```
chmod +x ./fix-folder-permissions.sh
./fix-folder-permissions.sh
```

## Start ResearchSpace 

```docker compose up -d```

The platform takes some time to start loading the first time, so check the platform loading status with command:

```docker logs localhost-researchspace-1```

The platform has started when the logs end with the following message

```INFO:oejs.AbstractConnector:main: Started ServerConnector@40fd1a78{HTTP/1.1, (http/1.1)}{0.0.0.0:8080}```
```INFO:oejs.Server:main: Started```

If you don't see the message above, rerun the command ```docker logs localhost-researchspace-1``` until that message is displayed or an error message is shown.

Open your browser and go to [http://127.0.0.1:10214/login](http://127.0.0.1:10214). You can now access the platform with admin/admin

## Stop ResearchSpace

```docker compose down```


# Configuring your docker setup using the .env file

The *.env* is a simple text file that holds environment variables in the format KEY=VALUE. ```docker compose``` automatically reads this file and substitutes any matching ${KEY} references in your docker-compose.yml
Use this file to specify the memory allocations or Java runtime parameters.

# Backup Data, Images and other Files

Once the system starts a set of folders and files are automatically generated. Data is stored in a triplestore, while images, files, and other ResearchsSpace created resources are placed in the ```researchspace/runtime-data``` folder. 
When creating a backup of your docker setup make sure the folders below are included. 

## Data in Blazegraph Triplestore
Your data is saved in a special file, called a journal, you can find it on your machine's local filesystem at:
```cd blazegraph/blazegraph.jnl```

## Images and other Files 
All files from the current running application are stored in the  ```researchspace/runtime-data```

# Troubleshooting

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
