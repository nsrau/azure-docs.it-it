---
title: Addebiti di unità richiesta per Azure Cosmos DB come archivio di valori chiave
description: Informazioni sugli addebiti di unità richiesta di Azure Cosmos DB per semplici operazioni di lettura e scrittura, quando si usa Cosmos DB come archivio di chiavi/valori.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 3a79db11ff05bcc9d18619c7f508a9864c17c3b8
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012806"
---
# <a name="azure-cosmos-db-as-a-key-value-store--cost-overview"></a>Azure Cosmos DB come archivio di valori chiave: Panoramica dei costi

Azure Cosmos DB è un servizio di database multimodello distribuito a livello globale che consente di compilare con facilità applicazioni su larga scala e a disponibilità elevata. Per impostazione predefinita, Azure Cosmos DB indicizza automaticamente e in modo efficiente tutti i dati inseriti. In questo modo è possibile eseguire query [SQL](how-to-sql-query.md) (e [JavaScript](stored-procedures-triggers-udfs.md)) veloci e coerenti sui dati. 

L'articolo descrive il costo di Azure Cosmos DB per semplici operazioni di lettura e scrittura, quando usato come archivio di chiavi/valori. Le operazioni di scrittura includono inserimenti, sostituzioni, eliminazioni e Upsert di elementi di dati. Oltre a garantire un contratto di servizio con disponibilità del 99,999% per tutti gli account in più aree, Azure Cosmos DB garantisce una latenza < 10 ms per le letture e per le Scritture (indicizzate) al 99 ° percentile. 

## <a name="why-we-use-request-units-rus"></a>Perché usare le unità richiesta (UR)

Azure Cosmos DB prestazioni si basano sulla quantità di velocità effettiva con provisioning espressa in [unità richiesta](request-units.md) (UR/sec). Il provisioning è a una seconda granularità e viene acquistato in ur/s ([da non confondere con la fatturazione oraria](https://azure.microsoft.com/pricing/details/cosmos-db/)). Le UR devono essere considerate come un'astrazione logica (una valuta) che semplifica il provisioning della velocità effettiva necessaria per l'applicazione. Gli utenti non devono pensare di distinguere la velocità effettiva di lettura e scrittura. Il modello singolo di valuta delle UR crea efficienza per condividere la capacità di provisioning tra le letture e le scritture. Questo modello di capacità con provisioning consente al servizio di fornire una **velocità effettiva prevedibile e coerente, bassa latenza garantita e disponibilità elevata**. Infine, mentre il modello ur viene usato per rappresentare la velocità effettiva, ogni UR con provisioning ha anche una quantità definita di risorse, ad esempio memoria, Core/CPU e IOPS.

Come sistema di database distribuito a livello globale, Cosmos DB è l'unico servizio di Azure che offre contratti di servizio completi relativi a latenza, velocità effettiva, coerenza e disponibilità elevata. La velocità effettiva di cui viene effettuato il provisioning viene applicata a ognuna delle aree associate all'account Cosmos. Per le letture, Cosmos DB offre più [livelli di coerenza](consistency-levels.md) ben definiti tra cui scegliere. 

La tabella seguente mostra il numero di ur necessarie per eseguire operazioni di lettura e scrittura basate su un elemento di dati di dimensioni pari a 1 KB e 100 KB.

|Dimensioni dell'elemento|1 Lettura|1 Scrittura|
|-------------|------|-------|
|1 KB|1 UR|5 UR|
|100 KB|10 UR|50 UR|

## <a name="cost-of-reads-and-writes"></a>Costo di letture e scritture

Se si effettua il provisioning di 1.000 UR/sec, questo importo è pari a 3,6 milioni ur/ora e costerà $0,08 per l'ora (negli Stati Uniti e in Europa). Per un elemento di dati di dimensioni di 1 KB, questo significa che è possibile utilizzare 3,6 milioni letture o 720.000 scritture (3,6 milioni UR/5) utilizzando la velocità effettiva con provisioning. Normalizzato a milioni di letture e scritture, il costo sarebbe $0,022/milione di letture ($0,08/3,6) e $0.111/milioni di Scritture ($0,08/0,72). Il costo al milione diventa minimo come illustrato nella tabella seguente.

|Dimensioni dell'elemento|Costo di 1 milione letture|Costo di 1 milione Scritture|
|-------------|-------|--------|
|1 KB|$0.022|$0.111|
|100 KB|$0.222|$1.111|


La maggior parte dei servizi di archivi di oggetti o BLOB di base addebita 0,40 dollari per milione di transazioni di lettura e 5 dollari per milione transazioni di scrittura. Se usato in modo ottimale, Cosmos DB può essere fino al 98% più economico di queste altre soluzioni (per transazioni da 1 KB).

## <a name="next-steps"></a>Passaggi successivi

* Usare [ur Calculator](https://cosmos.azure.com/capacitycalculator/) per stimare la velocità effettiva per i carichi di lavoro.

