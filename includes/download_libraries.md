# Download di Azure SDK per Java

## Librerie client di Azure per Java - Download manuale

Le librerie di Azure per Java vengono distribuite con la [licenza Apache versione 2.0][licenza Apache versione 2.0]. Fare clic [qui][qui] per ottenere un file ZIP contenente tutte le librerie e le relative dipendenze. Questo software è reso disponibile da Microsoft Open Technologies, Inc. Per dettagli sulla licenza e altre informazioni, vedere i file license.txt e ThirdPartyNotices.txt all'interno dell'archivio ZIP.

## Librerie di Azure per Java - Maven

Se il progetto è già stato configurato per usare Maven per la compilazione, aggiungere la dipendenza seguente al file pom.xml.

    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-management</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-management-compute</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-management-network</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-management-sql</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-management-storage</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-management-websites</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-media</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-servicebus</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-serviceruntime</artifactId>
        <version>n.n.n</version>
    </dependency>

All'interno dell'elemento `<version>` sostituire *n.n.n* con un numero di versione valido, che può essere ottenuto dal [repository delle librerie di Azure su Maven][repository delle librerie di Azure su Maven].

  [licenza Apache versione 2.0]: http://www.apache.org/licenses/LICENSE-2.0.html
  [qui]: http://go.microsoft.com/fwlink/?LinkId=253887
  [repository delle librerie di Azure su Maven]: http://go.microsoft.com/fwlink/?LinkID=286274
