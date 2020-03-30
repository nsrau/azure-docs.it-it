---
title: Addebiti di unità richiesta per Azure Cosmos DB come archivio di valori chiave
description: Informazioni sugli addebiti di unità richiesta di Azure Cosmos DB per semplici operazioni di lettura e scrittura, quando si usa Cosmos DB come archivio di chiavi/valori.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 5b2ee8b5bf19f16d7f7f04e9515fe591db7132f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77647512"
---
# <a name="azure-cosmos-db-as-a-key-value-store--cost-overview"></a>Azure Cosmos DB come archivio di valori chiave: panoramica dei costiAzure Cosmos DB as a key value store – cost overview

Azure Cosmos DB è un servizio di database multimodello distribuito a livello globale che consente di compilare con facilità applicazioni su larga scala e a disponibilità elevata. Per impostazione predefinita, Azure Cosmos DB indicizza automaticamente ed efficientemente tutti i dati inseriti. In questo modo vengono eseguite query [SQL](how-to-sql-query.md) (e [JavaScript)](stored-procedures-triggers-udfs.md)veloci e coerenti sui dati. 

L'articolo descrive il costo di Azure Cosmos DB per semplici operazioni di lettura e scrittura, quando usato come archivio di chiavi/valori. Le operazioni di scrittura includono inserimenti, sostituti, eliminazioni e backup di elementi di dati. Oltre a garantire un SLA di disponibilità del 99,999% per tutti gli account con più aree, Azure Cosmos DB offre una latenza garantita <10 ms per le letture e per le scritture (indicizzate), al 99esimo percentile. 

## <a name="why-we-use-request-units-rus"></a>Perché usare le unità richiesta (UR)

Le prestazioni di Azure Cosmos DB si basano sulla quantità di velocità effettiva di cui è stato eseguito il provisioning in [Request Units](request-units.md) (RU/s). Il provisioning ha una seconda granularità e viene acquistato in RU/s[(da non confondere con la fatturazione oraria).](https://azure.microsoft.com/pricing/details/cosmos-db/) Le RU devono essere considerate come un'astrazione logica (una valuta) che semplifica il provisioning della velocità effettiva richiesta per l'applicazione. Gli utenti non devono pensare di differenziare la velocità effettiva di lettura e scrittura. Il modello singolo di valuta delle UR crea efficienza per condividere la capacità di provisioning tra le letture e le scritture. Questo modello di capacità di cui è stato eseguito il provisioning consente al servizio di fornire una velocità effettiva prevedibile e coerente, una bassa **latenza garantita e un'elevata disponibilità.** Infine, mentre il modello RU viene usato per rappresentare la velocità effettiva, ogni RU di cui è stato eseguito il provisioning ha anche una quantità definita di risorse (ad esempio, memoria, core/CPU e IOPS).

Come sistema di database distribuito a livello globale, Cosmos DB è l'unico servizio di Azure che fornisce contratti di servizio completi che coprono latenza, velocità effettiva, coerenza e disponibilità elevata. La velocità effettiva di cui si esegue il provisioning viene applicata a ciascuna delle aree associate all'account Cosmos. Per le letture, Cosmos DB offre più [livelli di coerenza](consistency-levels.md) ben definiti tra cui scegliere. 

Nella tabella seguente viene illustrato il numero di RU necessarie per eseguire operazioni di lettura e scrittura basate su un elemento di dati di dimensioni 1 KB e 100 KB con l'indicizzazione automatica predefinita disattivata. 

|Dimensioni dell'elemento|1 Lettura|1 Scrittura|
|-------------|------|-------|
|1 KB|1 UR|5 UR|
|100 kB|10 UR|50 UR|

## <a name="cost-of-reads-and-writes"></a>Costo di letture e scritture

Se si effettuano il provisioning di 1.000 RU/s, questo ammonta a 3,6 milioni di RU all'ora e avrà un costo di 0,08 USD per l'ora (negli Stati Uniti e in Europa). Per un elemento di dati di dimensioni 1 KB, ciò significa che è possibile utilizzare 3,6 milioni di letture o 0,72 milioni di scritture (3,6 milioni di RU / 5) usando la velocità effettiva di cui è stato eseguito il provisioning. Normalizzato in milioni di letture e scritture, il costo sarebbe di 0,022 USD /milioni di letture (0,08 USD / 3,6) e 0,111 USD/milioni di scritture (0,08 USD / 0,72). Il costo al milione diventa minimo come illustrato nella tabella seguente.

|Dimensioni dell'elemento|Costo di 1 milione di letture|Costo di 1 milione di scritture|
|-------------|-------|--------|
|1 KB|$0.022|$0.111|
|100 kB|$0.222|$1.111|


La maggior parte dei servizi di archivi di oggetti o BLOB di base addebita 0,40 dollari per milione di transazioni di lettura e 5 dollari per milione transazioni di scrittura. Se utilizzato in modo ottimale, Cosmos DB può essere fino al 98% più economico rispetto a queste altre soluzioni (per transazioni da 1 KB).

## <a name="next-steps"></a>Passaggi successivi

* Usare il [calcolatore RU](https://cosmos.azure.com/capacitycalculator/) per stimare la velocità effettiva per i carichi di lavoro.

