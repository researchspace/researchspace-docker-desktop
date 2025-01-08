# ResearchSpace with blazegraph and digilib

## Setup

Set `COMPOSE_PROJECT_NAME` in the *.env* file.

If using UBUNTU, run the following two commands; Not necessary for OS X setups.
```
chmod +x ./fix-folder-permissions.sh
./fix-folder-permissions.sh
```

Start the service stack
```
docker-compose up -d
```

Check in the terminal if any issues appeared initiating the docker containers:  
```
docker ps -a 
docker logs <yourdockercontainerid>
```

Go to your browser (Chrome, Firefox) and login with admin/admin at:
```
http://localhost:10214
```

## Configuration
The previous steps allowed you to setup an empty instance of ResearchSpace with a basic service stack: platform, blazegraph, and digilib.

