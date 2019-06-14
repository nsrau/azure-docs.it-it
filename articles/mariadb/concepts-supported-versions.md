---
title: Versioni supportate in Database di Azure per MariaDB
description: In questo articolo vengono descritte le versioni supportate in Database di Azure per MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 760cb39ea1f3980faba348c7aa1de68a66b20a8d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67065714"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>Versioni server supportate in Database di Azure per MariaDB

Database di Azure per MariaDB è stato sviluppato dal database [MariaDB Server](https://downloads.mariadb.org/) open source usando il motore InnoDB. Al momento Database di Azure per MariaDB supporta la versione seguente:

## <a name="mariadb-version-102"></a>Versione di MariaDB 10.2

Vedere le [MariaDB documentazione](https://mariadb.com/kb/en/library/mariadb-10223-release-notes/) per altre informazioni sui miglioramenti e correzioni di MariaDB 10.2.23.

## <a name="mariadb-version-103"></a>MariaDB versione 10.3

Vedere le [MariaDB documentazione](https://mariadb.com/kb/en/library/mariadb-10314-release-notes/) per altre informazioni sui miglioramenti e correzioni di MariaDB 10.3.14.

> [!NOTE]
> Nel servizio viene usato un gateway per reindirizzare le connessioni alle istanze del server. Dopo che è stata stabilita la connessione, il client MySQL visualizza la versione di MariaDB impostata nel gateway e non la versione effettiva in esecuzione nell'istanza del server MariaDB. Per determinare la versione dell'istanza del server MariaDB, usare il comando `SELECT VERSION();` dal prompt di MySQL.

## <a name="managing-updates-and-upgrades"></a>Gestire gli aggiornamenti

Il servizio gestisce automaticamente l'applicazione di patch per gli aggiornamenti delle versioni secondarie.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulle quote specifiche di risorse e sulle limitazioni in base al **livello di servizio**, vedere la pagina relativa ai [livelli di servizio](./concepts-pricing-tiers.md).
