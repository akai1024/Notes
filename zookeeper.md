# Zookeeper的日誌清理
有以下幾種方法
1. zookeeper本身有自己寫好了腳本，在bin/zkCleanup.sh中(我目前裝的沒有所以沒實測過這個方法)
2. 在3.4.0之後的版本，zookeeper提供了自動清理snapshot和事務日誌的功能，通過在config配置 `autopurge.purgeInterval` 和 `autopurge.snapRetainCount`實現

        # 指定清理頻率，單位是小時，默認0，表示不開啟功能。
        autopurge.purgeInterval=6

        # 和上面的參數搭配使用，指定需要保留的文件數目，默認是3。
        autopurge.snapRetainCount=5
3. 自己寫一個清除腳本，每天定時執行

        #!/bin/bash
        #snapshot file dir
        dataDir=/home/yinshi.nc/test/zk_data/version-2
        #tran log dir
        dataLogDir=/home/yinshi.nc/test/zk_log/version-2
        #zk log dir
        logDir=/home/yinshi.nc/test/logs
        #Leave 66 files
        count=66
        count=$[$count+1]
        ls -t $dataLogDir/log.* | tail -n +$count | xargs rm -f
        ls -t $dataDir/snapshot.* | tail -n +$count | xargs rm -f
        ls -t $logDir/zookeeper.log.* | tail -n +$count | xargs rm -f

---
# 如何查詢zookeeper版號
使用telnet連線到zookeeper的位置(範例是192.168.56.21 2181)

    root@vm02zks:~# telnet 192.168.56.21 2181
    Trying 192.168.56.21...
    Connected to 192.168.56.21.
    Escape character is '^]'.

這時command會停在一個輸入點，接著輸入`stats`

    stats
    Zookeeper version: 3.4.13-2d71af4dbe22557fda74f9a9b4309b15a7487f03, built on 06/29/2018 00:39 GMT
    Clients:
    ...(這裡會顯示出與zookeeper連接的所有service)

    Latency min/avg/max: 0/0/51
    Received: 3943
    Sent: 3945
    Connections: 6
    Outstanding: 0
    Zxid: 0x7bc00000209
    Mode: follower
    Node count: 421
    Connection closed by foreign host.

這時就可以在 `Zookeeper version: 3.4.13-2d71af4dbe22557fda74f9a9b4309b15a7487f03, built on 06/29/2018 00:39 GMT` 這行查看版號
