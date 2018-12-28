# MySQL單源與多源同步

1. mysql8的user創建時如果不使用ssl直接identified with 'mysql_native_password'
2. mysql8與5.7之前的版本在指令上有所差異，5.7使用 `slave start` , `slave stop` ，8使用 `start slave` , `stop slave`
3. 本文主要使用MySQL8(或稱mysql80)紀錄

---

### 單源
##### Master>
1. 建立提供slave同步的user

        create user 'rpl'@'%' identified with 'mysql_native_password' by 'rpl123';

2. 並開啟replication slave權限

        grant replication slave on *.* to 'rpl'@'%';

3. 刷新權限

        flush privileges;


##### Slave>
1. 停止slave(保險起見的步驟)

        stop slave;

2. 設定master

        change master to
        master_host='127.0.0.1',
        master_user='rpl',
        master_password='rpl123',
        master_log_file='MASTER的log file名稱(註1)',
        master_log_pos=MASTER的log file當前位置(註2);

3. 啟動slave

        start slave;

---

### 多源
##### Master(在所有要同步的master都要操作)>
1. 建立提供slave同步的user

        create user 'rpl'@'%' identified with 'mysql_native_password' by 'rpl123';

2. 並開啟replication slave權限

        grant replication slave on *.* to 'rpl'@'%';

3. 刷新權限

        flush privileges;


##### Slave>
1. 先停止slave

        stop slave;

2. 設定master

        change master to
        master_host='127.0.0.1',
        master_user='rpl',
        master_password='rpl123',
        master_log_file='MASTER的log file名稱(註1)',
        master_log_pos=MASTER的log file當前位置(註2)
        for channel '頻道名稱(註3)';

3. 多源同步時重複步驟2，將channel指定為不同名稱，建議寫在一個file上紀錄channel與host對應關係

4. 啟動slave同步所有數據源(master)

        start slave;

5. 啟動單個channel

        start slave for channel '頻道名稱';

6. 查看同步狀態

        show slave status;
        或是
        show slave status for channel '頻道名稱';

7. 清空所有配置

        reset slave all;

---
### 備註

*註1：應為一個字串，通常是my-bin, binlog之類的名稱，可以從master的command line查看*
1. 登入master mysql

        sudo mysql

2. 查看master狀態

        show master status;

*註2：應為一個整數，一樣可由註1的方式查看*

*註3：應為一個字串，自行在slave上定義*

---
### mysqld.cnf
一般來說mysql8會自動啟用binlog，若沒有啟動時可利用這些配置啟動

    server-id=123456
    log_bin=mysql-bin
    binlog_format=row
    binlog_row_image=full
    expire_logs_days=10

當然，對於每一個mysql instance最好指定不同的[server-id][1]

---
### GTID與master_auto_position
GTID是global transaction ID的縮寫，mysql用來標記每一個事件的唯一碼
可以在mysqld.cnf中透過配置啟動(主從庫都要配置)

    gtid_mode=ON(必選)
    enforce-gtid-consistency(必選)
    log-slave-updates=ON(可選，高可用切換)

啟用之後在replication的指令中就可以用master_auto_position=1來自動follow主庫
省去了指定file與position的困擾
例:

    change master to
    master_host='127.0.0.1',
    master_user='rpl',
    master_password='rpl123',
    master_auto_position=1
    for channel '頻道名稱(註3)';


[1]: https://dev.mysql.com/doc/refman/8.0/en/replication-options.html#option_mysqld_server-id 'mysql document'
