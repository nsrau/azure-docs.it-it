---
title: Configurare la connettività SSL per la connessione sicura a Database di Azure per MariaDB
description: Istruzioni per la configurazione di Database di Azure per MariaDB e delle applicazioni associate per usare correttamente le connessioni SSL
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: 197281a4666179037cd689e7e8d488e73039174b
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54810296"
---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mariadb"></a>Configurare la connettività SSL nell'applicazione per la connessione sicura a Database di Azure per MariaDB
Database di Azure per MariaDB supporta la connessione alle applicazioni client tramite Secure Sockets Layer (SSL). L'applicazione delle connessioni SSL tra il server di database e le applicazioni client aiuta a proteggersi dagli attacchi "man in the middle" crittografando il flusso di dati tra il server e l'applicazione.

## <a name="obtain-ssl-certificate"></a>Ottenere un certificato SSL
Scaricare il certificato necessario per comunicare tramite SSL con il server di Database di Azure per MariaDB da [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) e salvare il file del certificato nell'unità locale (questa esercitazione per esempio usa il percorso c:\ssl).
**Per Microsoft Internet Explorer e Microsoft Edge:** dopo aver completato il download, rinominare il certificato in BaltimoreCyberTrustRoot.crt.pem.

## <a name="bind-ssl"></a>Associare SSL
### <a name="connecting-to-server-using-the-mysql-workbench-over-ssl"></a>Connessione al server con MySQL Workbench tramite SSL
Configurare MySQL Workbench per connettersi in modo sicuro tramite SSL. Passare alla scheda **SSL** dalla finestra di dialogo Setup New Connection (Configura nuova connessione). Immettere il percorso del file **BaltimoreCyberTrustRoot.crt.pem** nel campo **SSL CA File:** (File CA SSL:). 
![Salvare un riquadro personalizzato](./media/howto-configure-ssl/mysql-workbench-ssl.png) Per le connessioni esistenti, è possibile associare SSL facendo clic con il pulsante destro del mouse sull'icona di connessione e scegliendo Modifica. In seguito passare alla scheda **SSL** e associare il file del certificato.

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>Connessione al server con l'interfaccia della riga di comando di MySQL tramite SSL
Un altro modo per associare il certificato SSL è quello di usare l'interfaccia della riga di comando di MySQL per eseguire il comando seguente:
```bash
mysql.exe -h mydemoserver.mariadb.database.azure.com -u Username@mydemoserver -p --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```
> [!NOTE]
> Quando si usano versioni più recenti dell'interfaccia della riga di comando di MySQL in Windows, è possibile che venga visualizzato un errore `SSL connection error: Certificate signature check failed`. In questo caso, sostituire il parametro `--ssl-ca={filepath}` con `--ssl`.

## <a name="enforcing-ssl-connections-in-azure"></a>Applicazione delle connessioni SSL in Azure 
### <a name="using-the-azure-portal"></a>Uso del portale di Azure
Tramite il portale di Azure, passare all'istanza di Database di Azure per il server MariaDB e fare clic su **Sicurezza delle connessioni**. Usare l'interruttore per abilitare o disabilitare l'impostazione **Imponi connessione SSL**, quindi fare clic su **Salva**. È consigliabile abilitare sempre l'impostazione **Imponi connessione SSL** per ottenere un livello di sicurezza avanzato.
![enable-ssl](./media/howto-configure-ssl/enable-ssl.png)

### <a name="using-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure
È possibile abilitare o disabilitare il parametro **ssl-enforcement** usando rispettivamente i valori Enabled o Disabled nell'interfaccia della riga di comando di Azure.
```azurecli-interactive
az mariadb server update --resource-group myresource --name mydemoserver --ssl-enforcement Enabled
```

## <a name="verify-the-ssl-connection"></a>Verificare la connessione SSL
Eseguire il comando **stato** mysql per verificare di essere connessi al server MariaDB tramite SSL:
```sql
status
```
Verificare che la connessione sia crittografata esaminando l'output, che dovrebbe indicare:  **SSL: Cipher in use is AES256-SHA** (Crittografia usata AES256-SHA) 

## <a name="sample-code"></a>Codice di esempio
Per stabilire una connessione sicura a Database di Azure per MariaDB tramite SSL dall'applicazione, fare riferimento agli esempi di codice seguenti:

### <a name="php"></a>PHP
```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL) ; 
mysqli_real_connect($conn, 'mydemoserver.mariadb.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector Python)
```python
try:
    conn=mysql.connector.connect(user='myadmin@mydemoserver', 
        password='yourpassword', 
        database='quickstartdb', 
        host='mydemoserver.mariadb.database.azure.com', 
        ssl_ca='/var/www/html/BaltimoreCyberTrustRoot.crt.pem')
except mysql.connector.Error as err:
    print(err)
```
### <a name="python-pymysql"></a>Python (PyMySQL)
```python
conn = pymysql.connect(user = 'myadmin@mydemoserver', 
        password = 'yourpassword', 
        database = 'quickstartdb', 
        host = 'mydemoserver.mariadb.database.azure.com', 
        ssl = {'ssl': {'ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}})
```
### <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(
        :host     => 'mydemoserver.mariadb.database.azure.com', 
        :username => 'myadmin@mydemoserver',      
        :password => 'yourpassword',    
        :database => 'quickstartdb',
        :ssl_ca => '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'
    )
```
### <a name="golang"></a>Golang
```go
rootCertPool := x509.NewCertPool()
pem, _ := ioutil.ReadFile("/var/www/html/BaltimoreCyberTrustRoot.crt.pem")
if ok := rootCertPool.AppendCertsFromPEM(pem); !ok {
    log.Fatal("Failed to append PEM.")
}
mysql.RegisterTLSConfig("custom", &tls.Config{RootCAs: rootCertPool})
var connectionString string
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin@mydemoserver' , 'yourpassword', 'mydemoserver.mariadb.database.azure.com', 'quickstartdb') 
db, _ := sql.Open("mysql", connectionString)
```
### <a name="java-jdbc"></a>Java (JDBC)
```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " + 
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " + 
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore 
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mysql://%s/%s?serverTimezone=UTC&useSSL=true", 'mydemoserver.mariadb.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin@mydemoserver');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```
### <a name="java-mariadb"></a>Java (MariaDB)
```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " + 
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " + 
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore 
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mariadb://%s/%s?useSSL=true&trustServerCertificate=true", 'mydemoserver.mariadb.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin@mydemoserver');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

<!-- 
## Next steps
Review various application connectivity options following [Connection libraries for Azure Database for MariaDB](concepts-connection-libraries.md)
-->
