
## mpm_prefork
root@apache:/etc/apache2# ab -n 1000 -c 100 http://192.168.1.186:80/test.html
This is ApacheBench, Version 2.3 <$Revision: 1879490 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking 192.168.1.186 (be patient)
Completed 100 requests
Completed 200 requests
Completed 300 requests
Completed 400 requests
Completed 500 requests
Completed 600 requests
Completed 700 requests
Completed 800 requests
Completed 900 requests
Completed 1000 requests
Finished 1000 requests


Server Software:        Apache/2.4.52
Server Hostname:        192.168.1.186
Server Port:            80

Document Path:          /test.html
Document Length:        57 bytes

Concurrency Level:      100
Time taken for tests:   0.379 seconds
Complete requests:      1000
Failed requests:        0
Total transferred:      303000 bytes
HTML transferred:       57000 bytes
Requests per second:    2637.59 [#/sec] (mean)
Time per request:       37.913 [ms] (mean)
Time per request:       0.379 [ms] (mean, across all concurrent requests)
Transfer rate:          780.46 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    1   2.2      1      11
Processing:     2   34   7.6     35      49
Waiting:        0   34   7.7     34      48
Total:         11   36   6.3     36      49

Percentage of the requests served within a certain time (ms)
  50%     36
  66%     38
  75%     39
  80%     40
  90%     45
  95%     45
  98%     46
  99%     47
 100%     49 (longest request)



## mpm_worker
root@apache:/etc/apache2# ab -n 1000 -c 100 http://192.168.1.186:80/test.html
This is ApacheBench, Version 2.3 <$Revision: 1879490 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking 192.168.1.186 (be patient)
Completed 100 requests
Completed 200 requests
Completed 300 requests
Completed 400 requests
Completed 500 requests
Completed 600 requests
Completed 700 requests
Completed 800 requests
Completed 900 requests
Completed 1000 requests
Finished 1000 requests


Server Software:        Apache/2.4.52
Server Hostname:        192.168.1.186
Server Port:            80

Document Path:          /test.html
Document Length:        57 bytes

Concurrency Level:      100
Time taken for tests:   0.486 seconds
Complete requests:      1000
Failed requests:        0
Total transferred:      303000 bytes
HTML transferred:       57000 bytes
Requests per second:    2059.17 [#/sec] (mean)
Time per request:       48.563 [ms] (mean)
Time per request:       0.486 [ms] (mean, across all concurrent requests)
Transfer rate:          609.31 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    3   5.0      0      19
Processing:     9   43  12.1     45      71
Waiting:        1   41  12.6     44      70
Total:         11   45   9.9     46      71

Percentage of the requests served within a certain time (ms)
  50%     46
  66%     50
  75%     52
  80%     53
  90%     57
  95%     61
  98%     65
  99%     67
 100%     71 (longest request)

 ## mpm_event
root@apache:/etc/apache2# ab -n 1000 -c 100 http://192.168.1.186:80/test.html
This is ApacheBench, Version 2.3 <$Revision: 1879490 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking 192.168.1.186 (be patient)
Completed 100 requests
Completed 200 requests
Completed 300 requests
Completed 400 requests
Completed 500 requests
Completed 600 requests
Completed 700 requests
Completed 800 requests
Completed 900 requests
Completed 1000 requests
Finished 1000 requests


Server Software:        Apache/2.4.52
Server Hostname:        192.168.1.186
Server Port:            80

Document Path:          /test.html
Document Length:        57 bytes

Concurrency Level:      100
Time taken for tests:   0.494 seconds
Complete requests:      1000
Failed requests:        0
Total transferred:      303000 bytes
HTML transferred:       57000 bytes
Requests per second:    2024.00 [#/sec] (mean)
Time per request:       49.407 [ms] (mean)
Time per request:       0.494 [ms] (mean, across all concurrent requests)
Transfer rate:          598.90 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    1   3.3      0      14
Processing:     4   45  15.0     45     136
Waiting:        0   44  14.4     45     100
Total:          5   46  14.6     45     137

Percentage of the requests served within a certain time (ms)
  50%     45
  66%     48
  75%     50
  80%     51
  90%     59
  95%     79
  98%     92
  99%     96
 100%    137 (longest request)

