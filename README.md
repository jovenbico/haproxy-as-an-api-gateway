# HAProxy as an API Gateway

Create Some Test Files  
We're going to need some test files for our web server containers. We're going to use 6 containers in 2 groups
of 3. We'll use the figlet command to spice up ourtext files a bit!  
Let's create the files:  

```
mkdir -p ~/testfiles
```

```
for site in `seq 1 2`; do for server in `seq 1 3`; do figlet -f big SITE$site - WEB$server > ~/testfiles/site$site\_server$server.txt ; done ; done
```