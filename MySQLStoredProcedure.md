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

---
### 含有傳入值的範例
	delimiter #
	CREATE PROCEDURE insertPlayer (CustomName varchar(10))
	BEGIN

		DECLARE i int;
		DECLARE pID VARCHAR(32);
		set i = 0;

		while i<1000 do
			SET pID = CONCAT(CustomName, '-', i);
			INSERT INTO `database`.`table` VALUES (pID, 'someString');
			set i = i + 1;
		end while;

	END
	#

1. `CREATE PROCEDURE insertPlayer (CustomName varchar(10))`  
	這邊的CustomName即為傳入值，型別為varchar(10)，若要增加傳入值用逗號隔開新增

2. `BEGIN`與`END`即為procedure的起始與結尾
3. `DECLARE`為定義變數
4. `CONCAT(..,..,...)`為組合變數回傳字串
