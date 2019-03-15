# Maven
maven相關的知識

更改JDKcompiler版本，在pom.xml的properties區段內添加:<br>
		<maven.compiler.source>1.8</maven.compiler.source><br>
		<maven.compiler.target>1.8</maven.compiler.target>

 
## Scope of Dependency
compile （編譯範圍） 
compile 是默認的範圍；如果沒有提供一個範圍，那該依賴的範圍就是編譯範圍。編譯範圍依賴在所有的classpath中可用，同時它們也會被打包。 
 
provided （已提供範圍） 
provided 依賴只有在當JDK或者一個容器已提供該依賴之後才使用。 
例如，如果你開發了一個web應用，你可能在編譯classpath中需要可用的Servlet API來編譯一個servlet， 
但是你不會想要在打包好的WAR中包含這個Servlet API；這個Servlet API JAR由你的應用服務器或者servlet容器提供。 
已提供範圍的依賴在編譯classpath（不是運行時）可用。它們不是傳遞性的，也不會被打包。 
 
runtime （運行時範圍） 
runtime 依賴在運行和測試系統的時候需要，但在編譯的時候不需要。 
比如，你可能在編譯的時候只需要JDBC API JAR，而只有在運行的時候才需要JDBC驅動實現。 
 
test （測試範圍） 
test 範圍依賴在一般的編譯和運行時都不需要，它們只有在測試編譯和測試運行階段可用。 
 
system （系統範圍） 
system 範圍依賴與provided類似，但是你必須顯式的提供一個對於本地系統中JAR 文件的路徑。 
這麼做是為了允許基於本地對象編譯，而這些對像是系統類庫的一部分。 
這樣的構件應該是一直可用的，Maven 也不會在倉庫中去尋找它。如果你將一個依賴範圍設置成系統範圍，你必須同時提供一個systemPath元素。 
注意該範圍是不推薦使用的（你應該一直盡量去從公共或定制的Maven倉庫中引用依賴）。 
