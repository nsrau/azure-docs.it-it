---
title: Limiti del Database di Azure per MySQL | Microsoft Docs
description: Descrive i limiti dell'anteprima del Database di Azure per MySQL.
services: mysql
author: jasonh
ms.author: kamathsun
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 10/26/2017
ms.openlocfilehash: b3fba38cacf5b5abcdea7f0def8c1d39e653f0a8
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2017
---
# <a name="limitations-in-azure-database-for-mysql-preview"></a>Limiti del Database di Azure per MySQL (anteprima)
Il servizio Database di Azure per MySQL è in anteprima pubblica. Nelle sezioni seguenti vengono descritti i limiti delle capacità e funzionali nel servizio del database. Vedere anche le [limitazioni generali](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) applicabili al motore di database MySQL.

## <a name="service-tier-maximums"></a>Valori massimi del livello di servizio
Database di Azure per MySQL offre più livelli di servizio tra cui è possibile scegliere durante la creazione di un server. Per altre informazioni vedere [Opzioni e prestazioni disponibili in ogni livello di servizio](concepts-service-tiers.md).  

Esiste una quantità massima di connessioni, unità di calcolo e spazio di archiviazione in ogni livello di servizio durante l'anteprima del servizio, come indicato di seguito: 

|                            |                   |
| :------------------------- | :---------------- |
| **Numero massimo di connessioni**        |                   |
| Base con 50 unità di calcolo     | 50 connessioni    |
| Base con 100 unità di calcolo    | 100 connessioni   |
| Standard con 100 unità di calcolo | 200 connessioni   |
| Standard con 200 unità di calcolo | 400 connessioni   |
| Standard con 400 unità di calcolo | 800 connessioni   |
| Standard con 800 unità di calcolo | 1600 connessioni  |
| **Unità di calcolo massime**      |                   |
| Livello di servizio Basic         | 100 unità di calcolo |
| Livello di servizio Standard      | 800 unità di calcolo |
| **Spazio di archiviazione massimo**            |                   |
| Livello di servizio Basic         | 1 TB              |
| Livello di servizio Standard      | 1 TB              |

Quando viene raggiunto un numero eccessivo di connessioni, è possibile che si riceva l'errore seguente:
> ERROR 1040 (08004): Too many connections (ERRORE 1040 (08004): numero eccessivo di connessioni)

## <a name="preview-functional-limitations"></a>Limiti funzionali dell'anteprima

### <a name="scale-operations"></a>Operazioni di scalabilità
- La scalabilità dinamica dei server tra i livelli di servizio non è attualmente supportata, vale a dire il passaggio tra i livelli di servizio Base e Standard.
- L'aumento dinamico su richiesta dell'archiviazione sul server creato in precedenza non è attualmente supportato.
- La riduzione delle dimensioni di archiviazione del server non è supportato.

### <a name="server-version-upgrades"></a>Aggiornamenti della versione dei server
- La migrazione automatica tra le versioni del motore del database principale non è attualmente supportata.

### <a name="subscription-management"></a>Gestione sottoscrizioni
- Lo spostamento dinamico di server creati in precedenza tra le sottoscrizioni e il gruppo di risorse non è attualmente supportato.

### <a name="point-in-time-restore"></a>Ripristino temporizzato
- Il ripristino a un livello di servizio diverso e/o a dimensioni delle unità di calcolo e di archiviazione diverse non è consentito.
- Il ripristino di un server eliminato non è supportato.

## <a name="next-steps"></a>Passaggi successivi
- [Opzioni e prestazioni disponibili in ogni livello di servizio](concepts-service-tiers.md)
- [Versioni supportate del database MySQL](concepts-supported-versions.md)
