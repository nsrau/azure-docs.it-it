---
title: Compatibilità di driver e strumenti - Database di Azure per MariaDB
description: Questo articolo descrive i driver EANDB e gli strumenti di gestione compatibili con il database di Azure per MariaDB.This article describes the MariaDB drivers and management tools that are compatible with Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: a0cee198f028fd90e04dac15e98d7cd33aee9201
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79532349"
---
# <a name="mariadb-drivers-and-management-tools-compatible-with-azure-database-for-mariadb"></a>Driver e strumenti di gestione MariaDB compatibili con Azure Database per MariaDB

Questo articolo descrive i driver e gli strumenti di gestione compatibili con il database di Azure per MariaDB.This article describes the drivers and management tools that are compatible with Azure Database for MariaDB.

## <a name="mariadb-drivers"></a>Driver MariaDB

Il database di Azure per MariaDB usa l'edizione community del server MariaDB.Azure Database for MariaDB uses the community edition of MariaDB server. Pertanto è compatibile con un'ampia gamma di linguaggi di programmazione e driver. L'API e il protocollo di MariaDB sono compatibili con quelli utilizzati da MySQL. Ciò significa che i connettori che funzionano con MySQL devono funzionare anche con MariaDB.

L'obiettivo è quello di supportare le tre versioni più recenti dei driver MariaDB e gli sforzi con gli autori della comunità open source per migliorare costantemente la funzionalità e l'usabilità dei driver MariaDB continuano. Nella tabella seguente è disponibile un elenco di driver che sono stati testati e che sono compatibili con il database di Azure per MariaDB 10.2:

**autista** | **Link** | **Versioni compatibili** | **Versioni incompatibili** | **Note**
---|---|---|---|---
PHP | https://secure.php.net/downloads.php | 5.5, 5.6, 7.x | 5.3 | Per la connessione PHP 7.0 con SSL MySQLi, aggiungere MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT nella stringa di connessione. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> Impostazione PDO: opzione ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` su false.
.NET | [MySqlConnector su GitHub](https://github.com/mysql-net/MySqlConnector) <br> [Pacchetto di installazione di Nuget](https://www.nuget.org/packages/MySqlConnector/) | 0.27 e successive | 0.26.5 e precedenti |
Connettore MySQL/NET | [Connettore MySQL/NET](https://github.com/mysql/mysql-connector-net) | 8.0, 7.0, 6.10 |  | Le connessioni potrebbero non riuscire in alcuni sistemi Windows non UTF8 a causa di un bug di codifica.
Node.js |  [MySQLjs su GitHub](https://github.com/mysqljs/mysql/) <br> Pacchetto di installazione di NPM:<br> Eseguire `npm install mysql` da NPM | 2.15 | 2.14.1 e precedenti
GO | https://github.com/go-sql-driver/mysql/releases | 1.3, 1.4 | 1.2 e precedenti | Utilizzare `allowNativePasswords=true` nella stringa di connessione per la versione 1.3.Use in the connection string for version 1.3. La versione 1.4 `allowNativePasswords=true` contiene una correzione e non è più necessaria.
Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2.0, 2.1, 2.2 | 1.2.2 e precedenti |
Java | https://downloads.mariadb.org/connector-java/ | 2.1, 2.0, 1.6 | 1.5.5 e precedenti |

## <a name="management-tools"></a>Strumenti di gestione

Il vantaggio della compatibilità si estende anche agli strumenti di gestione del database. Gli strumenti esistenti devono continuare a usare il database di Azure per MariaDB, purché la modifica del database funzioni entro i limiti delle autorizzazioni utente. Nella tabella seguente sono elencati tre strumenti comuni di gestione dei database che sono stati testati e che sono compatibili con il database di Azure per MariaDB 10.2:Three common database management tools that have been tested and found to be compatible with Azure Database for MariaDB 10.2 are listed in the following table:

| | **MySQL Workbench 6.x e versioni successive** | **Navicat 12** | **PHPMyAdmin 4.x e versioni successive**
---|---|---|---
Creare, aggiornare, leggere, scrivere, eliminare | X | X | X
Connessione SSL | X | X | X
Completamento automatico della query SQL | X | X |
Importazione ed esportazione dati | X | X | X
Esportare in più formati | X | X | X
Backup e ripristino |  | X |
Visualizzare i parametri del server | X | X | X
Visualizzare le connessioni client | X | X | X

## <a name="next-steps"></a>Passaggi successivi

- [Risolvere i problemi di connessione a database di Azure per MariaDB](howto-troubleshoot-common-connection-issues.md)