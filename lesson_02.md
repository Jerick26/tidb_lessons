## 环境配置
**机器配置：**<br>
8Vcore, Intel(R) Core(TM) i7-6700 CPU @ 3.40GHz
16G Mem, 2133 MHz
HDD

**拓扑结构:**<br>
都部署在一个机器上
1 pd, 3 tikv, 1 tidb

**参数配置:**<br>
默认

## sysbench测试报告
导入10W数据，4张表

Point select output:

```yaml
SQL statistics:
    queries performed:
        read:                            4953915
        write:                           0
        other:                           0
        total:                           4953915
    transactions:                        4953915 (27521.31 per sec.)
    queries:                             4953915 (27521.31 per sec.)
    ignored errors:                      0      (0.00 per sec.)
    reconnects:                          0      (0.00 per sec.)

General statistics:
    total time:                          180.0018s
    total number of events:              4953915

Latency (ms):
         min:                                    0.11
         avg:                                    0.29
         max:                                 1004.32
         95th percentile:                        0.42
         sum:                              1437284.27

Threads fairness:
    events (avg/stddev):           619239.3750/224.85
    execution time (avg/stddev):   179.6605/0.00
```


Update index output:
```yaml
 SQL statistics:
    queries performed:
        read:                            0
        write:                           1215
        other:                           0
        total:                           1215
    transactions:                        1215   (6.72 per sec.)
    queries:                             1215   (6.72 per sec.)
    ignored errors:                      0      (0.00 per sec.)
    reconnects:                          0      (0.00 per sec.)

General statistics:
    total time:                          180.9024s
    total number of events:              1215

Latency (ms):
         min:                                  393.65
         avg:                                 1188.35
         max:                                 3374.54
         95th percentile:                     1618.78
         sum:                              1443846.35

Threads fairness:
    events (avg/stddev):           151.8750/1.83
    execution time (avg/stddev):   180.4808/0.29
```

Read-only output:

```yaml
SQL statistics:
    queries performed:
        read:                            1904364
        write:                           0
        other:                           272052
        total:                           2176416
    transactions:                        136026 (755.65 per sec.)
    queries:                             2176416 (12090.38 per sec.)
    ignored errors:                      0      (0.00 per sec.)
    reconnects:                          0      (0.00 per sec.)

General statistics:
    total time:                          180.0107s
    total number of events:              136026

Latency (ms):
         min:                                    5.36
         avg:                                   10.58
         max:                                   50.88
         95th percentile:                       14.73
         sum:                              1439612.18

Threads fairness:
    events (avg/stddev):           17003.2500/18.46
    execution time (avg/stddev):   179.9515/0.00
```

