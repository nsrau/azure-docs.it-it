---
title: Versioni supportate in Database di Azure per MySQL
description: In questo articolo vengono descritte le versioni supportate nel Database di Azure per MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 10/10/2018
ms.openlocfilehash: 720644519eb0031f9f2837cc8321a0def39c37a6
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2018
ms.locfileid: "53545706"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Versioni supportate del Database di Azure per il server MySQL
Database di Azure per MySQL è stato sviluppato da [MySQL Community Edition](https://www.mysql.com/products/community/) tramite il motore InnoDB. Al momento il Database di Azure per MySQL supporta le versioni indicate di seguito:

## <a name="mysql-version-5639"></a>MySQL versione 5.6.39
Per altre informazioni sui miglioramenti e sulle correzioni in MySQL 5.6.39, vedere la [documentazione](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-39.html) di MySQL.

## <a name="mysql-version-5721"></a>MySQL versione 5.7.21
Per informazioni sui miglioramenti e sulle correzioni in MySQL 5.7.21, vedere la [documentazione](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-21.html) di MySQL.

> [!NOTE]
> Nel servizio viene usato un gateway per reindirizzare le connessioni alle istanze del server. Dopo che è stata stabilita la connessione, il client MySQL visualizza la versione di MySQL impostata nel gateway e non la versione effettiva in esecuzione nell'istanza del server MySQL. Per determinare la versione dell'istanza del server MySQL, usare il comando `SELECT VERSION();` dal prompt di MySQL.

## <a name="managing-updates-and-upgrades"></a>Gestire gli aggiornamenti
Il servizio gestisce automaticamente l'applicazione di patch per gli aggiornamenti delle versioni di correzione dei bug. L'aggiornamento di versioni secondarie non è attualmente supportato. Ad esempio, l'aggiornamento da MySQL 5.6 a MySQL 5.7 non è supportato. Se si vuole eseguire l'aggiornamento alla versione secondaria successiva, eseguire un [dump e ripristinarlo](./concepts-migrate-dump-restore.md) in un server creato con la nuova versione del motore.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sulle quote specifiche di risorse e sulle limitazioni in base al **livello di servizio**, vedere [Livelli di servizio](./concepts-pricing-tiers.md)
