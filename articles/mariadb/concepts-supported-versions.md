---
title: 'Versioni supportate: database di Azure per MariaDB'
description: Informazioni sulle versioni del server MariaDB supportate nel database di Azure per il servizio MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 7/20/2020
ms.openlocfilehash: 0b495bf21b1f78e5e6a9285f67e1769d2d2a0db1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87065690"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>Versioni server supportate in Database di Azure per MariaDB

Il database di Azure per MariaDB è stato sviluppato dal [Server MariaDB](https://downloads.mariadb.org/)open source, usando il motore InnoDB.

MariaDB usa lo schema di denominazione X. Y. Z. X è la versione principale, Y è la versione secondaria e Z è la versione della patch.

> [!NOTE]
> Nel servizio viene usato un gateway per reindirizzare le connessioni alle istanze del server. Dopo che è stata stabilita la connessione, il client MySQL visualizza la versione di MariaDB impostata nel gateway e non la versione effettiva in esecuzione nell'istanza del server MariaDB. Per determinare la versione dell'istanza del server MariaDB, usare il `SELECT VERSION();` comando.

Al momento Database di Azure per MariaDB supporta la versione seguente:

## <a name="mariadb-version-102"></a>MariaDB versione 10,2

Versione patch: 10.2.32

Per ulteriori informazioni sui miglioramenti e sulle correzioni in questa versione, vedere la [documentazione di MariaDB](https://mariadb.com/kb/en/mariadb-10232-release-notes/) .

## <a name="mariadb-version-103"></a>MariaDB versione 10,3

Versione patch: 10.3.23

Per ulteriori informazioni sui miglioramenti e sulle correzioni in questa versione, vedere la [documentazione di MariaDB](https://mariadb.com/kb/en/mariadb-10323-release-notes/) .

## <a name="managing-updates-and-upgrades"></a>Gestire gli aggiornamenti
Il servizio gestisce automaticamente gli aggiornamenti per gli aggiornamenti delle patch. Ad esempio, 10.2.21 a 10.2.23.  

Attualmente, gli aggiornamenti di versioni principali e secondarie non sono supportati. Ad esempio, l'aggiornamento da MariaDB 10.2 a MariaDB 10.3 non è supportato. Se si vuole eseguire l'aggiornamento da 10,2 a 10,3, eseguire un [dump e ripristinarlo](./howto-migrate-dump-restore.md) in un server creato con la nuova versione del motore.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulle quote specifiche di risorse e sulle limitazioni in base al **livello di servizio**, vedere la pagina relativa ai [livelli di servizio](./concepts-pricing-tiers.md).
