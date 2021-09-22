# HAProxy as an API Gateway

## Create Some Test Files  

We're going to need some test files for our web server containers. We're going to use 6 containers in 2 groups
of 3. We'll use the figlet command to spice up ourtext files a bit!  

Let's create the files:  

```
mkdir -p ~/testfiles
```

```
for site in `seq 1 2`; do for server in `seq 1 3`; do figlet -f big SITE$site - WEB$server > ~/testfiles/site$site\_server$server.txt ; done ; done
```

## Start Some Web Server Containers

Ok, so now that we've got the odds and ends covered, let's stand up our web server containers. We're going to
start a total of 6 `nginx` containers using Docker, simulating 2 sites, with 3 web servers per site. Our web
servers will be available on ports `8081` through `8086`.  

Let's start our containers:  

```
port=1 ; for site in `seq 1 2`; do for server in `seq 1 3`; do sudo docker run -dt --name site$site\_server$server -p 808$(($port)):80 docker.io/library/nginx ; port=$(($port + 1 )) ; done ; done
```

Copy our Test files:  

```
for site in `seq 1 2`; do for server in `seq 1 3`; do sudo docker cp ~/testfiles/site$site\_server$server.txt site$site\_server$server:/usr/share/nginx/html/test.txt ; done ; done
```

Test our Web services:  

```
port=1 ; for site in `seq 1 2`; do for server in `seq 1 3`; do curl -s http://127.0.0.1:808$port/test.txt ; port=$(($port + 1 )) ; done ; done | more
```