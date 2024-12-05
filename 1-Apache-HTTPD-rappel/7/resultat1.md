mohamed@mohamed-VirtualBox:~/Documents/wrk$ wrk -t12 -c100 -d30s --latency https://http1.example.com/file1.txt
Running 30s test @ https://http1.example.com/file1.txt
  12 threads and 100 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency   339.83ms  442.72ms   2.00s    84.66%
    Req/Sec    29.39     16.63   108.00     63.20%
  Latency Distribution
     50%  139.65ms
     75%  343.65ms
     90%    1.08s 
     99%    1.84s 
  7778 requests in 30.10s, 7.61GB read
  Socket errors: connect 0, read 59, write 0, timeout 250
Requests/sec:    258.39
Transfer/sec:    259.01MB