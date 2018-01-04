---
title: 'Azure Cosmos DB come archivio di valori chiave: Panoramica dei costi | Microsoft Docs'
description: Informazioni sui costi ridotti correlati all'uso di Azure Cosmos DB come archivio di valori chiave.
keywords: archivio di valori chiave
services: cosmos-db
author: mimig1
manager: jhubbard
editor: 
tags: 
documentationcenter: 
ms.assetid: 7f765c17-8549-4509-9475-46394fc3a218
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: mimig
ms.openlocfilehash: e6f87cd82ebe31965fcaac1f66d2df03fd58294f
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2017
---
# <a name="azure-cosmos-db-as-a-key-value-store--cost-overview"></a>Azure Cosmos DB come archivio di valori chiave: Panoramica dei costi

Azure Cosmos DB è un servizio di database multimodello distribuito a livello globale che consente di compilare con facilità applicazioni su larga scala e a disponibilità elevata. Per impostazione predefinita, Azure Cosmos DB indicizza automaticamente tutti i dati che inserisce, in modo efficiente. In questo modo è possibile abilitare query [SQL](sql-api-sql-query.md) (e [JavaScript](programming.md)) rapide e coerenti su qualsiasi tipo di dati. 

L'articolo descrive il costo di Azure Cosmos DB per semplici operazioni di lettura e scrittura, quando usato come archivio di chiavi/valori. Le operazioni di scrittura includono inserimenti, sostituzioni, eliminazioni e upsert di documenti. Oltre a offrire un contratto di servizio con disponibilità del 99,99% per tutti gli account in una singola area e tutti gli account in più aree con coerenza media e con disponibilità in lettura del 99,999% per tutti gli account di database in più aree, Azure Cosmos DB offre latenza minore di 10 ms per le letture e di 15 ms per le scritture al 99° percentile. 

## <a name="why-we-use-request-units-rus"></a>Perché usare le unità richiesta (UR)

Le prestazioni di Azure Cosmos DB si basano sulla quantità di [unità richiesta](request-units.md) (UR) con provisioning per la partizione. Il provisioning è a una granularità in secondi e viene acquistato in UR/sec ([da non confondere con la fatturazione oraria](https://azure.microsoft.com/pricing/details/cosmos-db/)). Le UR devono essere considerate come una valuta che semplifica il provisioning della velocità effettiva necessaria per l'applicazione. I clienti non devono pensare a differenziare le unità di capacità tra lettura e scrittura. Il modello singolo di valuta delle UR crea efficienza per condividere la capacità di provisioning tra le letture e le scritture. Questo modello di capacità con provisioning consente al servizio fornire una velocità effettiva prevedibile e coerente, con bassa latenza e disponibilità elevata garantite. Infine, si usano le UR per generare un modello di velocità effettiva, ma ogni UR con provisioning presenta inoltre una quantità di risorse definita (memoria, memoria centrale). UR/sec non si riferisce solo alle operazioni di I/O al secondo (IOPS).

Come sistema di database distribuito a livello globale, Cosmos DB è il solo servizio di Azure che fornisce un contratto di servizio basato su latenza, velocità effettiva e coerenza oltre che sulla disponibilità elevata. La velocità effettiva con cui viene effettuato il provisioning viene applicata a ognuna delle aree associate all'account di database Cosmos DB. Per le letture, Cosmos DB offre più [livelli di coerenza](consistency-levels.md) ben definiti tra cui scegliere. 

La tabella seguente mostra il numero di UR necessarie per eseguire transazioni di lettura e scrittura con dimensioni di documento pari a 1 KB e 100 KB.

|Dimensioni dell'elemento|1 Lettura|1 Scrittura|
|-------------|------|-------|
|1 KB|1 UR|5 UR|
|100 KB|10 UR|50 UR|

## <a name="cost-of-reads-and-writes"></a>Costo di letture e scritture

Se si esegue il provisioning di 1.000 UR al secondo, si avrà un totale di 3,6 milioni di UR all'ora e il costo sarà di 0,08 dollari l'ora (negli Stati Uniti e in Europa). Per un documento di 1 KB, ciò significa che è possibile utilizzare 3,6 milioni di letture o 0,72 milioni di scritture (3,6 m UR/5) utilizzando la velocità effettiva con provisioning. Normalizzate in milioni di letture e scritture, il costo sarebbe di 0,022 dollari/milione di letture (0,08 dollari/3,6) e 0,111 dollari/milioni di scritture (0,08 dollari/0,72). Il costo al milione diventa minimo come illustrato nella tabella seguente.

|Dimensioni dell'elemento|1-m lettura|1-m scrittura|
|-------------|-------|--------|
|1 KB|$0.022|$0.111|
|100 KB|$0.222|$1.111|


La maggior parte dei servizi di archivi di oggetti o BLOB di base addebita 0,40 dollari per milione di transazioni di lettura e 5 dollari per milione transazioni di scrittura. Se usato in modo ottimale, Cosmos DB è fino al 98% più conveniente di queste altre soluzioni, per le transazioni di 1 KB.

## <a name="next-steps"></a>Passaggi successivi

Saranno presto disponibili nuovi articoli sull'ottimizzazione del provisioning delle risorse di Azure Cosmos DB. Nel frattempo, è possibile usare il nostro [calcolatore UR](https://www.documentdb.com/capacityplanner).

