This application is a sample voting app. The application has 3 components namely, voting-app, worker node and the result app.
The voting app is a pyhton based application which is interacting with a redis container for casting and temporarily storing the vote.
The worker node is talking to the redis and the postgres database where the vote gets stored.
The result app is a node-js application which displays the casted vote read from the db to the UI.

To run this application, we need 5 containers to be run in the respective order: redis, vote, db, worker, result.
Use the following commands for running the containers:
```
docker run -d --name=redis redis

docker run -d -p 5000:80 --name=vote --link redis:redis burhanhusain/votingapp_vote

docker run -d --name=db postgres:9.4

docker run -d --name=worker --link redis:redis --link db:db burhsnhusain/worker-app

docker run -d -p 5001:80 --name=result --link db:db burhanhusain/result-app
```
Run in this directory:
```
docker-compose up
```
The app will be running at [http://localhost:5000](http://localhost:5000), and the results will be at [http://localhost:5001](http://localhost:5001).

Alternately, if you want to run it on a [Docker Swarm](https://docs.docker.com/engine/swarm/), first make sure you have a swarm. If you don't, run:
```
docker swarm init
```
Once you have your swarm, in this directory run:
```
docker stack deploy --compose-file docker-stack.yml vote
```

Architecture
-----

![Architecture diagram](architecture.png)

* A Python webapp which lets you vote between two options
* A Redis queue which collects new votes
* A .NET worker which consumes votes and stores them in…
* A Postgres database backed by a Docker volume
* A Node.js webapp which shows the results of the voting in real time
