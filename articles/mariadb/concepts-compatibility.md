---
title: Compatibilità degli strumenti di Database di Azure per la gestione e i driver di MariaDB
description: Questo articolo descrive i driver di MariaDB e gli strumenti di gestione compatibili con Database di Azure per MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/19/2019
ms.openlocfilehash: 7a3d9a5f87a565625052fc54e3ecccc99fd928a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61386812"
---
# <a name="mariadb-drivers-and-management-tools-compatible-with-azure-database-for-mariadb"></a>I driver di MariaDB e strumenti di gestione compatibili con il Database di Azure per MariaDB

Questo articolo descrive i driver e gli strumenti di gestione compatibili con Database di Azure per MariaDB.

## <a name="mariadb-drivers"></a>Driver di MariaDB

Database di Azure per MariaDB utilizza l'edizione community di server MariaDB. Pertanto è compatibile con un'ampia gamma di linguaggi di programmazione e driver. API e protocolli di MariaDB sono compatibili con quelle usate da MySQL. Ciò significa che i connettori che interagiscono con MySQL dovrebbero funzionare anche con MariaDB.

L'obiettivo è supportare le tre versioni più recenti, i driver di MariaDB e continuare l'attività con gli autori della community open source per migliorare costantemente le funzionalità e l'usabilità dei driver di MariaDB. Nella tabella seguente viene fornito un elenco di driver che sono stati testati e risultano compatibili con Database di Azure per MariaDB 10.2:

**Driver** | **Collegamenti** | **Versioni compatibili** | **Versioni incompatibili** | **Note**
---|---|---|---|---
PHP | https://secure.php.net/downloads.php | 5.5, 5.6, 7.x | 5.3 | Per la connessione PHP 7.0 con SSL MySQLi, aggiungere MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT nella stringa di connessione. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> Impostazione PDO: opzione ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` su false.
.NET | [MySqlConnector su GitHub](https://github.com/mysql-net/MySqlConnector) <br> [Pacchetto di installazione di Nuget](https://www.nuget.org/packages/MySqlConnector/) | 0.27 e successive | 0.26.5 e precedenti |
Connettore MySQL/NET | [Connettore MySQL/NET](https://github.com/mysql/mysql-connector-net) | 8.0, 7.0, 6.10 |  | Le connessioni potrebbero non riuscire in alcuni sistemi Windows non UTF8 a causa di un bug di codifica.
Node.js |  [MySQLjs su GitHub](https://github.com/mysqljs/mysql/) <br> Pacchetto di installazione di NPM:<br> Eseguire `npm install mysql` da NPM | 2.15 | 2.14.1 e precedenti
GO | https://github.com/go-sql-driver/mysql/releases | 1.3, 1.4 | 1.2 e precedenti | Usare `allowNativePasswords=true` nella stringa di connessione per la versione 1.3. Versione 1.4 contiene una correzione e `allowNativePasswords=true` non è più necessario.
Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2.0, 2.1, 2.2 | 1.2.2 e precedenti |
Java | https://downloads.mariadb.org/connector-java/ | 2.1, 2.0, 1.6 | 1.5.5 e precedenti |

## <a name="management-tools"></a>Strumenti di gestione

Il vantaggio della compatibilità si estende anche agli strumenti di gestione del database. Gli strumenti esistenti continueranno a funzionare con Database di Azure per MariaDB, purché la modifica del database operi entro i confini delle autorizzazioni utente. Nella tabella seguente sono elencati tre strumenti di gestione comuni del database che sono stati testati e risultano compatibili con Database di Azure per MariaDB 10.2:

| | **MySQL Workbench 6.x e versioni successive** | **Navicat 12** | **PHPMyAdmin 4.x e versioni successive**
---|---|---|---
Creare, aggiornare, leggere, scrivere, eliminare | X | X | X
Connessione SSL | X | X | X
Completamento automatico della query SQL | X | X |
Importare ed esportare dati | X | X | X
Esportare in più formati | X | X | X
Backup e ripristino |  | X |
Visualizzare i parametri del server | X | X | X
Visualizzare le connessioni client | X | X | X

## <a name="next-steps"></a>Passaggi successivi

- [Risolvere i problemi di connessione a Database di Azure per MariaDB](howto-troubleshoot-common-connection-issues.md)