---
title: Limiti del Database di Azure per PostgreSQL
description: Questo articolo descrive i limiti di Database di Azure per PostgreSQL, ad esempio il numero di connessioni e le opzioni del motore di archiviazione.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 06/30/2018
ms.openlocfilehash: dc1f8581df5dc7c5728094577298ba078cc2c527
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2018
ms.locfileid: "37343302"
---
# <a name="limitations-in-azure-database-for-postgresql"></a>Limiti del Database di Azure per PostgreSQL
Nelle sezioni seguenti vengono descritti i limiti delle capacità e funzionali nel servizio del database.

## <a name="maximum-connections"></a>Numero massimo di connessioni
Di seguito è indicato il numero massimo di connessioni per ogni piano tariffario e vCore: 

|**Piano tariffario**| **vCore**| **Numero massimo di connessioni** |
|---|---|---|
|Basic| 1| 50 |
|Basic| 2| 100 |
|Utilizzo generico| 2| 150|
|Utilizzo generico| 4| 250|
|Utilizzo generico| 8| 480|
|Utilizzo generico| 16| 950|
|Utilizzo generico| 32| 1500|
|Con ottimizzazione per la memoria| 2| 150|
|Con ottimizzazione per la memoria| 4| 250|
|Con ottimizzazione per la memoria| 8| 480|
|Con ottimizzazione per la memoria| 16| 950|

Quando le connessioni superano il limite, è possibile che venga visualizzato l'errore seguente:
> FATAL: sorry, too many clients already (ERRORE IRREVERSIBILE: ci sono già troppi client)

Il sistema Azure richiede cinque connessioni per il monitoraggio del server di Database di Azure per PostgreSQL. 

## <a name="functional-limitations"></a>Limitazioni funzionali
### <a name="scale-operations"></a>Operazioni di scalabilità
- Non è attualmente supportata la scalabilità dinamica tra i piani tariffari.
- La riduzione delle dimensioni di archiviazione del server non è attualmente supportata.

### <a name="server-version-upgrades"></a>Aggiornamenti della versione dei server
- La migrazione automatica tra le versioni del motore del database principale non è attualmente supportata.

### <a name="subscription-management"></a>Gestione sottoscrizioni
- Lo spostamento dinamico dei server tra le sottoscrizioni e i gruppi di risorse non è attualmente supportato.

### <a name="vnet-service-endpoints"></a>Endpoint del servizio di rete virtuale
- Gli endpoint di servizio di rete virtuale sono supportati solo per i server per utilizzo generico e ottimizzati per la memoria.

### <a name="point-in-time-restore-pitr"></a>Ripristino temporizzato
- Quando si usa la funzionalità di ripristino temporizzato, il nuovo server viene creato con le stesse configurazioni del server su cui si basa.
- Il ripristino di un server eliminato non è supportato.

## <a name="next-steps"></a>Passaggi successivi
- Informazioni sulle [opzioni e prestazioni disponibili in ogni piano tariffario](concepts-pricing-tiers.md)
- Informazione sulle [versioni supportate del Database PostgreSQL](concepts-supported-versions.md)
- Vedere [Come eseguire la procedura di backup e ripristino di un server in Database di Azure per PostgreSQL usando il portale di Azure](howto-restore-server-portal.md)
