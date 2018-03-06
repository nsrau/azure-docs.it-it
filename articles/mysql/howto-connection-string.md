---
title: Connettere le applicazioni a Database di Azure per MySQL
description: Questo documento elenca le stringhe di connessione attualmente supportate per consentire alle applicazioni di connettersi a Database di Azure per MySQL, tra cui ADO.NET (C#), JDBC, Node.js, ODBC, PHP, Python e Ruby.
services: mysql
author: mswutao
ms.author: wuta
editor: jasonwhowell
manager: kfile
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: e7b200fd1de79f0bca680bdedc34fa376cf07d68
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="how-to-connect-applications-to-azure-database-for-mysql"></a>Come connettere le applicazioni a Database di Azure per MySQL
Questo argomento elenca i tipi di stringa di connessione supportati da Database di Azure per MySQL, oltre a modelli ed esempi. Nella stringa di connessione possono essere presenti parametri e impostazioni diverse.

- Per ottenere il certificato, vedere [Come configurare SSL](./howto-configure-ssl.md).
- {your_host} = <servername>.mysql.database.azure.com
- {your_user}@{servername} = formato userID per l'autenticazione in modo corretto.  Se si usa solo userID, l'autenticazione avrà esito negativo.

## <a name="adonet"></a>ADO.NET
```ado.net
Server={your_host};Port={your_port};Database={your_database};Uid={username@servername};Pwd={your_password};[SslMode=Required;]
```

In questo esempio il nome del server è `mydemoserver`, il nome del database è `wpdb`, il nome utente è `WPAdmin` e la password è `mypassword!2`. La stringa di connessione sarà quindi:

```ado.net
Server= "mydemoserver.mysql.database.azure.com"; Port=3306; Database= "wpdb"; Uid= "WPAdmin@mydemoserver"; Pwd="mypassword!2"; SslMode=Required;
```

## <a name="jdbc"></a>JDBC
```jdbc
String url ="jdbc:mysql://%s:%s/%s[?verifyServerCertificate=true&useSSL=true&requireSSL=true]",{your_host},{your_port},{your_database}"; myDbConn = DriverManager.getConnection(url, {username@servername}, {your_password}";
```

## <a name="nodejs"></a>Node.js
```node.js
var conn = mysql.createConnection({host: {your_host}, user: {username@servername}, password: {your_password}, database: {your_database}, Port: {your_port}[, ssl:{ca:fs.readFileSync({ca-cert filename})}}]);
```

## <a name="odbc"></a>ODBC
```odbc
DRIVER={MySQL ODBC 5.3 UNICODE Driver};Server={your_host};Port={your_port};Database={your_database};Uid={username@servername};Pwd={your_password}; [sslca={ca-cert filename}; sslverify=1; Option=3;]
```

## <a name="php"></a>PHP
```php
$con=mysqli_init(); [mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL);] mysqli_real_connect($con, {your_host}, {username@servername}, {your_password}, {your_database}, {your_port});
```

## <a name="python"></a>Python
```python
cnx = mysql.connector.connect(user={username@servername}, password={your_password}, host={your_host}, port={your_port}, database={your_database}[, ssl_ca={ca-cert filename}, ssl_verify_cert=true])
```

## <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(username: {username@servername}, password: {your_password}, database: {your_database}, host: {your_host}, port: {your_port}[, sslca:{ca-cert filename}, sslverify:false, sslcipher:'AES256-SHA'])
```

## <a name="get-the-connection-string-details-from-the-azure-portal"></a>Ottenere i dettagli della stringa di connessione dal portale di Azure
Nel [portale di Azure](https://portal.azure.com) passare a Database di Azure per il server MySQL e quindi fare clic su **Stringhe di connessione** per ottenere l'elenco di stringhe per l'istanza: ![Riquadro Stringhe di connessione nel portale di Azure](./media/howto-connection-strings/connection-strings-on-portal.png)

La stringa include informazioni dettagliate quali il driver, il server e altri parametri di connessione al database. Modificare questi esempi con i parametri personali, come il nome del database, la password e così via. È quindi possibile usare questa stringa per la connessione al server dal codice e dalle applicazioni.

## <a name="next-steps"></a>Passaggi successivi
- Per altre informazioni sulle raccolte di connessioni, vedere [Concepts - Connection libraries](./concepts-connection-libraries.md) (Concetti: raccolte di connessioni).
