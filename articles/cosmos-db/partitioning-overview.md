---
title: Partizionamento in Azure Cosmos DB
description: Panoramica del partizionamento in Azure Cosmos DB
author: aliuy
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: andrl
ms.openlocfilehash: b89830d566b36b0446836d8f32aee5756e2d0991
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2018
ms.locfileid: "52498437"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Partizionamento in Azure Cosmos DB

Il partizionamento è la tecnica usata da Cosmos DB per ridimensionare singoli contenitori in un database al fine di soddisfare le esigenze di prestazioni dell'applicazione. Grazie al partizionamento, gli elementi in un contenitore sono suddivisi in subset distinti, denominati partizioni logiche. Le partizioni logiche vengono create in base al valore di una proprietà della chiave di partizione associata a ogni elemento.

Una partizione logica è un subset distinto di elementi in un contenitore. Gli elementi in una partizione logica vengono identificati dal valore della chiave di partizione condivisa da tutti gli elementi nella partizione logica.  Si consideri ad esempio un contenitore in cui sono presenti documenti, a ognuno dei quali è associata una proprietà `UserID`.  Se `UserID` funge da chiave della partizione per gli elementi in un contenitore e se sono presenti 1000 valori `UserID` univoci, vengono create 1000 partizioni per il contenitore.

A ogni elemento in un contenitore è associata una **chiave di partizione** che determina la **partizione logica** dell'elemento e ogni elemento dispone anche di un **id elemento** (univoco all'interno di una partizione logica).  L'**indice** di un elemento lo identifica in modo univoco ed è costituito dalla combinazione della chiave di partizione e dell'id elemento.

La scelta di una chiave di partizione è una decisione importante che influisce sulle prestazioni dell'applicazione.  Per informazioni dettagliate, vedere l'articolo [Scelta di una chiave di partizione](partitioning-overview.md#choose-partitionkey).

## <a name="logical-partition-management"></a>Gestione delle partizioni logiche

Cosmos DB gestisce il posizionamento delle partizioni logiche su partizioni fisiche (infrastruttura server) in modo trasparente e automatico per soddisfare in modo efficiente le esigenze di scalabilità e prestazioni del contenitore. Con l'aumento dei requisiti di archiviazione e di velocità effettiva dell'applicazione, Cosmos DB consente di spostare le partizioni logiche per distribuire automaticamente il carico tra un numero maggiore di server. Per altre informazioni sul modo in cui Cosmos DB gestisce le partizioni, vedere l'articolo [Partizionamento e ridimensionamento in Azure Cosmos DB](partition-data.md). Non è necessario conoscere questi dettagli per compilare o eseguire le tue applicazioni.

Cosmos DB usa il partizionamento basato su hash per distribuire le partizioni logiche tra le partizioni fisiche.  Il valore della chiave di partizione di un elemento viene calcolato tramite hash da Cosmos DB e il risultato così calcolato determina la partizione fisica. Cosmos DB alloca lo spazio degli hash delle chiavi di partizione in modo uniforme tra le "N" partizioni fisiche.

Le query che accedono ai dati all'interno di una singola partizione sono più convenienti rispetto alle query che accedono a più partizioni. Le transazioni (in stored procedure o trigger) sono consentite solo sugli elementi all'interno di una singola partizione logica.  

## <a id="choose-partitionkey"></a>Scelta di una chiave di partizione

Quando si sceglie una chiave di partizione, prendere in considerazione gli aspetti seguenti:

* Una singola partizione logica è consentita fino a un limite massimo di 10 GB di spazio di archiviazione.  

* I contenitori partizionati vengono configurati con una velocità effettiva minima di 400 UR/sec. Le richieste alla stessa chiave di partizione non possono superare la velocità effettiva allocata a una partizione. Se la velocità effettiva allocata viene superata, le richieste saranno soggette a limitazioni. È quindi importante scegliere una chiave di partizione che non generi "aree sensibili" nell'applicazione.

* Scegliere una chiave di partizione che distribuisca il carico di lavoro in modo uniforme tra tutte le partizioni e in modo uniforme nel tempo.  La scelta della chiave di partizione deve bilanciare la necessità di eseguire query e/o transazioni su una partizione in modo efficiente con l'obiettivo di distribuire elementi tra più partizioni per ottenere la scalabilità.

* Scegliere una chiave di partizione con una vasta gamma di valori e modelli di accesso distribuiti uniformemente tra le partizioni logiche. L'idea di base è quella di suddividere i dati e l'attività nel contenitore tra i set di partizioni logiche, in modo che le risorse per l'archiviazione e la velocità effettiva dei dati possano essere distribuite tra le partizioni logiche.

* I candidati per le chiavi di partizione possono includere le proprietà usate di frequente come filtro nelle query. Le query possono essere indirizzate in modo efficiente includendo la chiave di partizione nel predicato del filtro.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulle [partizioni](partition-data.md)
* Informazioni sulla [velocità effettiva con provisioning in Azure Cosmos DB](request-units.md)
* Informazioni sulla [distribuzione globale in Azure Cosmos DB](distribute-data-globally.md)
