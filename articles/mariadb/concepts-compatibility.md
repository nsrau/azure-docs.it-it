---
title: Compatibilità di driver e strumenti-database di Azure per MariaDB
description: Questo articolo descrive i driver e gli strumenti di gestione di MariaDB compatibili con il database di Azure per MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: daec0aaf04cae26b6467cc4472305e75517cee5d
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74772988"
---
# <a name="mariadb-drivers-and-management-tools-compatible-with-azure-database-for-mariadb"></a>Driver e strumenti di gestione di MariaDB compatibili con il database di Azure per MariaDB

Questo articolo descrive i driver e gli strumenti di gestione compatibili con database di Azure per MariaDB.

## <a name="mariadb-drivers"></a>Driver MariaDB

Il database di Azure per MariaDB usa l'edizione community di MariaDB server. Pertanto è compatibile con un'ampia gamma di linguaggi di programmazione e driver. Il protocollo e l'API di MariaDB sono compatibili con quelli usati da MySQL. Ciò significa che i connettori che funzionano con MySQL dovrebbero funzionare anche con MariaDB.

L'obiettivo è supportare le tre versioni più recenti dei driver MariaDB e gli autori della community open source per migliorare costantemente la funzionalità e l'usabilità dei driver MariaDB continuare. Nella tabella seguente è disponibile un elenco di driver testati e risultanti compatibili con il database di Azure per MariaDB 10,2:

**Driver** | **Collegamenti** | **Versioni compatibili** | **Versioni incompatibili** | **Note**
---|---|---|---|---
PHP | https://secure.php.net/downloads.php | 5.5, 5.6, 7.x | 5.3 | Per la connessione PHP 7.0 con SSL MySQLi, aggiungere MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT nella stringa di connessione. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> Impostazione PDO: opzione ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` su false.
.NET | [MySqlConnector su GitHub](https://github.com/mysql-net/MySqlConnector) <br> [Pacchetto di installazione di Nuget](https://www.nuget.org/packages/MySqlConnector/) | 0.27 e successive | 0.26.5 e precedenti |
Connettore MySQL/NET | [Connettore MySQL/NET](https://github.com/mysql/mysql-connector-net) | 8.0, 7.0, 6.10 |  | Le connessioni potrebbero non riuscire in alcuni sistemi Windows non UTF8 a causa di un bug di codifica.
Node.js |  [MySQLjs su GitHub](https://github.com/mysqljs/mysql/) <br> Pacchetto di installazione di NPM:<br> Eseguire `npm install mysql` da NPM | 2.15 | 2.14.1 e precedenti
GO | https://github.com/go-sql-driver/mysql/releases | 1,3, 1,4 | 1.2 e precedenti | Utilizzare `allowNativePasswords=true` nella stringa di connessione per la versione 1,3. La versione 1,4 contiene una correzione e `allowNativePasswords=true` non è più necessaria.
Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2.0, 2.1, 2.2 | 1.2.2 e precedenti |
Java | https://downloads.mariadb.org/connector-java/ | 2.1, 2.0, 1.6 | 1.5.5 e precedenti |

## <a name="management-tools"></a>Strumenti di gestione

Il vantaggio della compatibilità si estende anche agli strumenti di gestione del database. Gli strumenti esistenti continueranno a funzionare con database di Azure per MariaDB, purché la manipolazione del database funzioni entro i limiti delle autorizzazioni utente. Nella tabella seguente sono elencati tre strumenti di gestione di database comuni testati e che risultano compatibili con il database di Azure per MariaDB 10,2:

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