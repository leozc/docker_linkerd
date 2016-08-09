# Runtime
Tested on [docker for mac](https://docs.docker.com/engine/installation/mac/)

# Setup and commands
## Store Dtab in consul
This dtab is a proof of concept to mix static bound name(nginx_1 and microsoft), consul based dynamic name resolving and file system based resolving. For now the resolving order is *TBA*.

Open your browser localhost:8500 add a key *namerd/dtabs/default* and put in following dtabs
```
/host => /#/io.l5d.fs;
/host/nginx  => /$/inet/nginx_1/80;
/host/ms   => /$/inet/microsoft.com/80;
/srv => /#/io.l5d.consul/dc1;
/host => /srv;
/http/1.1/* => /host;
```

## Building a shortcut
alias proxy="docker-compose -f proxy.yml  -p linkerd"

## Set up docker composer alias
> alias proxy='docker-compose --project-name proxy -f $YOURPROJECT/proxy.yml'

## To start
> proxy up -d

## To inspect the logs
> proxy logs -f

# Administration and Web UI
## namerd web interface
http://localhost:9991/dtab/default

## linkerd web interface
http://localhost:9990/

# Load test (Non-scientific-works-on-my-machine version)
Each of the following test has been repeated 3 times and the medium is picked.
## Direct
> wrk -c 10 -d 60s -t 10 --latency -H 'Host: nginx_1' http://localhost:8001
```
Running 1m test @ http://localhost:8001
  10 threads and 10 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     2.54ms   12.68ms 312.76ms   99.41%
    Req/Sec   597.46    164.55     1.14k    70.57%
  Latency Distribution
     50%    1.58ms
     75%    2.07ms
     90%    2.65ms
     99%    8.37ms
  356048 requests in 1.00m, 288.60MB read
  Socket errors: connect 0, read 10, write 0, timeout 0
Requests/sec:   5929.09
Transfer/sec:      4.81MB
```
## Test proxy performance through linkerd (pre-warmed JVM)
> wrk -c 10 -d 60s -t 10 --latency -H 'Host: nginx' http://localhost:4140
```
Running 1m test @ http://localhost:4140
  10 threads and 10 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     3.43ms    5.07ms 156.13ms   94.85%
    Req/Sec   352.78     91.02   676.00     65.27%
  Latency Distribution
     50%    2.43ms
     75%    3.85ms
     90%    6.16ms
     99%   16.12ms
  210645 requests in 1.00m, 165.93MB read
Requests/sec:   3505.07
Transfer/sec:      2.76MB
```
## Test through nginx_proxy
> wrk -c 10 -d 60s -t 10 --latency -H 'Host: nginx_1' http://localhost:8888
```
Running 1m test @ http://localhost:8888
  10 threads and 10 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     3.03ms    1.45ms  46.56ms   93.13%
    Req/Sec   339.67     62.03   535.00     70.03%
  Latency Distribution
     50%    2.79ms
     75%    3.30ms
     90%    3.99ms
     99%    8.20ms
  203099 requests in 1.00m, 164.63MB read
Requests/sec:   3381.53
Transfer/sec:      2.74MB
```

# Proof working with consul name binding
curl -H'host:consul'  http://localhost:4140
