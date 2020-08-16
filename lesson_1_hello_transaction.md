

# 题目描述
本地下载TiDB,TiKV,PD源代码，改写源码并编译部署一下环境：
* TiDB
* PD
* TiKV
改写后：使得TiDB启动事务时，能打印出一个“hello transaction”的日志

# 环境
mac
go1.14
rustc 1.45

# git clone and build TiDB/PD/TiKV
从github上找到这三个项目，并git clone到本地。这个操作在公司上班时完成tidb/tikv的下载，当时怕使用家里网络从github上下载源码很慢。
周末在家做作业时发现还需要下载pd，于是开始漫长的git clone.
* git clone慢
经过多次尝试，都遇到git “fatal: the remote end hung up unexpectedly”错误。
在stackoverflow上找到办法：https://stackoverflow.com/questions/6842687/the-remote-end-hung-up-unexpectedly-while-git-cloning
```
git config --global http.postBuffer 500M
git config --global http.maxRequestBuffer 100M
git config --global core.compression 0
```
设置后，git clone速度飞起，解决pd git clone问题

* cargo build tikv慢
其实也是利用公司往装好了rust环境，但是在家build tikv时还是遇到了问题。
我一开始切换到 release-4.0版本，本以为是一个稳定版本，能直接执行`make`编译。
但是在编译到后面会报错。"error[E0554]: #![feature] may not be used on the stable release channel Couldn't install racer using cargo"
于是又重装rust nightly版本，下载龟速。
又是一番网上各种搜索解决办法，最后通过配置rust的国内镜像解决问题。
https://cloud.tencent.com/developer/article/1620144
经过多次尝试后，release-4.0还是没有编译成功，切换到master分支后，能够make通过。

* TiDB/PD build
都切换到release-4.0分支,使用make能顺利编译完成。

# run tidb/tikv/pd locally
通过查找pingcap文档，https://docs.pingcap.com/zh/tidb/stable/command-line-flags-for-tidb-configuration 了解了三个程序的命令行参数和配置文件。
按照题目要求：
* 启动1个pd实例:
```
./bin/pd-server -config conf/config.toml
```
(listen addr: http://127.0.0.1:2379)
* 启动3个tikv实例:
```
./bin/tikv-server --addr 127.0.0.1:20170 --pd 127.0.0.1:2379 --data-dir /tmp/tikv/store_0 --config etc/config.toml
./bin/tikv-server --addr 127.0.0.1:20180 --pd 127.0.0.1:2379 --data-dir /tmp/tikv/store_1 --config etc/config.toml
./bin/tikv-server --addr 127.0.0.1:20190 --pd 127.0.0.1:2379 --data-dir /tmp/tikv/store_2 --config etc/config.toml
```
* 启动一个tidb实例:
```
./bin/tidb-server --store=tikv --path="127.0.0.1:2379" --config config/config.toml
```
通过查看输出到stderr的log，显示pd/tikv/tidb连接正常，

# print "hello transaction" when tidb starts a transaction
通过阅读tidb源码，同时使用搜索，很容易在tidb源码中找到Transaction启动的代码。
Transaction interface定义在package tidb/kv中的kv.go文件中,定义了熟悉的Commit/Rollback等方法。(Begin方法定义在Storage interface中)
Implementation of Transaction在tidb/store/tikv package中的kv.go中。
在方法`func (s *tikvStore) Begin() (kv.Transaction, error)` 或 `func (s *tikvStore) BeginWithStartTS(startTS uint64) (kv.Transaction, error)`
中加入code: `logutil.BgLogger().Info("hello transaction")`
重新make并run，可以看到tidb的输出能持续打印出“hello transaction”的log
