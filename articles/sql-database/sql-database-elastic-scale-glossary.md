---
title: Glossario sugli strumenti di database elastici | Documentazione Microsoft
description: Spiegazione dei termini utilizzati per gli strumenti dei database elastici
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 446203b45744a95c32cd41d9ded26fd960ac8a22
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2019
ms.locfileid: "55561434"
---
# <a name="elastic-database-tools-glossary"></a>Glossario sugli strumenti di database elastici

Di seguito sono elencate le definizioni dei termini usati per gli [strumenti di database elastici](sql-database-elastic-scale-introduction.md), una funzionalità del database SQL di Azure. Gli strumenti vengono usati per gestire le [mappe partizioni](sql-database-elastic-scale-shard-map-management.md) e includono la [libreria client](sql-database-elastic-database-client-library.md), lo [strumento di suddivisione-unione](sql-database-elastic-scale-overview-split-and-merge.md), i [pool elastici](sql-database-elastic-pool.md) e le [query](sql-database-elastic-query-overview.md). 

Questi termini vengono usati in [Aggiunta di una partizione utilizzando gli strumenti di database elastici](sql-database-elastic-scale-add-a-shard.md) e [Uso della classe RecoveryManager per correggere i problemi delle mappe partizioni](sql-database-elastic-database-recovery-manager.md).

![Termini della scalabilità elastica][1]

**Database**: un database SQL di Azure. 

**Routing dipendente dai dati**: La funzionalità che consente a un'applicazione di connettersi a una partizione in base a una specifica chiave di partizionamento. Vedere [Routing dipendente dei dati](sql-database-elastic-scale-data-dependent-routing.md). Confrontare con **[Multi-Shard Query](sql-database-elastic-scale-multishard-querying.md)**.

**Mappa globale partizioni**: Il mapping tra le chiavi di partizionamento e le rispettive partizioni all'interno di un **set di partizioni**. La mappa partizioni globale viene archiviata nel **gestore mappe partizioni**. Confrontare con la **mappa partizioni locale**.

**Mappa partizioni di tipo elenco**: Una mappa partizioni in cui le chiavi di partizionamento vengono mappate singolarmente. Confrontare con la **mappa partizioni di tipo intervallo**.   

**Mappa locale partizioni**: Archiviata in una partizione, la mappa partizioni locale contiene i mapping per gli shardlet che risiedono nella partizione.

**Esecuzione di query su più partizioni**: La possibilità di eseguire una query su più partizioni; i set di risultati vengono restituiti usando la semantica di UNION ALL (nota anche come "query di tipo fan-out"). Confrontare con **Routing dipendente dai dati**.

**Multi-tenant** e **Tenant singolo**: L'immagine mostra un database a tenant singolo e un database multi-tenant:

![Database a tenant singolo e multi-tenant](./media/sql-database-elastic-scale-glossary/multi-single-simple.png)

Ecco una rappresentazione di database a tenant singolo e multi-tenant **partizionati** . 

![Database a tenant singolo e multi-tenant](./media/sql-database-elastic-scale-glossary/shards-single-multi.png)

**Mappa partizioni di tipo intervallo**: Una mappa partizioni in cui la strategia di distribuzione delle partizioni è basata su più intervalli di valori contigui. 

**Tabelle di riferimento**: Tabelle che non vengono partizionate, ma vengono replicate tra le partizioni. I codici di avviamento postale, ad esempio, possono essere archiviati in una tabella di riferimento. 

**Partizione**: Un database SQL di Azure che archivia i dati da un set di dati partizionato. 

**Elasticità di partizionamento**: La possibilità di eseguire il **ridimensionamento orizzontale** e il **ridimensionamento verticale**.

**Tabelle partizionate**: Tabelle che vengono partizionate, ovvero i cui dati vengono distribuiti tra le partizioni in base ai valori della chiave di partizionamento. 

**Chiave di partizionamento**: Un valore di colonna che determina la modalità di distribuzione dei dati tra le partizioni. Il tipo valore può essere uno dei seguenti: **int**, **bigint**, **varbinary** o **uniqueidentifier**. 

**Set di partizioni**: La raccolta di partizioni attribuite alla stessa mappa partizioni nel gestore delle mappe partizioni.  

**Shardlet**: Il complesso dei dati associati a un singolo valore di una chiave di partizionamento in una partizione. Uno shardlet è la più piccola unità di spostamento dei dati possibile quando si ridistribuiscono tabelle partizionate. 

**Mappa partizioni**: Il set di mapping tra le chiavi di partizionamento e le rispettive partizioni.

**Gestore mappe partizioni**: Un archivio di dati e oggetti di gestione che contiene le mappe partizioni, i percorsi delle partizioni e i mapping per uno o più set di partizioni.

![Mapping][2]

## <a name="verbs"></a>Verbi
**Scalabilità orizzontale**: L'azione di aumentare o ridurre le dimensioni di una raccolta di partizioni aggiungendo o rimuovendo partizioni a una mappa partizioni.

![Scalabilità orizzontale e verticale][3]

**Unione**: L'azione di spostare shardlet da due partizioni a una partizione e aggiornare la mappa partizioni di conseguenza.

**Spostamento di shardlet** : L'azione di spostare un singolo shardlet in una partizione diversa. 

**Partizione**: L'azione di eseguire il partizionamento orizzontale di dati strutturati in modo identico in più database in base a una chiave di partizionamento.

**Divisione**: L'azione di spostare più shardlet da una partizione a un'altra (in genere una nuova partizione). Come punto di divisione viene usata una chiave di partizionamento orizzontale fornita dall'utente.

**Scalabilità verticale**: L'azione di aumentare o ridurre la dimensione di calcolo di una singola partizione. Ad esempio, modifica di una partizione da Standard a Premium (che comporta più risorse di elaborazione). 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png
[3]: ./media/sql-database-elastic-scale-glossary/h_versus_vert.png