![image](https://user-images.githubusercontent.com/23067882/91176245-9269f080-e714-11ea-8824-1b835aa521ae.png)


## go-ycsb测试报告
执行命令: <br>
`./bin/go-ycsb run mysql -P workloads/workloada -p operationcount=1000 -p mysql.host=127.0.0.1 -p mysql.port=4000 --threads 8`<br>
output:

```yaml
***************** properties *****************
"threadcount"="8"
"readallfields"="true"
"operationcount"="1000"
"recordcount"="1000"
"readproportion"="0.5"
"requestdistribution"="uniform"
"workload"="core"
"mysql.port"="4000"
"scanproportion"="0"
"mysql.host"="127.0.0.1"
"dotransactions"="true"
"updateproportion"="0.5"
"insertproportion"="0"
**********************************************
READ   - Takes(s): 10.0, Count: 125, OPS: 12.5, Avg(us): 2282, Min(us): 544, Max(us): 12510, 99th(us): 5000, 99.9th(us): 13000, 99.99th(us): 13000
UPDATE - Takes(s): 9.4, Count: 110, OPS: 11.8, Avg(us): 693974, Min(us): 464331, Max(us): 1023933, 99th(us): 1020000, 99.9th(us): 1024000, 99.99th(us): 1024000
READ   - Takes(s): 20.0, Count: 267, OPS: 13.4, Avg(us): 2654, Min(us): 544, Max(us): 14835, 99th(us): 13000, 99.9th(us): 15000, 99.99th(us): 15000
UPDATE - Takes(s): 19.4, Count: 240, OPS: 12.4, Avg(us): 654774, Min(us): 464331, Max(us): 1023933, 99th(us): 1018000, 99.9th(us): 1024000, 99.99th(us): 1024000
READ   - Takes(s): 30.0, Count: 410, OPS: 13.7, Avg(us): 3588, Min(us): 544, Max(us): 271100, 99th(us): 13000, 99.9th(us): 272000, 99.99th(us): 272000
UPDATE - Takes(s): 29.4, Count: 374, OPS: 12.7, Avg(us): 627662, Min(us): 421447, Max(us): 1023933, 99th(us): 1004000, 99.9th(us): 1024000, 99.99th(us): 1024000
READ   - Takes(s): 40.0, Count: 503, OPS: 12.6, Avg(us): 3361, Min(us): 455, Max(us): 271100, 99th(us): 11000, 99.9th(us): 272000, 99.99th(us): 272000
UPDATE - Takes(s): 39.4, Count: 470, OPS: 11.9, Avg(us): 647562, Min(us): 421447, Max(us): 1283542, 99th(us): 1266000, 99.9th(us): 1284000, 99.99th(us): 1284000
Run finished, takes 44.39704297s
READ   - Takes(s): 44.4, Count: 517, OPS: 11.6, Avg(us): 3331, Min(us): 455, Max(us): 271100, 99th(us): 11000, 99.9th(us): 272000, 99.99th(us): 272000
UPDATE - Takes(s): 43.8, Count: 483, OPS: 11.0, Avg(us): 646926, Min(us): 421447, Max(us): 1283542, 99th(us): 1266000, 99.9th(us): 1284000, 99.99th(us): 1284000
```

![image](https://user-images.githubusercontent.com/23067882/91177760-c8a86f80-e716-11ea-97d4-7f483ffb1130.png)

## go-tpc测试报告
执行命令<br>
```
./bin/go-tpc tpcc -H 127.0.0.1 -P 4000 -D tpcc --warehouses 4 prepare
./bin/go-tpc tpcc -H 127.0.0.1 -P 4000 -D tpcc --warehouses 4 run
```

output:

```
[Current] NEW_ORDER - Takes(s): 7.9, Count: 3, TPM: 22.7, Sum(ms): 5831, Avg(ms): 1943, 90th(ms): 4000, 99th(ms): 4000, 99.9th(ms): 4000
[Current] ORDER_STATUS - Takes(s): 1.0, Count: 1, TPM: 58.2, Sum(ms): 401, Avg(ms): 401, 90th(ms): 512, 99th(ms): 512, 99.9th(ms): 512
[Current] PAYMENT - Takes(s): 5.3, Count: 1, TPM: 11.4, Sum(ms): 2674, Avg(ms): 2674, 90th(ms): 4000, 99th(ms): 4000, 99.9th(ms): 4000
[Current] DELIVERY - Takes(s): 5.9, Count: 1, TPM: 10.2, Sum(ms): 5174, Avg(ms): 5174, 90th(ms): 8000, 99th(ms): 8000, 99.9th(ms): 8000
[Current] NEW_ORDER - Takes(s): 0.6, Count: 1, TPM: 108.0, Sum(ms): 2285, Avg(ms): 2285, 90th(ms): 4000, 99th(ms): 4000, 99.9th(ms): 4000
[Current] ORDER_STATUS - Takes(s): 2.8, Count: 1, TPM: 21.1, Sum(ms): 300, Avg(ms): 300, 90th(ms): 512, 99th(ms): 512, 99.9th(ms): 512
[Current] PAYMENT - Takes(s): 3.1, Count: 1, TPM: 19.1, Sum(ms): 2714, Avg(ms): 2714, 90th(ms): 4000, 99th(ms): 4000, 99.9th(ms): 4000
[Current] NEW_ORDER - Takes(s): 5.8, Count: 1, TPM: 10.3, Sum(ms): 2333, Avg(ms): 2333, 90th(ms): 4000, 99th(ms): 4000, 99.9th(ms): 4000
[Current] PAYMENT - Takes(s): 8.2, Count: 4, TPM: 29.4, Sum(ms): 7455, Avg(ms): 1863, 90th(ms): 4000, 99th(ms): 4000, 99.9th(ms): 4000
[Current] NEW_ORDER - Takes(s): 8.9, Count: 2, TPM: 13.5, Sum(ms): 4399, Avg(ms): 2199, 90th(ms): 4000, 99th(ms): 4000, 99.9th(ms): 4000
[Current] PAYMENT - Takes(s): 5.9, Count: 2, TPM: 20.3, Sum(ms): 4968, Avg(ms): 2484, 90th(ms): 4000, 99th(ms): 4000, 99.9th(ms): 4000
[Current] NEW_ORDER - Takes(s): 7.6, Count: 1, TPM: 7.9, Sum(ms): 3840, Avg(ms): 3840, 90th(ms): 4000, 99th(ms): 4000, 99.9th(ms): 4000
[Current] PAYMENT - Takes(s): 5.3, Count: 1, TPM: 11.3, Sum(ms): 2260, Avg(ms): 2260, 90th(ms): 4000, 99th(ms): 4000, 99.9th(ms): 4000
[Current] DELIVERY - Takes(s): 6.7, Count: 1, TPM: 8.9, Sum(ms): 8548, Avg(ms): 8548, 90th(ms): 16000, 99th(ms): 16000, 99.9th(ms): 16000
[Current] NEW_ORDER - Takes(s): 3.1, Count: 1, TPM: 19.6, Sum(ms): 3691, Avg(ms): 3691, 90th(ms): 4000, 99th(ms): 4000, 99.9th(ms): 4000
[Current] NEW_ORDER - Takes(s): 9.9, Count: 1, TPM: 6.1, Sum(ms): 3197, Avg(ms): 3197, 90th(ms): 4000, 99th(ms): 4000, 99.9th(ms): 4000
[Current] ORDER_STATUS - Takes(s): 4.0, Count: 1, TPM: 15.2, Sum(ms): 542, Avg(ms): 542, 90th(ms): 1000, 99th(ms): 1000, 99.9th(ms): 1000
[Current] PAYMENT - Takes(s): 7.2, Count: 3, TPM: 25.0, Sum(ms): 7338, Avg(ms): 2446, 90th(ms): 4000, 99th(ms): 4000, 99.9th(ms): 4000
[Current] PAYMENT - Takes(s): 9.7, Count: 4, TPM: 24.7, Sum(ms): 9092, Avg(ms): 2273, 90th(ms): 4000, 99th(ms): 4000, 99.9th(ms): 4000
[Current] NEW_ORDER - Takes(s): 9.0, Count: 3, TPM: 20.0, Sum(ms): 9728, Avg(ms): 3242, 90th(ms): 4000, 99th(ms): 4000, 99.9th(ms): 4000
[Current] ORDER_STATUS - Takes(s): 0.3, Count: 1, TPM: 180.9, Sum(ms): 332, Avg(ms): 332, 90th(ms): 512, 99th(ms): 512, 99.9th(ms): 512
[Current] PAYMENT - Takes(s): 6.5, Count: 1, TPM: 9.2, Sum(ms): 2484, Avg(ms): 2484, 90th(ms): 4000, 99th(ms): 4000, 99.9th(ms): 4000
[Current] NEW_ORDER - Takes(s): 2.9, Count: 1, TPM: 20.7, Sum(ms): 3326, Avg(ms): 3326, 90th(ms): 4000, 99th(ms): 4000, 99.9th(ms): 4000
[Current] PAYMENT - Takes(s): 8.1, Count: 3, TPM: 22.4, Sum(ms): 6233, Avg(ms): 2077, 90th(ms): 4000, 99th(ms): 4000, 99.9th(ms): 4000
[Current] NEW_ORDER - Takes(s): 3.2, Count: 1, TPM: 18.9, Sum(ms): 3231, Avg(ms): 3231, 90th(ms): 4000, 99th(ms): 4000, 99.9th(ms): 4000
[Current] PAYMENT - Takes(s): 8.4, Count: 3, TPM: 21.3, Sum(ms): 6752, Avg(ms): 2250, 90th(ms): 4000, 99th(ms): 4000, 99.9th(ms): 4000
```

