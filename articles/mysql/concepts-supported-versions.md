---
title: Versioni supportate - Database di Azure per MySQLSupported versions - Azure Database for MySQL
description: Informazioni sulle versioni del server MySQL supportate nel servizio Database di Azure per MySQL.Learn which versions of the MySQL server are supported in the Azure Database for MySQL service.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 197b3100190711a51cfe125fe1214a59c18e1491
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536973"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Versioni supportate del Database di Azure per il server MySQL

Database di Azure per MySQL è stato sviluppato da [MySQL Community Edition](https://www.mysql.com/products/community/) tramite il motore InnoDB.

MySQL usa lo schema di denominazione X.Y.Z. X è la versione principale, Y è la versione secondaria Z è la versione di correzione di bug. Per altre informazioni sullo schema, vedere la [documentazione di MySQL](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).

> [!NOTE]
> Nel servizio viene usato un gateway per reindirizzare le connessioni alle istanze del server. Dopo che è stata stabilita la connessione, il client MySQL visualizza la versione di MySQL impostata nel gateway e non la versione effettiva in esecuzione nell'istanza del server MySQL. Per determinare la versione dell'istanza del server MySQL, usare il comando `SELECT VERSION();` dal prompt di MySQL.

Al momento il Database di Azure per MySQL supporta le versioni indicate di seguito:

## <a name="mysql-version-56"></a>MySQL versione 5.6

Rilascio della correzione di bug: 5.6.45

Fare riferimento alle note sulla [versione](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-45.html) di MySQL per ulteriori informazioni sui miglioramenti e le correzioni in questa versione.

## <a name="mysql-version-57"></a>MySQL versione 5.7

Rilascio della correzione di bug: 5.7.27

Fare riferimento alle note sulla [versione](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-27.html) di MySQL per ulteriori informazioni sui miglioramenti e le correzioni in questa versione.

## <a name="mysql-version-80"></a>MySQL versione 8.0

Rilascio della correzione di bug: 8.0.15

Fare riferimento alle note sulla [versione](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) di MySQL per ulteriori informazioni sui miglioramenti e le correzioni in questa versione.

## <a name="managing-updates-and-upgrades"></a>Gestire gli aggiornamenti
Il servizio gestisce automaticamente l'applicazione di patch per gli aggiornamenti delle versioni di correzione dei bug. Ad esempio, da 5.7.20 a 5.7.21.  

Attualmente, gli aggiornamenti di versioni principali e secondarie non sono supportati. Ad esempio l'aggiornamento da MySQL 5.6 a MySQL 5.7 non è supportato. Se si vuole eseguire l'aggiornamento dalla 5.6 alla 5.7, eseguire un [dump e ripristinarlo](./concepts-migrate-dump-restore.md) in un server creato con la nuova versione del motore.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su quote e limitazioni specifiche delle risorse in base al livello di **servizio,** vedere Livelli di servizioFor information about specific resource quotas and limitations based on your service tier , see [Service tiers](./concepts-pricing-tiers.md)
