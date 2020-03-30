---
title: Versioni supportate - Database di Azure per MariaDBSupported versions - Azure Database for MariaDB
description: Informazioni sulle versioni del server MariaDB supportate nel servizio Database di Azure per MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 361ba17532d27a7020be1b6874993da999f48604
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527708"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>Versioni server supportate in Database di Azure per MariaDB

Il database di Azure per MariaDB è stato sviluppato dal server [MariaDB](https://downloads.mariadb.org/)open-source , utilizzando il motore InnoDB.

MariaDB utilizza lo schema di denominazione X.Y. X è la versione principale, Y è la versione secondaria, e s è la versione della patch.

> [!NOTE]
> Nel servizio viene usato un gateway per reindirizzare le connessioni alle istanze del server. Dopo che è stata stabilita la connessione, il client MySQL visualizza la versione di MariaDB impostata nel gateway e non la versione effettiva in esecuzione nell'istanza del server MariaDB. Per determinare la versione dell'istanza del `SELECT VERSION();` server MariaDB, utilizzare il comando .

Al momento Database di Azure per MariaDB supporta la versione seguente:

## <a name="mariadb-version-102"></a>MariaDB Versione 10.2

Versione patch: 10.2.25

Fare riferimento alla documentazione di [MariaDB](https://mariadb.com/kb/en/library/mariadb-10225-release-notes/) per ulteriori informazioni sui miglioramenti e le correzioni in questa versione.

## <a name="mariadb-version-103"></a>MariaDB Versione 10.3

Versione patch: 10.3.16

Fare riferimento alla documentazione di [MariaDB](https://mariadb.com/kb/en/library/mariadb-10316-release-notes/) per ulteriori informazioni sui miglioramenti e le correzioni in questa versione.

## <a name="managing-updates-and-upgrades"></a>Gestire gli aggiornamenti
Il servizio gestisce automaticamente gli aggiornamenti per gli aggiornamenti delle patch. Ad esempio, da 10.2.21 a 10.2.23.  

Attualmente, gli aggiornamenti di versioni principali e secondarie non sono supportati. Ad esempio, l'aggiornamento da MariaDB 10.2 a MariaDB 10.3 non è supportato. Se si desidera eseguire l'aggiornamento da 10.2 a 10.3, eseguire un [dump e ripristinarlo](./howto-migrate-dump-restore.md) in un server creato con la nuova versione del motore.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulle quote specifiche di risorse e sulle limitazioni in base al **livello di servizio**, vedere la pagina relativa ai [livelli di servizio](./concepts-pricing-tiers.md).
