---
title: Limiti di Database di Azure per PostgreSQL - Server singolo
description: Questo articolo descrive le limitazioni nel Database di Azure per PostgreSQL - Server singolo, ad esempio numero di connessioni e opzioni di archiviazione del motore.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/05/2019
ms.openlocfilehash: 3e1597def26c09378d3917ad2d49163ef17732f8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66732870"
---
# <a name="limitations-in-azure-database-for-postgresql---single-server"></a>Limiti di Database di Azure per PostgreSQL - Server singolo
Nelle sezioni seguenti vengono descritti i limiti delle capacità e funzionali nel servizio del database. Se vuoi informazioni sui livelli di risorse (calcolo, memoria, archiviazione), vedere la [sui piani tariffari](concepts-pricing-tiers.md) articolo.


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
|Utilizzo generico| 64| 1900|
|Con ottimizzazione per la memoria| 2| 300|
|Con ottimizzazione per la memoria| 4| 500|
|Con ottimizzazione per la memoria| 8| 960|
|Con ottimizzazione per la memoria| 16| 1900|
|Con ottimizzazione per la memoria| 32| 1900|

Quando le connessioni superano il limite, è possibile che venga visualizzato l'errore seguente:
> FATAL: sorry, too many clients already (ERRORE IRREVERSIBILE: ci sono già troppi client)

Il sistema Azure richiede cinque connessioni per il monitoraggio del server di Database di Azure per PostgreSQL. 

## <a name="functional-limitations"></a>Limitazioni funzionali
### <a name="scale-operations"></a>Operazioni di scalabilità
- Non è attualmente supportata la scalabilità dinamica tra i piani tariffari.
- La riduzione delle dimensioni di archiviazione del server non è attualmente supportata.

### <a name="server-version-upgrades"></a>Aggiornamenti della versione dei server
- La migrazione automatica tra le versioni del motore del database principale non è attualmente supportata. Se si vuole eseguire l'aggiornamento alla versione principale successiva, eseguire un [dump e ripristinarlo](./howto-migrate-using-dump-and-restore.md) in un server creato con la nuova versione del motore.

### <a name="vnet-service-endpoints"></a>Endpoint del servizio di rete virtuale
- Gli endpoint di servizio di rete virtuale sono supportati solo per i server per utilizzo generico e ottimizzati per la memoria.

### <a name="restoring-a-server"></a>Rispristino di un server
- Quando si usa la funzionalità di recupero temporizzato, il nuovo server viene creato con le stesse configurazioni relative al piano tariffario del server su cui si basa.
- Il nuovo server creato durante un ripristino non dispone delle regole del firewall presenti nel server originale. Per questo nuovo server, le regole del firewall devono essere impostate separatamente.
- Il ripristino di un server eliminato non è supportato.

### <a name="utf-8-characters-on-windows"></a>Caratteri UTF-8 in Windows
- In alcuni scenari, i caratteri UTF-8 non sono completamente supportati in PostgreSQL open source per Windows, con ripercussioni su Database di Azure per PostgreSQL. Per ulteriori informazioni, vedere il thread nell'articolo [Bug #15476 in the postgresql-archive](https://www.postgresql-archive.org/BUG-15476-Problem-on-show-trgm-with-4-byte-UTF-8-characters-td6056677.html) (Bug n. 15476 in postgresql-archive).

## <a name="next-steps"></a>Passaggi successivi
- Informazioni sulle [opzioni e prestazioni disponibili in ogni piano tariffario](concepts-pricing-tiers.md)
- Informazione sulle [versioni supportate del Database PostgreSQL](concepts-supported-versions.md)
- Vedere [Come eseguire la procedura di backup e ripristino di un server in Database di Azure per PostgreSQL usando il portale di Azure](howto-restore-server-portal.md)
