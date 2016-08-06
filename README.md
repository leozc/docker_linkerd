# Set up docker composer alias
> alias proxy='docker-compose --project-name proxy -f $YOURPROJECT/proxy.yml'

## To start
> proxy up -d

## to look at the logs =
> proxy logs -f

## to look at the containers
> proxy ps

## add a key in consul
Open your browser localhost:8500 add a key *namerd/dtabs/default* and put in following dtabs
```
/host/nginx  => /$/inet/nginx_1/80;
/host/ms   => /$/inet/microsoft.com/80;
/http/1.1/* => /host;
```

# namerd web interface
http://localhost:9991/dtab/default

# linkerd web interface =
http://localhost:9990/

# load test
## Test proxy performance through linkerd
Strongly suggest running the following test once to allow JVM finishing the JIT
> wrk -c 10 -d 60s -t 10 --latency -H 'Host: nginx' http://localhost:4140
## through nginx_proxy
> wrk -c 10 -d 60s -t 10 --latency -H 'Host: nginx' http://localhost:8888
