#Download di Azure SDK per Java

##Librerie client di Azure per Java - Download manuale

Le librerie di Azure per Java vengono distribuite con la [licenza Apache versione 2.0][license]. Fare clic [qui][zip-download] per ottenere un file ZIP contenente tutte le librerie e le relative dipendenze.  Questo software è reso disponibile da Microsoft Open Technologies, Inc. Per dettagli sulla licenza e altre informazioni, vedere i file license.txt e ThirdPartyNotices.txt all'interno dell'archivio ZIP.

##Librerie di Azure per Java - Maven

Se il progetto è già stato configurato per usare Maven per la compilazione, aggiungere la dipendenza seguente al file pom.xml.

	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-compute</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-network</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-sql</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-storage</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-websites</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-media</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-servicebus</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-serviceruntime</artifactId>
	    <version>n.n.n</version>
	</dependency>



All'interno dell'elemento`<version>` sostituire *n.n.n* con un numero di versione valido, che può essere ottenuto dal [repository delle librerie di Azure su Maven](http://go.microsoft.com/fwlink/?LinkID=286274).

[license]: http://www.apache.org/licenses/LICENSE-2.0.html
[zip-download]:  http://go.microsoft.com/fwlink/?LinkId=253887

<!--HONumber=42-->
