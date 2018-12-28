# MySQL 儲存執行
使用 mysql command line 操作

1. 將分隔符暫時改為'**#**'
`delimiter #`

2. 建立一個連續insert的procedure

		CREATE PROCEDURE testProcedure()
		BEGIN

		declare i int;
		set i = 0;

		while i<1000 do
			INSERT INTO `some_database`.`some_table` (`s_column`) VALUES ('testing...');
		set i = i + 1;
		end while;

		END
		#

3. 將分隔符切換回'**;**'
`delimiter ;`
在heidiSQL使用時不用做這行，原因還不知道，註解之後也不會影響下一個query的結尾符(還是;)
