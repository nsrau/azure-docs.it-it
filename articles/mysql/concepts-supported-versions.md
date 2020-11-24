---
title: 'Versioni supportate: database di Azure per MySQL'
description: Informazioni sulle versioni di MySQL Server supportate nel database di Azure per il servizio MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 6/3/2020
ms.openlocfilehash: 8033117d9e3c31f8aa9bba06afb7c3b1b7bba67f
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95751029"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Versioni supportate del Database di Azure per il server MySQL

Database di Azure per MySQL è stato sviluppato da [MySQL Community Edition](https://www.mysql.com/products/community/) tramite il motore InnoDB.

MySQL usa lo schema di denominazione X.Y.Z. X è la versione principale, Y è la versione secondaria Z è la versione di correzione di bug. Per altre informazioni sullo schema, vedere la [documentazione di MySQL](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).


> [!NOTE]
> Nel servizio viene usato un gateway per reindirizzare le connessioni alle istanze del server. Dopo che è stata stabilita la connessione, il client MySQL visualizza la versione di MySQL impostata nel gateway e non la versione effettiva in esecuzione nell'istanza del server MySQL. Per determinare la versione dell'istanza del server MySQL, usare il comando `SELECT VERSION();` dal prompt di MySQL.

Al momento il Database di Azure per MySQL supporta le versioni indicate di seguito:

## <a name="mysql-version-56"></a>MySQL versione 5.6

Versione correzione bug: 5.6.47

Per ulteriori informazioni sui miglioramenti e sulle correzioni in questa versione, vedere le [Note sulla versione](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html) di MySQL.

## <a name="mysql-version-57"></a>MySQL versione 5.7

Versione correzione bug: 5.7.29

Per ulteriori informazioni sui miglioramenti e sulle correzioni in questa versione, vedere le [Note sulla versione](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) di MySQL.

## <a name="mysql-version-80"></a>MySQL versione 8,0

Versione correzione bug: 8.0.15

Per ulteriori informazioni sui miglioramenti e sulle correzioni in questa versione, vedere le [Note sulla versione](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) di MySQL.

## <a name="managing-updates-and-upgrades"></a>Gestire gli aggiornamenti
Il servizio gestisce automaticamente l'applicazione di patch per gli aggiornamenti delle versioni di correzione dei bug. Ad esempio, da 5.7.20 a 5.7.21.  

L'aggiornamento della versione principale è attualmente supportato dal servizio per gli aggiornamenti da MySQL v 5.6 a v 5.7. Per ulteriori informazioni, vedere [come eseguire gli aggiornamenti della versione principale](how-to-major-version-upgrade.md). Se si vuole eseguire l'aggiornamento da 5,7 a 8,0, è consigliabile eseguire il [dump e il ripristino](./concepts-migrate-dump-restore.md) in un server creato con la nuova versione del motore.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni dettagliate sui criteri di controllo delle versioni di database di Azure per MySQL, vedere [questo documento](concepts-version-policy.md).
- Per informazioni sulle quote di risorse e sulle limitazioni specifiche in base al **livello di servizio**, vedere livelli di [servizio](./concepts-pricing-tiers.md)
