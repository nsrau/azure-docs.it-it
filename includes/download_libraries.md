
# Download di Azure SDK per Java

## Librerie client di Azure per Java - Download manuale

Le librerie di Azure per Java vengono distribuite con la [licenza Apache versione 2.0][1]. Fare clic [qui][2] per ottenere un file ZIP contenente tutte le librerie e le relative dipendenze. Questo software è reso disponibile da Microsoft Open Technologies, Inc. Per dettagli sulla licenza e altre informazioni, vedere i file license.txt e ThirdPartyNotices.txt all'interno dell'archivio ZIP.

## Librerie di Azure per Java - Maven

Se il progetto è già stato configurato per utilizzare Maven per la compilazione, aggiungere la dipendenza seguente al file pom.xml.

    <dependency>
    	<groupId>com.microsoft.windowsazure</groupId>
    	<a rtifactId>microsoft-windowsazure-api</artifactId>
    	<version>n.n.n</version>
    </dependency>

All'interno dell'elemento `<version>` sostituire
*n.n.n* con un numero di versione valido, che può essere ottenuto
dall'[archivio delle librerie di Azure su Maven][3].



[1]: http://www.apache.org/licenses/LICENSE-2.0.html
[2]: http://go.microsoft.com/fwlink/?LinkId=253887
[3]: http://go.microsoft.com/fwlink/?LinkID=286274