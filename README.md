Spring Cloud Gateway with sleuth Benchmark
=======

MacBook Pro (Retina, 15-inch, Mid 2015), no sleuth integrated

Proxy | Avg Latency | Avg Req/Sec/Thread
-- | -- | -- 
gateway | 13.75ms | 1.53k
zuul | 18.55ms | 1.27k
none | 0.87ms | 5.62k

MacBook Pro (Retina, 15-inch, Mid 2015), sleuth 2.0.3.RELEASE integrated

Proxy | Avg Latency | Avg Req/Sec/Thread
-- | -- | -- 
gateway | 47.91ms | 420.04
zuul | 24.85ms | 0.93k

MacBook Pro (Retina, 15-inch, Mid 2015), sleuth 2.1.0-BUILD-SNAPSHOT integrated

Proxy | Avg Latency | Avg Req/Sec/Thread
-- | -- | -- 
gateway | xx ms | xx k
zuul | 12.56ms | 2.09k

## Terminal 1 (simple webserver)

```bash
cd static
./webserver # or ./webserver.darwin-amd64 on a mac
```

## Terminal 2 (zuul)
```bash
cd zuul
./mvnw clean package
java -jar target/zuul-0.0.1-SNAPSHOT.jar 
```

## Terminal 3 (gateway)
```bash
cd gateway
./mvnw clean package
java -jar target/gateway-0.0.1-SNAPSHOT.jar 
```

## Terminal N (wrk)

### install `wrk`
Ubuntu: `sudo apt install wrk`

Mac: `brew install wrk`

NOTE: run each one multiple times to warm up jvm

### Gateway bench (8082)
```bash
$ wrk -t 10 -c 200 -d 30s http://localhost:8082/hello.txt
Running 30s test @ http://localhost:8082/hello.txt
  10 threads and 200 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency    13.75ms    9.22ms 154.98ms   84.60%
    Req/Sec     1.53k   144.28     1.89k    83.47%
  457689 requests in 30.05s, 63.29MB read
  Socket errors: connect 0, read 53, write 0, timeout 0
Requests/sec:  15232.68
Transfer/sec:      2.11MB
```

### zuul bench (8081)
```bash
~% wrk -t 10 -c 200 -d 30s http://localhost:8081/hello.txt
Running 30s test @ http://localhost:8081/hello.txt
  10 threads and 200 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency    18.55ms   17.56ms 255.92ms   82.69%
    Req/Sec     1.27k   236.91    10.36k    89.74%
  379894 requests in 30.10s, 58.40MB read
  Socket errors: connect 0, read 83, write 0, timeout 0
Requests/sec:  12623.04
Transfer/sec:      1.94MB
```

### no proxy bench (8000)
```bash
~% wrk -t 10 -c 200 -d 30s http://localhost:8000/hello.txt
Running 30s test @ http://localhost:8000/hello.txt
  10 threads and 200 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     0.87ms  460.43us  20.91ms   83.89%
    Req/Sec     5.62k     3.00k   12.15k    60.43%
  1679137 requests in 30.01s, 232.20MB read
  Socket errors: connect 0, read 148, write 0, timeout 0
Requests/sec:  55958.10
Transfer/sec:      7.74MB
```
