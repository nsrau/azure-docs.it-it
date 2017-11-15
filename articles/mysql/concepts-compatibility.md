---
title: "Compatibilità tra gli strumenti di gestione e i driver MySQL | Microsoft Docs"
description: Driver di MySQL e strumenti di gestione compatibili con il Database di Azure per MySQL
services: mysql
author: seanli1988
ms.author: seanli
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 10/27/2017
ms.openlocfilehash: 4df0dcc7d0f2bde24c1a7e12eea6fa142612a0e7
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2017
---
## <a name="mysql-drivers"></a>Driver di MySQL
Il Database di Azure per MySQL usa la versione di community del database MySQL più diffusa al mondo. Pertanto è compatibile con un'ampia gamma di linguaggi di programmazione e driver. L'obiettivo è di supportare le tre versioni più recenti dei driver MySQL e continuare l'attività con gli autori della community open source per migliorare costantemente le funzionalità e l'usabilità dei driver di MySQL. Nella tabella seguente è riportato un elenco di driver che sono stati testati e che risultano compatibili con il Database di Azure per MySQL 5.6 e 5.7:

| **Driver** | **Collegamenti** | **Versioni compatibili** | **Versioni non compatibili** | **Note** |
| :-------- | :------------------------ | :----------- | :---------------------- | :--------------------------------------- |
| PHP | http://php.net/downloads.php | 5.5 5.6 7.x | 5.3 | Per la connessione PHP 7.0 con SSL MySQLi, aggiungere MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT nella stringa di connessione. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> Impostazione PDO: opzione ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` su false.|
| .Net | [MySqlConnector su Github]: https://github.com/mysql-net/MySqlConnector/releases <br> [Pacchetto di installazione di Nuget]:<br> https://www.nuget.org/packages/MySqlConnector/ | 0.27 e successive | 0.26.5 e precedenti | |
| NodeJS |  [MySQLjs su Github]:<br> https://github.com/mysqljs/mysql/releases <br> [Pacchetto di installazione di NPM]:<br> Eseguire "npm install mysql" da NPM | 2.15 | 2.14.1 e precedenti | |
| GO | https://github.com/go-sql-driver/mysql/releases | 1.3 | 1.2 e precedente | Usare allowNativePasswords=true nella stringa di connessione |
| Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2.0, 2.1, 2.2 | 1.2.2 e precedente | |
| Java | https://downloads.mariadb.org/connector-java/ | 2.1 2.0 1.6 | 1.5.5 e precedente | |

## <a name="management-tools"></a>Strumenti di gestione
Il vantaggio della compatibilità si estende anche agli strumenti di gestione del database. Gli strumenti esistenti continueranno a funzionare con Database di Azure per MySQL, purché la modifica del database operi entro i confini di autorizzazione dell'utente. Nella tabella seguente sono elencati tre strumenti di gestione del database comuni che sono stati testati e che risultano compatibili con il Database di Azure per MySQL 5.6 e 5.7:

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