---
title: Limiti del Database di Azure per PostgreSQL
description: Questo articolo descrive i limiti di Database di Azure per PostgreSQL, ad esempio il numero di connessioni e le opzioni del motore di archiviazione.
services: postgresql
author: kamathsun
ms.author: sukamat
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 03/20/2018
ms.openlocfilehash: 7e06cdba7c9c9f7e5c1d621e7421a18c342c0fdb
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="limitations-in-azure-database-for-postgresql"></a>Limiti del Database di Azure per PostgreSQL
Nelle sezioni seguenti vengono descritti i limiti delle capacità e funzionali nel servizio del database.

## <a name="pricing-tier-maximums"></a>Valori massimi del piano tariffario
Il Database di Azure per PostgreSQL ha più piani tariffari tra cui è possibile scegliere durante la creazione di un server. Per altre informazioni, vedere [Piani tariffari di Database di Azure per PostgreSQL](concepts-pricing-tiers.md).  

Esiste una quantità massima di connessioni, unità di calcolo e spazio di archiviazione in ogni piano tariffario, come indicato di seguito: 

|Piano tariffario| Generazione di calcolo| vCore| Numero massimo di connessioni |
|---|---|---|---|
|Basic| Generazione 4| 1| 50 |
|Basic| Generazione 4| 2| 100 |
|Basic| Generazione 5| 1| 50 |
|Basic| Generazione 5| 2| 100 |
|Utilizzo generico| Generazione 4| 2| 150|
|Utilizzo generico| Generazione 4| 4| 250|
|Utilizzo generico| Generazione 4| 8| 480|
|Utilizzo generico| Generazione 4| 16| 950|
|Utilizzo generico| Generazione 4| 32| 1500|
|Utilizzo generico| Generazione 5| 2| 150|
|Utilizzo generico| Generazione 5| 4| 250|
|Utilizzo generico| Generazione 5| 8| 480|
|Utilizzo generico| Generazione 5| 16| 950|
|Utilizzo generico| Generazione 5| 32| 1500|
|Con ottimizzazione per la memoria| Generazione 5| 2| 150|
|Con ottimizzazione per la memoria| Generazione 5| 4| 250|
|Con ottimizzazione per la memoria| Generazione 5| 8| 480|
|Con ottimizzazione per la memoria| Generazione 5| 16| 950|

Quando le connessioni superano il limite, è possibile che venga visualizzato l'errore seguente:
> FATAL: sorry, too many clients already (ERRORE IRREVERSIBILE: ci sono già troppi client)

Il sistema Azure richiede cinque connessioni per il monitoraggio del server di Database di Azure per PostgreSQL. 

## <a name="functional-limitations"></a>Limitazioni funzionali
### <a name="scale-operations"></a>Operazioni di scalabilità
1.  Non è attualmente supportata la scalabilità dinamica dei server tra i piani tariffari, ovvero il passaggio tra i piani Basic, Utilizzo generico o Con ottimizzazione per la memoria.
2.  La riduzione delle dimensioni di archiviazione del server non è attualmente supportata.

### <a name="server-version-upgrades"></a>Aggiornamenti della versione dei server
- La migrazione automatica tra le versioni del motore del database principale non è attualmente supportata.

### <a name="subscription-management"></a>Gestione sottoscrizioni
- Lo spostamento dinamico dei server tra le sottoscrizioni e i gruppi di risorse non è attualmente supportato.

### <a name="point-in-time-restore-pitr"></a>Ripristino temporizzato
1.  Quando si usa la funzionalità di ripristino temporizzato, il nuovo server viene creato con le stesse configurazioni del server su cui si basa.
2.  Il ripristino di un server eliminato non è supportato.

## <a name="next-steps"></a>Passaggi successivi
- Informazioni sulle [opzioni e prestazioni disponibili in ogni piano tariffario](concepts-pricing-tiers.md)
- Informazione sulle [versioni supportate del Database PostgreSQL](concepts-supported-versions.md)
- Vedere [Come eseguire la procedura di backup e ripristino di un server in Database di Azure per PostgreSQL usando il portale di Azure](howto-restore-server-portal.md)
