---
title: Versioni supportate in Database di Azure per MySQL
description: In questo articolo vengono descritte le versioni supportate nel Database di Azure per MySQL.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 03/22/2018
ms.openlocfilehash: 53c8d51ddf9b7465a99b8b0685d7f6ce177fc526
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2018
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Versioni supportate del Database di Azure per il server MySQL
Database di Azure per MySQL è stato sviluppato da [MySQL Community Edition](https://www.mysql.com/products/community/) tramite il motore InnoDB.  Al momento il Database di Azure per MySQL supporta le versioni indicate di seguito:

## <a name="mysql-version-5638"></a>MySQL versione 5.6.38
Per altre informazioni sui miglioramenti e sulle correzioni in MySQL 5.6.38, vedere la [documentazione](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-38.html) di MySQL.

## <a name="mysql-version-5720"></a>MySQL versione 5.7.20
Per informazioni sui miglioramenti e sulle correzioni in MySQL 5.7.20, vedere la [documentazione](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-20.html) di MySQL.

> [!NOTE]
> Nel servizio viene usato un gateway per reindirizzare le connessioni alle istanze del server. Dopo che è stata stabilita la connessione, il client MySQL visualizza la versione di MySQL impostata nel gateway e non la versione effettiva in esecuzione nell'istanza del server MySQL. Per determinare la versione dell'istanza del server MySQL, usare il comando `SELECT VERSION();` dal prompt di MySQL. 

## <a name="managing-updates-and-upgrades"></a>Gestire gli aggiornamenti
Il servizio gestisce automaticamente l'applicazione di patch per gli aggiornamenti delle versioni secondarie. Gli aggiornamenti delle versioni principali, ad esempio da MySQL 5.6 a MySQL 5.7, non sono supportati.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sulle quote specifiche di risorse e sulle limitazioni in base al **livello di servizio**, vedere [Livelli di servizio](./concepts-pricing-tiers.md)
