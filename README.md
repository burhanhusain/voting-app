This application is a sample voting app. The application has 3 components namely, voting-app, worker node and the result app.
The voting app is a pyhton based application which is interacting with a redis container for casting and temporarily storing the vote.
The worker node is talking to the redis and the postgres database where the vote gets stored.
The result app is a node-js application which displays the casted vote read from the db to the UI.
TO build the docker images navigate through the directory of each app which has their respective Dockerfiles run run the following commands:
```
docker build . -t votingapp_vote

docker build . -t votingapp_worker

docker build . -t votingapp_result
```

To run this application, we need 5 containers to be run in the respective order: redis, vote, db, worker, result.
Use the following commands for running the containers:
```
docker run -d --name=redis redis

docker run -d -p 5000:80 --name=vote --link redis:redis burhanhusain/votingapp_vote

docker run -d --name=db postgres:9.4

docker run -d --name=worker --link redis:redis --link db:db burhsnhusain/votingapp_worker

docker run -d -p 5001:80 --name=result --link db:db burhanhusain/votingapp_result
```
Alternatively, instead of running the seperate commands for each container we can usr the docker compose to bring up the entire application stack using the following command:
```
docker-compose up
```
The app will be running at [http://localhost:5000](http://localhost:5000), and the results will be at [http://localhost:5001](http://localhost:5001).


For Step 2, the container platform can be deployed using the Docker Swarm which can be used to deploy the application to AWS.

Within the AWS comsole a VPC needs to be created as the Swarm cluster. There should be minimum of two subnets so that the ELB could maintain the availability of the Swarm cluster.
The different EC2 isntances will have a Swarm manager and the rest worker nodes.
Once the swarm master and worker nodes are registered the Swarm can be initiated.
In order to enable continuous deployment, the Jenkins or any other CI server can be setup by copying the certificates that generated when the swarm manager was initiated, to the Jenkins home.
In the Jenkins deployment script, the Docker host and the Certificate path needs to be mentioned so that Jenkins can access the Swarm manager. The rest of the nodes are managed by Swarm itself.

Architecture
-----

![Architecture diagram](architecture.png)

* A Python webapp which lets you vote between two options
* A Redis queue which collects new votes
* A .NET worker which consumes votes and stores them in…
* A Postgres database backed by a Docker volume
* A Node.js webapp which shows the results of the voting in real time
