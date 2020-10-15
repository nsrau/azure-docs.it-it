---
title: Connettività crittografata con TLS/SSL nel database di Azure per MySQL-server flessibile
description: Istruzioni e informazioni su come connettersi usando TLS/SSL nel database di Azure per MySQL-server flessibile.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 24a8dd4d21cb6ab6edeb985db4e6e6a1349a758d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90940491"
---
# <a name="encrypted-connectivity-using-transport-layer-security-tls-12-in-azure-database-for-mysql---flexible-server"></a>Connettività crittografata con Transport Layer Security (TLS 1,2) nel database di Azure per MySQL-server flessibile

> [!IMPORTANT]
> Il server flessibile di Database di Azure per MySQL è attualmente disponibile in anteprima pubblica

Il server flessibile database di Azure per MySQL supporta la connessione delle applicazioni client al servizio MySQL usando Transport Layer Security (TLS), precedentemente noto come Secure Sockets Layer (SSL). TLS è un protocollo standard del settore che garantisce connessioni di rete crittografate tra il server di database e le applicazioni client, consentendo di rispettare i requisiti di conformità.

Il server flessibile database di Azure per MySQL supporta solo connessioni crittografate con Transport Layer Security (TLS 1,2) e tutte le connessioni in ingresso con TLS 1,0 e TLS 1,1 verranno negate. Per tutti i server flessibili, l'applicazione delle connessioni TLS è abilitata e non è possibile disabilitare TLS/SSL per la connessione al server flessibile.

## <a name="applications-that-require-certificate-verification-for-tlsssl-connectivity"></a>Applicazioni che richiedono la verifica del certificato per la connettività TLS/SSL
In alcuni casi, le applicazioni richiedono un file di certificato locale generato da un file di certificato dell'autorità di certificazione (CA) attendibile per la connessione sicura. Il server flessibile database di Azure per MySQL usa la *CA radice globale DigiCert*. Scaricare questo certificato necessario per comunicare tramite SSL dalla [CA radice globale DigiCert](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) e salvare il file del certificato nel percorso preferito. Ad esempio, questa esercitazione usa `c:\ssl`.

### <a name="connect-using-mysql-command-line-client-with-tlsssl"></a>Connettersi usando il client da riga di comando MySQL con TLS/SSL

Se il server flessibile è stato creato con l'opzione per l'*accesso privato (integrazione rete virtuale)* , sarà necessario connettersi al server da una risorsa all'interno della stessa rete virtuale usata dal server. È possibile creare una macchina virtuale e aggiungerla alla rete virtuale creata con il server flessibile.

Se il server flessibile è stato creato con l'opzione per l'*accesso pubblico (indirizzi IP consentiti)* , è possibile aggiungere l'indirizzo IP locale all'elenco di regole del firewall nel server.

È possibile scegliere [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) o [MySQL Workbench](./connect-workbench.md)--> per connettersi al server dall'ambiente locale. 

L'esempio seguente illustra come connettersi al server usando l'interfaccia della riga di comando di MySQL. Usare l' `--ssl-mode=REQUIRED` impostazione della stringa di connessione per applicare la verifica del certificato TLS/SSL. Passare il percorso del file del certificato locale al `--ssl-ca` parametro. Sostituire i valori con il nome server e la password effettivi. 

```bash
 mysql.exe -h mydemoserver.mysql.database.azure.com -u myadmin -p --ssl-mode=REQUIRED --ssl-ca=c:\ssl\DigiCertGlobalRootCA.crt.pem
```
> [!Note]
> Confermare che il valore passato a `--ssl-ca` corrisponda al percorso del file del certificato salvato.

### <a name="verify-the-tlsssl-connection"></a>Verificare la connessione TLS/SSL

Eseguire il comando MySQL **status** per verificare di avere effettuato la connessione al server MySQL usando TLS/SSL:

```dos
mysql> status
```
Verificare che la connessione sia crittografata esaminando l'output, che dovrebbe mostrare:  **SSL: la crittografia in uso è AES256-SHA**. Questo pacchetto di crittografia Mostra un esempio e basato sul client. è possibile visualizzare un pacchetto di crittografia diverso.

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Assicurarsi che l'applicazione o il Framework supporti le connessioni TLS

Alcuni framework di applicazioni che usano MySQL per i servizi di database non abilitano TLS per impostazione predefinita durante l'installazione. Il server MySQL impone le connessioni TLS, ma se l'applicazione non è configurata per TLS, l'applicazione potrebbe non riuscire a connettersi al server di database. Consultare la documentazione dell'applicazione per informazioni su come abilitare le connessioni TLS.

