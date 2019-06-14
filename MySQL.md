# MySQL一些筆記

1. 顯示binlog `show binary logs`
2. 顯示binlog特定行的事件 `show binlog events in '{BIN_LOG_FILE}' limit {START_INDEX},{LIMIT_SIZE}` ，BIN_LOG_FILE就是檔案名稱、START_INDEX就是事件的INDEX、LIMIT_SIZE就是要顯示多少事件
3. command模式下指令最後加上 `\G` 可以讓結果用值觀的console log印出
4. replication跳過錯誤 `set global sql_slave_skip_counter=N` ，N指的是跳過N個事件
5. replication跳過所有錯誤，在config中加上 `slave-skip-errors=all`，或是 `slave-skip-errors=1062,1053` 來跳過指定的錯誤，官方文件：
https://dev.mysql.com/doc/refman/8.0/en/replication-options-slave.html#option_mysqld_slave-skip-errors
