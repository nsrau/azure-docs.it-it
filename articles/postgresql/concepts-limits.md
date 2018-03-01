---
title: Limiti del Database di Azure per PostgreSQL | Microsoft Docs
description: Descrive i limiti del Database di Azure per PostgreSQL.
services: postgresql
author: kamathsun
ms.author: sukamat
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.custom: mvc
ms.topic: article
ms.date: 12/04/2017
ms.openlocfilehash: 6dbed1a834d74047178a9f996683d65520047e66
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2018
---
# <a name="limitations-in-azure-database-for-postgresql"></a>Limiti del Database di Azure per PostgreSQL
Il servizio Database di Azure per PostgreSQL è in anteprima pubblica. Nelle sezioni seguenti vengono descritti i limiti delle capacità e funzionali nel servizio del database.

## <a name="service-tier-maximums"></a>Valori massimi del livello di servizio
Il Database di Azure per PostgreSQL dispone di più livelli di servizio tra cui è possibile scegliere durante la creazione di un server. Per altre informazioni vedere [Opzioni e prestazioni disponibili in ogni livello di servizio](concepts-service-tiers.md).  

Esiste un numero massimo di connessioni, unità di calcolo e archiviazione in ogni livello di servizio durante l'anteprima del servizio, come indicato di seguito: 

| | |
| :------------------------- | :---------------- |
| **Numero massimo di connessioni**        |                   |
| Base con 50 unità di calcolo     | 55 connessioni    |
| Base con 100 unità di calcolo    | 105 connessioni   |
| Standard con 100 unità di calcolo | 150 connessioni   |
| Standard con 200 unità di calcolo | 250 connessioni   |
| Standard con 400 unità di calcolo | 480 connessioni   |
| Standard con 800 unità di calcolo | 950 connessioni   |
| **Unità di calcolo massime**      |                   |
| Livello di servizio Basic         | 100 unità di calcolo |
| Livello di servizio Standard      | 800 unità di calcolo |
| **Spazio di archiviazione massimo**            |                   |
| Livello di servizio Basic         | 1 TB              |
| Livello di servizio Standard      | 1 TB              |

Il sistema Azure richiede cinque connessioni per il monitoraggio del server di Database di Azure per PostgreSQL. Quando viene raggiunto un numero eccessivo di connessioni, è possibile che si riceva l'errore seguente:
> FATAL: sorry, too many clients already (ERRORE IRREVERSIBILE: ci sono già troppi client)


## <a name="preview-functional-limitations"></a>Limiti funzionali dell'anteprima
### <a name="scale-operations"></a>Operazioni di scalabilità
1.  La scalabilità dinamica dei server tra i livelli di servizio non è attualmente supportata, vale a dire il passaggio tra i livelli di servizio Base e Standard.
2.  L'aumento dinamico su richiesta dell'archiviazione sul server creato in precedenza non è attualmente supportato.
3.  La riduzione delle dimensioni di archiviazione del server non è supportato.

### <a name="server-version-upgrades"></a>Aggiornamenti della versione dei server
- La migrazione automatica tra le versioni del motore del database principale non è attualmente supportata.

### <a name="subscription-management"></a>Gestione sottoscrizioni
- Lo spostamento dinamico di server creati in precedenza tra le sottoscrizioni e il gruppo di risorse non è attualmente supportato.

### <a name="point-in-time-restore"></a>Ripristino temporizzato
1.  Il ripristino a un livello di servizio diverso e/o a dimensioni delle unità di calcolo e di archiviazione diverse non è consentito.
2.  Il ripristino di un server eliminato non è supportato.

## <a name="next-steps"></a>Passaggi successivi
- Informazioni sulle [opzioni e prestazioni disponibili in ogni piano tariffario](concepts-service-tiers.md)
- Informazione sulle [Supported PostgreSQL Database Versions](concepts-supported-versions.md) (Versioni supportate del Database PostgreSQL)
- Rivedere [How To Back up and Restore a server in Azure Database for PostgreSQL using the Azure portal](howto-restore-server-portal.md) (Come eseguire il backup e il ripristino di un server nel Database di Azure per PostgreSQL usando il portale di Azure)