## <a name="sample-code"></a>Codice di esempio
Le stringhe di connessione predefinite nella pagina "stringhe di connessione" disponibili per il server nella portale di Azure includono i parametri necessari per la connessione dei linguaggi comuni al server di database tramite TLS/SSL. Il parametro TLS/SSL varia in base al connettore. Ad esempio, "useSSL = true", "sslmode = required" o "ssl_verify_cert = true" e altre varianti.

Per stabilire una connessione crittografata al server flessibile tramite TLS/SSL dall'applicazione, vedere gli esempi di codice seguenti:

### <a name="php"></a>PHP

```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/DigiCertGlobalRootCA.crt.pem", NULL, NULL);
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```

### <a name="php-using-pdo"></a>PHP (usando DOP)

```phppdo
$options = array(
    PDO::MYSQL_ATTR_SSL_CA => '/var/www/html/DigiCertGlobalRootCA.crt.pem'
);
$db = new PDO('mysql:host=mydemoserver.mysql.database.azure.com;port=3306;dbname=databasename', 'myadmin', 'yourpassword', $options);
```

### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector Python)

```python
try:
    conn = mysql.connector.connect(user='myadmin',
                                   password='yourpassword',
                                   database='quickstartdb',
                                   host='mydemoserver.mysql.database.azure.com',
                                   ssl_ca='/var/www/html/DigiCertGlobalRootCA.crt.pem')
except mysql.connector.Error as err:
    print(err)
```

### <a name="python-pymysql"></a>Python (PyMySQL)

```python
conn = pymysql.connect(user='myadmin',
                       password='yourpassword',
                       database='quickstartdb',
                       host='mydemoserver.mysql.database.azure.com',
                       ssl={'ca': '/var/www/html/DigiCertGlobalRootCA.crt.pem'})
```

### <a name="django-pymysql"></a>Django (PyMySQL)

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'quickstartdb',
        'USER': 'myadmin',
        'PASSWORD': 'yourpassword',
        'HOST': 'mydemoserver.mysql.database.azure.com',
        'PORT': '3306',
        'OPTIONS': {
            'ssl': {'ca': '/var/www/html/DigiCertGlobalRootCA.crt.pem'}
        }
    }
}
```

### <a name="ruby"></a>Ruby

```ruby
client = Mysql2::Client.new(
        :host     => 'mydemoserver.mysql.database.azure.com',
        :username => 'myadmin',
        :password => 'yourpassword',
        :database => 'quickstartdb',
        :sslca => '/var/www/html/DigiCertGlobalRootCA.crt.pem'
    )
```

### <a name="golang"></a>Golang

```go
rootCertPool := x509.NewCertPool()
pem, _ := ioutil.ReadFile("/var/www/html/DigiCertGlobalRootCA.crt.pem")
if ok := rootCertPool.AppendCertsFromPEM(pem); !ok {
    log.Fatal("Failed to append PEM.")
}
mysql.RegisterTLSConfig("custom", &tls.Config{RootCAs: rootCertPool})
var connectionString string
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin' , 'yourpassword', 'mydemoserver.mysql.database.azure.com', 'quickstartdb')
db, _ := sql.Open("mysql", connectionString)
```

### <a name="java-mysql-connector-for-java"></a>Java (MySQL Connector per Java)

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

url = String.format("jdbc:mysql://%s/%s?serverTimezone=UTC&useSSL=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="java-mariadb-connector-for-java"></a>Java (MariaDB Connector per Java)

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

url = String.format("jdbc:mariadb://%s/%s?useSSL=true&trustServerCertificate=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="net-mysqlconnector"></a>.NET (MySqlConnector)

```csharp
var builder = new MySqlConnectionStringBuilder
{
    Server = "mydemoserver.mysql.database.azure.com",
    UserID = "myadmin",
    Password = "yourpassword",
    Database = "quickstartdb",
    SslMode = MySqlSslMode.VerifyCA,
    SslCa = "DigiCertGlobalRootCA.crt.pem",
};
using (var connection = new MySqlConnection(builder.ConnectionString))
{
    connection.Open();
}
```

## <a name="next-steps"></a>Passaggi successivi
- [Usare MySQL Workbench per connettersi ed eseguire query sui dati nel server flessibile di database di Azure per MySQL](./connect-workbench.md)
- [Usare PHP per connettersi ed eseguire query sui dati nel server flessibile database di Azure per MySQL](./connect-php.md)
- [Creare e gestire una rete virtuale di server flessibili per database di Azure per MySQL usando l'interfaccia della riga di](./how-to-manage-virtual-network-cli.md)comando
- Altre informazioni sulla [rete nel server flessibile database di Azure per MySQL](./concepts-networking.md)
- Altre informazioni sulle [regole del firewall per server flessibili per database di Azure per MySQL](./concepts-networking.md#public-access-allowed-ip-addresses)
