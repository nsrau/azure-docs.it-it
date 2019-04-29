---
title: Connettere le applicazioni al Database di Azure per MariaDB
description: Questo documento elenca le stringhe di connessione attualmente supportate per consentire alle applicazioni di connettersi a Database di Azure per MariaDB, tra cui ADO.NET (C#), JDBC, Node.js, ODBC, PHP, Python e Ruby.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 70cd25ff63101fa2a477cde2502d5d286b289366
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61039689"
---
# <a name="how-to-connect-applications-to-azure-database-for-mariadb"></a>Come connettere le applicazioni a Database di Azure per MariaDB
Questo argomento elenca i tipi di stringhe di connessione supportati da Database di Azure per MariaDB, oltre a modelli ed esempi. Nella stringa di connessione possono essere presenti parametri e impostazioni diverse.

- Per ottenere il certificato, vedere [Come configurare SSL](./howto-configure-ssl.md).
- {your_host} = [servername].mariadb.database.azure.com
- {your_user}@{servername} = formato userID per l'autenticazione in modo corretto.  Se si usa solo userID, l'autenticazione avrà esito negativo.

## <a name="adonet"></a>ADO.NET
```csharp
Server={your_host}; Port=3306; Database={your_database}; Uid={username@servername}; Pwd={your_password}; SslMode=Preferred;
```

In questo esempio il nome del server è `mydemoserver`, il nome del database è `wpdb`, il nome utente è `WPAdmin` e la password è `mypassword!2`. La stringa di connessione sarà quindi:

```csharp
Server= "mydemoserver.mariadb.database.azure.com"; Port=3306; Database= "wpdb"; Uid= "WPAdmin@mydemoserver"; Pwd="mypassword!2"; SslMode=Required;
```

## <a name="jdbc"></a>JDBC
```java
String url ="jdbc:mariadb://{your_host}:3306/{your_database}?useSSL=true&trustServerCertificate=true"; myDbConn = DriverManager.getConnection(url, "{username@servername}", {your_password});
```

## <a name="nodejs"></a>Node.js
```javascript
var conn = mysql.createConnection({host: "{your_host}", user: "{your_username}", password: {your_password}, database: {your_database}, port: 3306, ssl:{ca:fs.readFileSync({ca-cert filename})}});
```

## <a name="odbc"></a>ODBC
```cpp
DRIVER={MARIADB ODBC 3.0 Driver}; Server="{your_host}"; Port=3306; Database={your_database}; Uid="{username@servername}"; Pwd={your_password}; sslca={ca-cert filename}; sslverify=1;
```

## <a name="php"></a>PHP
```php
$con=mysqli_init(); mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL); mysqli_real_connect($con, "{your_host}", "{username@servername}", {your_password}, {your_database}, 3306);
```

## <a name="python"></a>Python
```python
cnx = mysql.connector.connect(user="{username@servername}", password={your_password}, host="{your_host}", port=3306, database={your_database}, ssl_ca={ca-cert filename}, ssl_verify_cert=true)
```

## <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(username: "{username@servername}", password: {your_password}, database: {your_database}, host: "{your_host}", port: 3306, sslca:{ca-cert filename}, sslverify:false, sslcipher:'AES256-SHA')
```

## <a name="get-the-connection-string-details-from-the-azure-portal"></a>Ottenere i dettagli della stringa di connessione dal portale di Azure
Nel [portale di Azure](https://portal.azure.com) passare a Database di Azure per MariaDB e quindi fare clic su **Stringhe di connessione** per ottenere l'elenco di stringhe per l'istanza: ![Riquadro Stringhe di connessione nel portale di Azure](./media/howto-connection-strings/connection-strings-on-portal.png)

La stringa include informazioni dettagliate quali il driver, il server e altri parametri di connessione al database. Modificare questi esempi con i parametri personali, come il nome del database, la password e così via. È quindi possibile usare questa stringa per la connessione al server dal codice e dalle applicazioni.

<!-- 
## Next steps
- For more information about connection libraries, see [Concepts - Connection libraries](./concepts-connection-libraries.md).
- -->
