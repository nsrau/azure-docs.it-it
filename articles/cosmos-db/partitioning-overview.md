---
title: Partizionamento in Azure Cosmos DB
description: Panoramica del partizionamento in Azure Cosmos DB.
ms.author: rimman
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 1bfa7104425b5013f9cdf36ff3c1dd88107d3ec7
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467850"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Partizionamento in Azure Cosmos DB

Azure Cosmos DB usa il partizionamento per ridimensionare i singoli contenitori di un database per soddisfare le esigenze di prestazioni dell'applicazione. Nel partizionamento gli elementi in un contenitore sono divisi in subset distinti denominati *partizioni logiche*. Le partizioni logiche vengono formate in base al valore di una *chiave di partizione* associata a ogni elemento in un contenitore. Tutti gli elementi di una partizione logica hanno lo stesso valore della chiave di partizione.

Un contenitore, ad esempio, include gli elementi. Ogni elemento ha un valore univoco per la `UserID` proprietà. Se `UserID` funge da chiave di partizione per gli elementi nel contenitore e sono presenti 1.000 valori univoci `UserID` , vengono create 1.000 partizioni logiche per il contenitore.

Oltre a una chiave di partizione che determina la partizione logica dell'elemento, ogni elemento in un contenitore dispone di un *ID elemento* (univoco all'interno di una partizione logica). Combinando la chiave di partizione e l'ID dell'elemento viene creato l' *Indice*dell'elemento, che identifica in modo univoco l'elemento.

La [scelta di una chiave di partizione](partitioning-overview.md#choose-partitionkey) è una decisione importante che influirà sulle prestazioni dell'applicazione.

## <a name="managing-logical-partitions"></a>Gestione di partizioni logiche

Azure Cosmos DB in modo trasparente e automatico gestisce il posizionamento delle partizioni logiche nelle partizioni fisiche per soddisfare in modo efficiente le esigenze di scalabilità e prestazioni del contenitore. Poiché i requisiti di archiviazione e velocità effettiva di un'applicazione aumentano, Azure Cosmos DB sposta le partizioni logiche per suddividere automaticamente il carico su un numero maggiore di server. 

Azure Cosmos DB usa il partizionamento basato su hash per distribuire partizioni logiche tra partizioni fisiche. Azure Cosmos DB hashing del valore della chiave di partizione di un elemento. Il risultato con hash determina la partizione fisica. Quindi, Azure Cosmos DB alloca in modo uniforme lo spazio chiavi degli hash della chiave di partizione tra le partizioni fisiche.

Le query che accedono ai dati all'interno di una singola partizione logica sono più convenienti rispetto alle query che accedono a più partizioni. Le transazioni (in stored procedure o trigger) sono consentite solo per gli elementi di una singola partizione logica.

Per ulteriori informazioni sul modo in cui Azure Cosmos DB gestisce le partizioni, vedere [partizioni logiche](partition-data.md). Non è necessario comprendere i dettagli interni per compilare o eseguire le applicazioni, ma è stato aggiunto qui per un lettore curioso.

## <a id="choose-partitionkey"></a>Scelta di una chiave di partizione

Di seguito sono riportate informazioni utili per la scelta di una chiave di partizione:

* Una singola partizione logica ha un limite massimo di 10 GB di spazio di archiviazione.  

* I contenitori di Azure Cosmos hanno una velocità effettiva minima di 400 unità richiesta al secondo (UR/sec). Le richieste alla stessa chiave di partizione non possono superare la velocità effettiva allocata a una partizione. Se le richieste superano la velocità effettiva allocata, le richieste sono con limitazione della frequenza. È quindi importante scegliere una chiave di partizione che non generi "aree sensibili" nell'applicazione.

* Scegliere una chiave di partizione con una vasta gamma di valori e modelli di accesso distribuiti uniformemente tra le partizioni logiche. Ciò consente di distribuire i dati e l'attività nel contenitore attraverso il set di partizioni logiche, in modo che sia possibile distribuire le risorse per l'archiviazione e la velocità effettiva dei dati tra le partizioni logiche.

* Scegliere una chiave di partizione che distribuisce il carico di lavoro in modo uniforme tra tutte le partizioni e in modo uniforme nel tempo. La scelta della chiave di partizione deve bilanciare la necessità di eseguire query di partizionamento e transazioni efficienti rispetto allo scopo di distribuire gli elementi tra più partizioni per ottenere la scalabilità.

* I candidati per le chiavi di partizione possono includere proprietà che vengono visualizzate di frequente come filtro nelle query. Le query possono essere indirizzate in modo efficiente includendo la chiave di partizione nel predicato del filtro.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sul [partizionamento e la scalabilità orizzontale in Azure Cosmos DB](partition-data.md).
* Informazioni sulla [velocità effettiva con provisioning in Azure Cosmos DB](request-units.md).
* Informazioni sulla [distribuzione globale in Azure Cosmos DB](distribute-data-globally.md).
