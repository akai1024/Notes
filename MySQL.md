# MySQL一些筆記

1. 顯示binlog `show binary logs`
2. 顯示binlog特定行的事件 `show binlog events in '{BIN_LOG_FILE}' limit {START_INDEX},{LIMIT_SIZE}` ，BIN_LOG_FILE就是檔案名稱、START_INDEX就是事件的INDEX、LIMIT_SIZE就是要顯示多少事件
3. command模式下指令最後加上 `\G` 可以讓結果用值觀的console log印出
4. replication跳過錯誤 `set global sql_slave_skip_counter=N` ，N指的是跳過N個事件
