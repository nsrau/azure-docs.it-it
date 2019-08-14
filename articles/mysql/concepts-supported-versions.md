---
title: Versioni supportate in Database di Azure per MySQL
description: In questo articolo vengono descritte le versioni supportate nel Database di Azure per MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 08/09/2019
ms.openlocfilehash: 28d635dc5042799790d032ef4b46bf28118cb326
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947161"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Versioni supportate del Database di Azure per il server MySQL

Database di Azure per MySQL è stato sviluppato da [MySQL Community Edition](https://www.mysql.com/products/community/) tramite il motore InnoDB.

MySQL usa lo schema di denominazione X.Y.Z. X è la versione principale, Y è la versione secondaria Z è la versione di correzione di bug. Per altre informazioni sullo schema, vedere la [documentazione di MySQL](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).

> [!NOTE]
> Nel servizio viene usato un gateway per reindirizzare le connessioni alle istanze del server. Dopo che è stata stabilita la connessione, il client MySQL visualizza la versione di MySQL impostata nel gateway e non la versione effettiva in esecuzione nell'istanza del server MySQL. Per determinare la versione dell'istanza del server MySQL, usare il comando `SELECT VERSION();` dal prompt di MySQL.

Al momento il Database di Azure per MySQL supporta le versioni indicate di seguito:

## <a name="mysql-version-56"></a>MySQL versione 5.6

Versione di correzione di bug: 5.6.42

Per altre informazioni sui miglioramenti e sulle correzioni in MySQL 5.6.42, vedere le [Note sulla versione](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-42.html) di MySQL.

## <a name="mysql-version-57"></a>MySQL versione 5.7

Versione di correzione di bug: 5.7.24

Per informazioni sui miglioramenti e sulle correzioni in MySQL 5.7.24, vedere le [Note sulla versione](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-24.html) di MySQL.

## <a name="mysql-version-80"></a>MySQL versione 8,0

> [!IMPORTANT]
> MySQL 8,0 è attualmente in fase di anteprima.

Versione di correzione di bug: 8.0.15

Per informazioni sui miglioramenti e sulle correzioni in MySQL 8.0.15, vedere le [Note sulla versione](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) di MySQL.

## <a name="managing-updates-and-upgrades"></a>Gestire gli aggiornamenti
Il servizio gestisce automaticamente l'applicazione di patch per gli aggiornamenti delle versioni di correzione dei bug. Ad esempio, da 5.7.20 a 5.7.21.  

Attualmente, gli aggiornamenti di versioni principali e secondarie non sono supportati. Ad esempio l'aggiornamento da MySQL 5.6 a MySQL 5.7 non è supportato. Se si vuole eseguire l'aggiornamento dalla 5.6 alla 5.7, eseguire un [dump e ripristinarlo](./concepts-migrate-dump-restore.md) in un server creato con la nuova versione del motore.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sulle quote specifiche di risorse e sulle limitazioni in base al **livello di servizio**, vedere [Livelli di servizio](./concepts-pricing-tiers.md)
