
## mpm_prefork
root@apache:/etc/apache2# ab -n 5000 -c 500 -k http://192.168.1.186:80/large.html
This is ApacheBench, Version 2.3 <$Revision: 1879490 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking 192.168.1.186 (be patient)
Completed 500 requests
Completed 1000 requests
Completed 1500 requests
Completed 2000 requests
Completed 2500 requests
Completed 3000 requests
Completed 3500 requests
Completed 4000 requests
Completed 4500 requests
Completed 5000 requests
Finished 5000 requests


Server Software:        Apache/2.4.52
Server Hostname:        192.168.1.186
Server Port:            80

Document Path:          /large.html
Document Length:        52428800 bytes

Concurrency Level:      500
Time taken for tests:   194.299 seconds
Complete requests:      5000
Failed requests:        200
   (Connect: 0, Receive: 0, Length: 200, Exceptions: 0)
Keep-Alive requests:    4800
Total transferred:      251659757100 bytes
HTML transferred:       251658240000 bytes
Requests per second:    25.73 [#/sec] (mean)
Time per request:       19429.930 [ms] (mean)
Time per request:       38.860 [ms] (mean, across all concurrent requests)
Transfer rate:          1264860.34 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    3  10.3      0      49
Processing:   119 11378 36382.4   3702  194242
Waiting:        0 11413 44443.9     48  193042
Total:        119 11380 36390.3   3702  194279

Percentage of the requests served within a certain time (ms)
  50%   3702
  66%   4413
  75%   5002
  80%   5100
  90%   6399
  95%   9671
  98%  189495
  99%  192495
 100%  194279 (longest request)

## mpm_worker
root@apache:/etc/apache2# ab -n 5000 -c 500 -k http://192.168.1.186:80/large.html
This is ApacheBench, Version 2.3 <$Revision: 1879490 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking 192.168.1.186 (be patient)
Completed 500 requests
Completed 1000 requests
Completed 1500 requests
Completed 2000 requests
Completed 2500 requests
Completed 3000 requests
Completed 3500 requests
Completed 4000 requests
Completed 4500 requests
Completed 5000 requests
Finished 5000 requests


Server Software:        Apache/2.4.52
Server Hostname:        192.168.1.186
Server Port:            80

Document Path:          /large.html
Document Length:        52428800 bytes

Concurrency Level:      500
Time taken for tests:   190.979 seconds
Complete requests:      5000
Failed requests:        181
   (Connect: 0, Receive: 0, Length: 181, Exceptions: 0)
Keep-Alive requests:    4819
Total transferred:      252912348641 bytes
HTML transferred:       252910821714 bytes
Requests per second:    26.18 [#/sec] (mean)
Time per request:       19097.926 [ms] (mean)
Time per request:       38.196 [ms] (mean, across all concurrent requests)
Transfer rate:          1293254.10 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    2   7.5      0      35
Processing:  1414 11983 32955.5   5751  190937
Waiting:        0 7561 36585.8     25  190654
Total:       1414 11985 32960.3   5752  190959

Percentage of the requests served within a certain time (ms)
  50%   5752
  66%   6427
  75%   6888
  80%   7230
  90%   8195
  95%   9395
  98%  187592
  99%  189285
 100%  190959 (longest request)

## mpm_event
root@apache:/etc/apache2# ab -n 5000 -c 500 -k http://192.168.1.186:80/large.html
This is ApacheBench, Version 2.3 <$Revision: 1879490 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking 192.168.1.186 (be patient)
Completed 500 requests
Completed 1000 requests
Completed 1500 requests
Completed 2000 requests
Completed 2500 requests
Completed 3000 requests
Completed 3500 requests
Completed 4000 requests
Completed 4500 requests
Completed 5000 requests
Finished 5000 requests


Server Software:        Apache/2.4.52
Server Hostname:        192.168.1.186
Server Port:            80

Document Path:          /large.html
Document Length:        52428800 bytes

Concurrency Level:      500
Time taken for tests:   189.917 seconds
Complete requests:      5000
Failed requests:        376
   (Connect: 0, Receive: 0, Length: 376, Exceptions: 0)
Keep-Alive requests:    4624
Total transferred:      246276197022 bytes
HTML transferred:       246274692001 bytes
Requests per second:    26.33 [#/sec] (mean)
Time per request:       18991.711 [ms] (mean)
Time per request:       37.983 [ms] (mean, across all concurrent requests)
Transfer rate:          1266363.49 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    2   7.7      0      35
Processing:     0 12624 32420.5   5925  189855
Waiting:        0 10589 40251.7     30  186950
Total:          0 12626 32426.2   5925  189888

Percentage of the requests served within a certain time (ms)
  50%   5925
  66%   6604
  75%   7130
  80%   7540
  90%   8682
  95%  34427
  98%  185605
  99%  187926
 100%  189888 (longest request)

