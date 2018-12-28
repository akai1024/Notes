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


---
### 備註

*註1：應為一個字串，通常是my-bin, binlog之類的名稱，可以從master的command line查看*
1. 登入master mysql

        sudo mysql

2. 查看master狀態

        show master status;

*註2：應為一個整數，一樣可由註1的方式查看*
*註3：應為一個字串，自行在slave上定義*