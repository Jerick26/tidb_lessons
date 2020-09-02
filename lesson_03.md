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

## 抓取并分析pprof
执行tpc-c的过程中抓取pprof数据<br>
```
./bin/go-tpc tpcc -H 127.0.0.1 -P 4000 -D tpcc --warehouses 4 prepare
./bin/go-tpc tpcc -H 127.0.0.1 -P 4000 -D tpcc --warehouses 4 run
curl http://127.0.0.1:10080/debug/zip\?seconds\=60 --output debug-tpcc-run.zip
```
解压debug-tpcc-run.zip，得到profile文件，终端执行`go tool pprof -http=:8080 profile`，在浏览器中可观察到cpu的profile，如下图所示:<br>
![image](https://user-images.githubusercontent.com/23067882/91974813-f3b34480-ed50-11ea-864c-f0af15a2fd8a.png)
<br>
![image](https://user-images.githubusercontent.com/23067882/91975028-53a9eb00-ed51-11ea-8914-518be6ddf762.png)

从火焰图中可以看出程序的大部分时间都在执行golang runtime的mcall和mstart。<br>
查阅golang的关于mcall的资料，`mcall switches from the g to the g0 stack and invokes fn(g), where g is the goroutine that made the call. `, `mstart is the entry-point for new Ms.`。可能与go routine频繁切换有关。对于关键服务 dispatch, autoAnalizeWorker, httpClient Reader/Write 占比不超过10%，优化的空间很少。

后来又过了一遍profile的graph图，发现runtime.futex执行时间高，可能更网上说的Ticker定时器trigger周期很小有关，这个还得下来仔细阅读代码来分析。
![image](https://user-images.githubusercontent.com/23067882/91977276-ffa10580-ed54-11ea-984f-7683d2d84203.png)
