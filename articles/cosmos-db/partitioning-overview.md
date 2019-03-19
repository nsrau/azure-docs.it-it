---
title: Partizionamento in Azure Cosmos DB
description: Panoramica del partizionamento in Azure Cosmos DB.
ms.author: mjbrown
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: a8db510bea57fa3d6ee873571e586bcef7508b26
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961636"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Partizionamento in Azure Cosmos DB

Azure Cosmos DB Usa il partizionamento per la scalabilità di singoli contenitori in un database per soddisfare le esigenze di prestazioni dell'applicazione. Il partizionamento, gli elementi in un contenitore sono suddivisi in sottoinsiemi distinti chiamati *partizioni logiche*. Partizioni logiche sono formate in base al valore di una *chiave di partizione* associate a ogni elemento in un contenitore. Tutti gli elementi in una partizione logica hanno lo stesso valore di chiave di partizione.

Ad esempio, un contenitore include i documenti. Ogni documento ha un valore univoco per il `UserID` proprietà. Se `UserID` funge da partizione della chiave per gli elementi nel contenitore e sono disponibili 1.000 univoco `UserID` valori, 1.000 partizioni logiche vengono create per il contenitore.

Oltre a una chiave di partizione che determina una partizione logica dell'elemento, a ogni elemento in un contenitore è un *ID elemento* (univoco all'interno di una partizione logica). La combinazione chiave di partizione e l'ID dell'elemento crea l'elemento *indice*, che identifica in modo univoco l'elemento.

[Scelta di una chiave di partizione](partitioning-overview.md#choose-partitionkey) è una decisione importante che influirà sulle prestazioni dell'applicazione.

## <a name="managing-logical-partitions"></a>Gestione delle partizioni logiche

Azure Cosmos DB in modo trasparente e automatico gestisce il posizionamento delle partizioni logiche su partizioni fisiche (infrastruttura di server) per soddisfare in modo efficiente le esigenze di scalabilità e prestazioni del contenitore. Come aumentano i requisiti di archiviazione e velocità effettiva di un'applicazione, Azure Cosmos DB consente di spostare le partizioni logiche per distribuire automaticamente il carico tra un maggior numero di server. 

Azure Cosmos DB Usa il partizionamento basato su hash per distribuire le partizioni logiche tra le partizioni fisiche. Azure Cosmos DB genera un hash per il valore di chiave di partizione di un elemento. Il risultato con hash determina la partizione fisica. Quindi, Azure Cosmos DB alloca lo spazio della partizione hash delle chiavi in modo uniforme tra le partizioni fisiche.

Le query che accedono ai dati all'interno di una singola partizione sono più convenienti rispetto alle query che accedono a più partizioni. Transazioni (in stored procedure o trigger) sono consentite solo per gli elementi in una singola partizione logica.

Per altre informazioni su come Azure Cosmos DB gestisce le partizioni, vedere [partizioni logiche](partition-data.md). (Non è necessario conoscere questi dettagli per compilare o eseguire le tue applicazioni.)

## <a id="choose-partitionkey"></a>Scelta di una chiave di partizione

Quando si sceglie una chiave di partizione, tenere presente quanto segue:

* Una singola partizione logica ha un limite massimo di 10 GB di spazio di archiviazione.  

* I contenitori partizionati hanno una velocità effettiva minima di 400 unità richiesta al secondo (UR/sec). Le richieste per la stessa chiave di partizione non possono superare la velocità effettiva allocata a una partizione. Se le richieste superano la velocità effettiva allocata, le richieste sono soggette a limitazioni. È quindi importante scegliere una chiave di partizione che non generi "aree sensibili" nell'applicazione.

* Scegliere una chiave di partizione che distribuisce il carico di lavoro in modo uniforme tra tutte le partizioni e in modo uniforme nel corso del tempo. La scelta della chiave di partizione deve bilanciare la necessità di query di partizione efficace e transazioni rispetto all'obiettivo di distribuzione di elementi tra più partizioni per ottenere la scalabilità.

* Scegliere una chiave di partizione con una vasta gamma di valori e modelli di accesso distribuiti uniformemente tra le partizioni logiche. Ciò consente di suddividere i dati e l'attività nel contenitore tra il set di partizioni logiche, in modo che le risorse per l'archiviazione e velocità effettiva possono essere distribuite tra le partizioni logiche.

* Candidati per le chiavi di partizione potrebbero includere le proprietà che vengono usati frequentemente come filtro nelle query. Le query possono essere indirizzate in modo efficiente includendo la chiave di partizione nel predicato del filtro.

## <a name="next-steps"></a>Passaggi successivi

* Scopri [partizioni](partition-data.md).
* Informazioni sulla [velocità effettiva con provisioning in Azure Cosmos DB](request-units.md).
* Informazioni sulla [distribuzione globale in Azure Cosmos DB](distribute-data-globally.md).
