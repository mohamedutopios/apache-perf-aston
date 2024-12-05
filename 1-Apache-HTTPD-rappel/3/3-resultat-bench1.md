
## mpm_prefork
root@apache:/etc/apache2# ab -n 10000 -c 1000 http://192.168.1.186:80/test.html
This is ApacheBench, Version 2.3 <$Revision: 1879490 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking 192.168.1.186 (be patient)
Completed 1000 requests
Completed 2000 requests
Completed 3000 requests
Completed 4000 requests
Completed 5000 requests
Completed 6000 requests
Completed 7000 requests
Completed 8000 requests
Completed 9000 requests
Completed 10000 requests
Finished 10000 requests


Server Software:        Apache/2.4.52
Server Hostname:        192.168.1.186
Server Port:            80

Document Path:          /test.html
Document Length:        57 bytes

Concurrency Level:      1000
Time taken for tests:   4.162 seconds
Complete requests:      10000
Failed requests:        0
Total transferred:      3030000 bytes
HTML transferred:       570000 bytes
Requests per second:    2402.60 [#/sec] (mean)
Time per request:       416.217 [ms] (mean)
Time per request:       0.416 [ms] (mean, across all concurrent requests)
Transfer rate:          710.92 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    8  55.6      0    1036
Processing:    12  341 823.2    139    4104
Waiting:        0  341 823.2    139    4104
Total:         57  349 834.9    140    4160

Percentage of the requests served within a certain time (ms)
  50%    140
  66%    157
  75%    182
  80%    197
  90%    471
  95%    513
  98%   4106
  99%   4140
 100%   4160 (longest request)



## mpm_worker
root@apache:/etc/apache2# ab -n 10000 -c 1000 http://192.168.1.186:80/test.html
This is ApacheBench, Version 2.3 <$Revision: 1879490 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking 192.168.1.186 (be patient)
Completed 1000 requests
Completed 2000 requests
Completed 3000 requests
Completed 4000 requests
Completed 5000 requests
Completed 6000 requests
Completed 7000 requests
Completed 8000 requests
Completed 9000 requests
Completed 10000 requests
Finished 10000 requests


Server Software:        Apache/2.4.52
Server Hostname:        192.168.1.186
Server Port:            80

Document Path:          /test.html
Document Length:        57 bytes

Concurrency Level:      1000
Time taken for tests:   5.611 seconds
Complete requests:      10000
Failed requests:        0
Total transferred:      3030000 bytes
HTML transferred:       570000 bytes
Requests per second:    1782.11 [#/sec] (mean)
Time per request:       561.134 [ms] (mean)
Time per request:       0.561 [ms] (mean, across all concurrent requests)
Transfer rate:          527.32 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0   21 124.8      0    1033
Processing:    12  448 1058.0    218    5552
Waiting:        1  446 1058.1    216    5552
Total:         54  469 1072.1    219    5607

Percentage of the requests served within a certain time (ms)
  50%    219
  66%    240
  75%    251
  80%    268
  90%    363
  95%   1268
  98%   5522
  99%   5563
 100%   5607 (longest request)

 ## mpm_event
root@apache:/etc/apache2# ab -n 10000 -c 1000 http://192.168.1.186:80/test.html
This is ApacheBench, Version 2.3 <$Revision: 1879490 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking 192.168.1.186 (be patient)
Completed 1000 requests
Completed 2000 requests
Completed 3000 requests
Completed 4000 requests
Completed 5000 requests
Completed 6000 requests
Completed 7000 requests
Completed 8000 requests
Completed 9000 requests
Completed 10000 requests
Finished 10000 requests


Server Software:        Apache/2.4.52
Server Hostname:        192.168.1.186
Server Port:            80

Document Path:          /test.html
Document Length:        57 bytes

Concurrency Level:      1000
Time taken for tests:   3.474 seconds
Complete requests:      10000
Failed requests:        0
Total transferred:      3030000 bytes
HTML transferred:       570000 bytes
Requests per second:    2878.23 [#/sec] (mean)
Time per request:       347.436 [ms] (mean)
Time per request:       0.347 [ms] (mean, across all concurrent requests)
Transfer rate:          851.66 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0   18 117.5      0    1036
Processing:    12  276 617.1    153    3390
Waiting:        6  275 617.2    153    3390
Total:         52  294 637.4    154    3454

Percentage of the requests served within a certain time (ms)
  50%    154
  66%    161
  75%    166
  80%    170
  90%    198
  95%   1172
  98%   3393
  99%   3424
 100%   3454 (longest request)

