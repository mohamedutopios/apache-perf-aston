mohamed@mohamed-VirtualBox:~/Documents/wrk$ wrk -t12 -c100 -d30s --latency https://http2.example.com/file1.txt
Running 30s test @ https://http2.example.com/file1.txt
  12 threads and 100 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency   358.10ms  431.74ms   2.00s    84.09%
    Req/Sec    29.72     16.75   105.00     63.49%
  Latency Distribution
     50%  163.87ms
     75%  407.90ms
     90%    1.08s 
     99%    1.80s 
  9541 requests in 30.10s, 9.33GB read
  Socket errors: connect 0, read 63, write 0, timeout 162
Requests/sec:    317.02
Transfer/sec:    317.45MB