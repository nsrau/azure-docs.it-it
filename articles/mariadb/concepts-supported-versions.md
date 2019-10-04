---
title: Versioni supportate in Database di Azure per MariaDB
description: In questo articolo vengono descritte le versioni supportate in Database di Azure per MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: e5d1dbc8c212d4cdefb12fb740a454324d3adfa1
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70962931"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>Versioni server supportate in Database di Azure per MariaDB

Il database di Azure per MariaDB è stato sviluppato dal [Server MariaDB](https://downloads.mariadb.org/)open source, usando il motore InnoDB. 

MariaDB usa lo schema di denominazione X. Y. Z. X è la versione principale, Y è la versione secondaria e Z è la versione della patch.

> [!NOTE]
> Nel servizio viene usato un gateway per reindirizzare le connessioni alle istanze del server. Dopo che è stata stabilita la connessione, il client MySQL visualizza la versione di MariaDB impostata nel gateway e non la versione effettiva in esecuzione nell'istanza del server MariaDB. Per determinare la versione dell'istanza del server MariaDB, usare il `SELECT VERSION();` comando.

Al momento Database di Azure per MariaDB supporta la versione seguente:

## <a name="mariadb-version-102"></a>MariaDB versione 10,2

Versione patch: 10.2.25

Per ulteriori informazioni sui miglioramenti e sulle correzioni in questa versione, vedere la [documentazione di MariaDB](https://mariadb.com/kb/en/library/mariadb-10225-release-notes/) .

## <a name="mariadb-version-103"></a>MariaDB versione 10,3

Versione patch: 10.3.16

Per ulteriori informazioni sui miglioramenti e sulle correzioni in questa versione, vedere la [documentazione di MariaDB](https://mariadb.com/kb/en/library/mariadb-10316-release-notes/) .

## <a name="managing-updates-and-upgrades"></a>Gestire gli aggiornamenti
Il servizio gestisce automaticamente gli aggiornamenti per gli aggiornamenti delle patch. Ad esempio, 10.2.21 a 10.2.23.  

Attualmente, gli aggiornamenti di versioni principali e secondarie non sono supportati. Ad esempio, l'aggiornamento da MariaDB 10,2 a MariaDB 10,3 non è supportato. Se si vuole eseguire l'aggiornamento da 10,2 a 10,3, eseguire un [dump e ripristinarlo](./howto-migrate-dump-restore.md) in un server creato con la nuova versione del motore.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulle quote specifiche di risorse e sulle limitazioni in base al **livello di servizio**, vedere la pagina relativa ai [livelli di servizio](./concepts-pricing-tiers.md).
