# HAProxy as an API Gateway

## Install HAProxy  

Before we can work with HAProxy, we need to install it:  

```
sudo apt -y install haproxy
```

Let's modify our /etc/haproxy/haproxy.cfg configuration file:  

```
# Site 1 Backend
backend site1
	balance roundrobin
	server site1-web1 192.168.2.25:8081 check
	server site1-web2 192.168.2.25:8082 check
	server site1-web3 192.168.2.25:8083 check
# Site 2 Backend
backend site2
	balance roundrobin
	server site2-web1 192.168.2.25:8084 check
	server site2-web2 192.168.2.25:8085 check
	server site2-web3 192.168.2.25:8086 check

# Site Frontends
frontend site1
	bind *:8000
	default_backend site1
frontend site2
	bind *:8100
	default_backend site2

# Single frontend for http on port 80
frontend http-in
	bind *:80
	mode http
	acl site-1 hdr(host) -i site1.homelab.com
	acl site-2 hdr(host) -i site2.homelab.com
	use_backend site1 if site-1
	use_backend site2 if site-2
```

Restart HAProxy:  

```
sudo systemctl restart haproxy
```

Add to `/etc/hosts`:  

```
192.168.2.23 site1.homelab.com site2.homelab.com
```

## Create Some Test Files  

We're going to need some test files for our web server containers. We're going to use 6 containers in 2 groups
of 3. We'll use the figlet command to spice up ourtext files a bit!  

Let's create the files:  

```
mkdir -p ~/testfiles
```

```
for site in `seq 1 2`; \
  do for server in `seq 1 3`; \
    do figlet -f big SITE$site - WEB$server > ~/testfiles/site$site\_server$server.txt ; \
  done ; \
done
```

## Start Some Web Server Containers

Ok, so now that we've got the odds and ends covered, let's stand up our web server containers. We're going to
start a total of 6 `nginx` containers using Docker, simulating 2 sites, with 3 web servers per site. Our web
servers will be available on ports `8081` through `8086`.  

Let's start our containers:  

```
port=1 ; \
for site in `seq 1 2`; \
  do for server in `seq 1 3`; \
    do sudo docker run -dt --name site$site\_server$server -p 808$(($port)):80 docker.io/library/nginx ; \
    port=$(($port + 1 )) ; \
  done ; \
done
```

Copy our Test files:  

```
for site in `seq 1 2`; \
  do for server in `seq 1 3`; \
    do sudo docker cp ~/testfiles/site$site\_server$server.txt site$site\_server$server:/usr/share/nginx/html/test.txt ; \
  done ; \
done
```

Test our Web services:  

```
port=1 ; \
for site in `seq 1 2`; \
  do for server in `seq 1 3`; \
    do curl -s http://127.0.0.1:808$port/test.txt ; \
    port=$(($port + 1 )) ; \
  done ; \
done | more
```