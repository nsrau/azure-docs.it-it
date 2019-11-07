---
title: Compatibilità degli strumenti di gestione e dei driver di database di Azure per MySQL
description: Questo articolo descrive i driver MySQL e gli strumenti di gestione compatibili con Database di Azure per MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 11/06/2019
ms.openlocfilehash: 916c02c30f6d54aef44459775a7a437fe96a4ff9
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720150"
---
# <a name="mysql-drivers-and-management-tools-compatible-with-azure-database-for-mysql"></a>Driver MySQL e strumenti di gestione compatibili con Database di Azure per MySQL
Questo articolo descrive i driver e gli strumenti di gestione compatibili con il Database di Azure per MySQL.

## <a name="mysql-drivers"></a>Driver di MySQL
Database di Azure per MySQL usa la versione di community del database MySQL più diffusa al mondo. Pertanto è compatibile con un'ampia gamma di linguaggi di programmazione e driver. L'obiettivo è supportare le tre versioni più recenti dei driver MySQL e continuare l'attività  con gli autori della community open source per migliorare costantemente le funzionalità  e l'usabilità  dei driver MySQL. Nella tabella seguente è riportato un elenco di driver che sono stati testati e che risultano compatibili con Database di Azure per MySQL 5.6 e 5.7:

| **Linguaggio di programmazione** | **Driver** | **Collegamenti** | **Versioni compatibili** | **Versioni incompatibili** | **Note** |
| :----------------------- | :--------- | :-------- | :---------------------- | :------------------------ | :-------- |
| PHP | mysqli, pdo_mysql, mysqlnd | https://secure.php.net/downloads.php | 5.5, 5.6, 7.x | 5.3 | Per la connessione PHP 7.0 con SSL MySQLi, aggiungere MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT nella stringa di connessione. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> Impostazione PDO: opzione ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` su false.|
| .NET | Connettore Async MySQL per .NET | https://github.com/mysql-net/MySqlConnector <br> [Pacchetto di installazione di Nuget](https://www.nuget.org/packages/MySqlConnector/) | 0.27 e successive | 0.26.5 e precedenti | |
| .NET | Connettore MySQL/NET | https://github.com/mysql/mysql-connector-net | 8.0, 7.0, 6.10 |  | Le connessioni potrebbero non riuscire in alcuni sistemi Windows non UTF8 a causa di un bug di codifica. |
| Node.js | mysqljs | https://github.com/mysqljs/mysql/ <br> Pacchetto di installazione di NPM:<br> Eseguire `npm install mysql` da NPM | 2.15 | 2.14.1 e precedenti | |
| Node.js | node-mysql2 | https://github.com/sidorares/node-mysql2 | 1.3.4 + | | |
| Go | Vai al driver MySQL | https://github.com/go-sql-driver/mysql/releases | 1,3, 1,4 | 1.2 e precedente | Utilizzare `allowNativePasswords=true` nella stringa di connessione per la versione 1,3. La versione 1,4 contiene una correzione e `allowNativePasswords=true` non è più necessaria. |
| Python | Connettore MySQL/python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2,0, 2,1, 2,2, usare 8.0.16 + con MySQL 8,0  | 1.2.2 e precedente | |
| Python | PyMySQL | https://pypi.org/project/PyMySQL/ | 0.7.11, 0.8.0, 0.8.1, 0.9.3 + | 0.9.0-0.9.2 (regressione in web2py) | |
| Java | Connettore MariaDB/J | https://downloads.mariadb.org/connector-java/ | 2.1, 2.0, 1.6 | 1.5.5 e precedenti | | 
| Java | Connettore MySQL/J | https://github.com/mysql/mysql-connector-j | 5.1.20 +, usare 8.0.17 + con MySQL 8,0 | 5.1.9 e versioni precedenti | |
| C | Connettore MySQL/C (libmysqlclient) | https://dev.mysql.com/doc/refman/5.7/en/c-api-implementations.html | 6.0.2 + | | |
| C | Connettore MySQL/ODBC (ODBC) | https://github.com/mysql/mysql-connector-odbc | 3.51.29 + | | |
| C++ | Connettore MySQL/C++ | https://github.com/mysql/mysql-connector-cpp | 1.1.9 + | 1.1.3 e versioni precedenti | | 
| C++ | MySQL + +| https://tangentsoft.net/mysql++ | 3.2.3 + | | |
| Ruby | mysql2 | https://github.com/brianmario/mysql2 | 0.4.10 + | | |
| R | RMySQL | https://github.com/rstats-db/RMySQL | 0.10.16 + | | |
| Swift | MySQL-Swift | https://github.com/novi/mysql-swift | 0.7.2 + | | |
| Swift | Vapor/MySQL | https://github.com/vapor/mysql-kit | 2.0.1 + | | |

## <a name="management-tools"></a>Strumenti di gestione
Il vantaggio della compatibilità si estende anche agli strumenti di gestione del database. Gli strumenti esistenti continueranno a funzionare con Database di Azure per MySQL, purché la modifica del database operi entro i confini di autorizzazione dell'utente. Nella tabella seguente sono elencati tre strumenti comuni di gestione del database che sono stati testati e che risultano compatibili con il Database di Azure per MySQL 5.6 e 5.7:

|                                     | **MySQL Workbench 6.x e versioni successive** | **Navicat 12** | **PHPMyAdmin 4.x e versioni successive** |
| :---------------------------------- | :----------------------------- | :------------- | :-------------------------|
| Creare, aggiornare, leggere, scrivere, eliminare | X | X | X |
| Connessione SSL | X | X | X |
| Completamento automatico della query SQL | X | X |  |
| Importare ed esportare dati | X | X | X | 
| Esportare in più formati | X | X | X |
| Backup e ripristino |  | X |  |
| Visualizzare i parametri del server | X | X | X |
| Visualizzare le connessioni client | X | X | X |

## <a name="next-steps"></a>Passaggi successivi

- [Troubleshoot connection issues to Azure Database for MySQL](howto-troubleshoot-common-connection-issues.md) (Risolvere i problemi di connessione a database di Azure per MySQL)