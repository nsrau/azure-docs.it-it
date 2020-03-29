---
title: Partizionamento in Azure Cosmos DB
description: Informazioni sul partizionamento in Azure Cosmos DB, sulle procedure consigliate per la scelta di una chiave di partizione e su come gestire le partizioni logicheLearn about partitioning in Azure Cosmos DB, best practices when choosing a partition key, and how to manage logical partitions
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 551703b5dcca082904197010366ee059998dde4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251869"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Partizionamento in Azure Cosmos DB

Azure Cosmos DB usa il partizionamento per ridimensionare i singoli contenitori in un database per soddisfare le esigenze di prestazioni dell'applicazione. Nel partizionamento, gli elementi in un contenitore sono suddivisi in sottoinsiemi distinti *denominati partizioni logiche*. Le partizioni logiche sono formate in base al valore di una chiave di *partizione* associata a ogni elemento in un contenitore. Tutti gli elementi in una partizione logica hanno lo stesso valore di chiave di partizione.

Ad esempio, un contenitore contiene elementi. Ogni elemento ha un `UserID` valore univoco per la proprietà. Se `UserID` funge da chiave di partizione per gli elementi nel `UserID` contenitore e sono presenti 1.000 valori univoci, vengono create 1.000 partizioni logiche per il contenitore.

Oltre a una chiave di partizione che determina la partizione logica dell'elemento, ogni elemento in un contenitore ha un *ID elemento* (univoco all'interno di una partizione logica). La combinazione della chiave di partizione e dell'ID elemento crea *l'indice*dell'elemento, che identifica in modo univoco l'elemento.

[La scelta di una chiave](partitioning-overview.md#choose-partitionkey) di partizione è una decisione importante che influirà sulle prestazioni dell'applicazione.

## <a name="managing-logical-partitions"></a>Gestione delle partizioni logiche

Azure Cosmos DB gestisce in modo trasparente e automatico il posizionamento delle partizioni logiche nelle partizioni fisiche per soddisfare in modo efficiente le esigenze di scalabilità e prestazioni del contenitore. Con l'aumentare della velocità effettiva e dei requisiti di archiviazione di un'applicazione, Azure Cosmos DB sposta le partizioni logiche per distribuire automaticamente il carico in un numero maggiore di server. 

Azure Cosmos DB usa il partizionamento basato su hash per distribuire le partizioni logiche tra le partizioni fisiche. Azure Cosmos DB analizza il valore della chiave di partizione di un elemento. Il risultato hash determina la partizione fisica. Quindi, Azure Cosmos DB alloca lo spazio chiave degli eventi delle chiavi di partizione in modo uniforme tra le partizioni fisiche.

Le query che accedono ai dati all'interno di una singola partizione logica sono più convenienti rispetto alle query che accedono a più partizioni. Le transazioni (in stored procedure o trigger) sono consentite solo per gli elementi in una singola partizione logica.

Per altre informazioni su come Azure Cosmos DB gestisce le partizioni, vedere [Partizioni logiche](partition-data.md). (Non è necessario comprendere i dettagli interni per compilare o eseguire le applicazioni, ma aggiunto qui per un lettore curioso.)

## <a name="choosing-a-partition-key"></a><a id="choose-partitionkey"></a>Scelta di una chiave di partizione

Di seguito è riportata una buona guida per la scelta di una chiave di partizione:The following is a good guidance for choosing a partition key:

* Una singola partizione logica ha un limite massimo di 20 GB di spazio di archiviazione.  

* I contenitori Cosmos di Azure hanno una velocità effettiva minima di 400 unità di richiesta al secondo (RU/s). Quando viene eseguito il provisioning della velocità effettiva in un database, le RU minime per contenitore sono 100 unità richiesta al secondo (RU/s). Le richieste alla stessa chiave di partizione non possono superare la velocità effettiva allocata a una partizione. Se le richieste superano la velocità effettiva allocata, le richieste sono limitate. È quindi importante scegliere una chiave di partizione che non generi "aree sensibili" nell'applicazione.

* Scegliere una chiave di partizione con una vasta gamma di valori e modelli di accesso distribuiti uniformemente tra le partizioni logiche. Ciò consente di distribuire i dati e l'attività nel contenitore tra il set di partizioni logiche, in modo che le risorse per l'archiviazione dei dati e la velocità effettiva possano essere distribuite tra le partizioni logiche.

* Scegliere una chiave di partizione che distribuisca il carico di lavoro in modo uniforme tra tutte le partizioni e uniformemente nel tempo. La scelta della chiave di partizione deve bilanciare la necessità di query e transazioni di partizione efficienti con l'obiettivo di distribuire gli elementi tra più partizioni per ottenere la scalabilità.

* I candidati per le chiavi di partizione possono includere proprietà che vengono visualizzate di frequente come filtro nelle query. Le query possono essere indirizzate in modo efficiente includendo la chiave di partizione nel predicato del filtro.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sul partizionamento e la [scalabilità orizzontale in Azure Cosmos DB](partition-data.md).
* Informazioni sulla velocità effettiva di cui è stato eseguito il [provisioning in Azure Cosmos DB](request-units.md).
* Informazioni sulla [distribuzione globale in Azure Cosmos DB](distribute-data-globally.md).
