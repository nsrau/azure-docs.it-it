---
title: Come connettere le applicazioni a Database di Azure per MySQL | Microsoft Docs
description: Questo documento elenca tutte le stringhe di connessione attualmente supportate per consentire alle applicazioni di connettersi a Database di Azure per MySQL
services: mysql
author: wuta
ms.author: wuta
editor: jasonh
manager: jhubbard
ms.assetid: 
ms.service: mysql-database
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 93b74f146cd0a75437e85dbb1c941b29534ffab2
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017

---

# <a name="how-to-connect-applications-to-azure-database-for-mysql"></a>Come connettere le applicazioni a Database di Azure per MySQL
Questo documento elenca tutti i tipi di stringa di connessione supportati da Database di Azure per MySQL, oltre a modelli ed esempi. Nella stringa di connessione possono essere presenti parametri diversi e impostazioni diverse.

- Vedere il documento [How to configure SSL](./howto-configure-ssl.md) (Come configurare SSL) per ottenere il certificato
- {your_host} = <servername>.mysql.database.azure.com

## <a name="adonet"></a>ADO.NET
```ado.net
Server={your_host};Port={your_port};Database={your_database};Uid={your_username};Pwd={your_password};[SslMode=Required;]
```

Si supponga ad esempio che il nome del server sia **wpdemo**, il nome del database **wpdb**, il nome utente **WPAdmin** e la password **orcas!2**.

La stringa di connessione sarà:

```ado.net
Server= "wpdemo.mysql.database.azure.com"; Port=3306; Database= "wpdb"; Uid= "WPAdmin@wpdemo"; Pwd="orcas!2"; SslMode=Required;
```

## <a name="jdbc"></a>JDBC
```jdbc
String url ="jdbc:mysql://%s:%s/%s[?verifyServerCertificate=true&useSSL=true&requireSSL=true]",{your_host},{your_port},{your_database}"; myDbConn = DriverManager.getConnection(url, {your_username}, {your_password}";
```

## <a name="nodejs"></a>Node.JS
```node.js
var conn = mysql.createConnection({host: {your_host}, user: {your_username}, password: {your_password}, database: {your_database}, Port: {your_port}[, ssl:{ca:fs.readFileSync({ca-cert filename})}}]);
```

## <a name="odbc"></a>ODBC
```odbc
DRIVER={MySQL ODBC 5.3 UNICODE Driver};Server={your_host};Port={your_port};Database={your_database};Uid={your_username};Pwd={your_password}; [sslca={ca-cert filename}; sslverify=1; Option=3;]
```

## <a name="php"></a>PHP
```php
$con=mysqli_init(); [mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL);] mysqli_real_connect($con, {your_host}, {your_username}, {your_password}, {your_database}, {your_port});
```

## <a name="python"></a>Python
```python
cnx = mysql.connector.connect(user={your_username}, password={your_password}, host={your_host}, port={your_port}, database={your_database}[, ssl_ca={ca-cert filename}, ssl_verify_cert=true])
```

## <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(username: {your_username}, password: {your_password}, database: {your_database}, host: {your_host}, port: {your_port}[, sslca:{ca-cert filename}, sslverify:false, sslcipher:'AES256-SHA'])
```

## <a name="get-connection-string-from-portal"></a>Ottenere la stringa di connessione dal portale
Nel [portale di Azure](https://portal.azure.com) andare al database di Azure per MySQL e fare clic su **Stringhe di connessione** per ottenere l'elenco di stringhe per l'istanza: ![Stringhe di connessione nel portale](./media/howto-connection-strings/connection-strings-on-portal.png)

La stringa include informazioni dettagliate quali il driver, il server e altri parametri di connessione al database. Copiare la stringa di connessione richiesta, quindi modificarla con i propri parametri, ad esempio nome del database, password e così via. Sarà quindi possibile usare questa stringa per connettersi al server.

## <a name="next-steps"></a>Passaggi successivi
- Per altre informazioni sulla raccolta di connessioni, vedere [Concepts - Connection libraries](./concepts-connection-libraries.md) (Concetti: raccolte di connessioni)

