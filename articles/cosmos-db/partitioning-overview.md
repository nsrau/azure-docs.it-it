---
title: Partizionamento in Azure Cosmos DB
description: Informazioni sul partizionamento in Azure Cosmos DB, procedure consigliate per la scelta di una chiave di partizione e su come gestire le partizioni logiche
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 1a760b4cedad5e43a2ef9f186162675aaf6d5ea5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82234180"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Partizionamento in Azure Cosmos DB

Azure Cosmos DB usa il partizionamento per ridimensionare i singoli contenitori di un database per soddisfare le esigenze di prestazioni dell'applicazione. Nel partizionamento gli elementi in un contenitore sono divisi in subset distinti denominati *partizioni logiche*. Le partizioni logiche vengono formate in base al valore di una *chiave di partizione* associata a ogni elemento in un contenitore. Tutti gli elementi di una partizione logica hanno lo stesso valore della chiave di partizione.

Un contenitore, ad esempio, include gli elementi. Ogni elemento ha un valore univoco per la `UserID` proprietà. Se `UserID` funge da chiave di partizione per gli elementi nel contenitore e sono presenti 1.000 valori univoci `UserID` , vengono create 1.000 partizioni logiche per il contenitore.

Oltre a una chiave di partizione che determina la partizione logica dell'elemento, ogni elemento in un contenitore dispone di un *ID elemento* (univoco all'interno di una partizione logica). Combinando la chiave di partizione e l' *ID* dell'elemento viene creato l' *Indice*dell'elemento, che identifica in modo univoco l'elemento.

La [scelta di una chiave di partizione](partitioning-overview.md#choose-partitionkey) è una decisione importante che influirà sulle prestazioni dell'applicazione.

## <a name="managing-logical-partitions"></a>Gestione di partizioni logiche

Azure Cosmos DB in modo trasparente e automatico gestisce il posizionamento delle partizioni logiche nelle partizioni fisiche per soddisfare in modo efficiente le esigenze di scalabilità e prestazioni del contenitore. Poiché i requisiti di archiviazione e velocità effettiva di un'applicazione aumentano, Azure Cosmos DB sposta le partizioni logiche per suddividere automaticamente il carico su un numero maggiore di partizioni fisiche. È possibile ottenere altre informazioni sulle [partizioni fisiche](partition-data.md#physical-partitions).

Azure Cosmos DB usa il partizionamento basato su hash per distribuire partizioni logiche tra partizioni fisiche. Azure Cosmos DB hashing del valore della chiave di partizione di un elemento. Il risultato con hash determina la partizione fisica. Quindi, Azure Cosmos DB alloca in modo uniforme lo spazio chiavi degli hash della chiave di partizione tra le partizioni fisiche.

Le transazioni (in stored procedure o trigger) sono consentite solo per gli elementi di una singola partizione logica.

Sono disponibili altre informazioni sul [modo in cui Azure Cosmos DB gestisce le partizioni](partition-data.md). Non è necessario comprendere i dettagli interni per compilare o eseguire le applicazioni, ma è stato aggiunto qui per un lettore curioso.

## <a name="choosing-a-partition-key"></a><a id="choose-partitionkey"></a>Scelta di una chiave di partizione

La selezione della chiave di partizione è una scelta di progettazione semplice, ma importante, in Azure Cosmos DB. Una volta selezionata la chiave di partizione, non è possibile modificarla sul posto. Se è necessario modificare la chiave di partizione, è necessario spostare i dati in un nuovo contenitore con la nuova chiave di partizione desiderata.

Per **tutti i** contenitori, la chiave di partizione deve:

* Essere una proprietà con un valore che non cambia. Se una proprietà è la chiave di partizione, non è possibile aggiornare il valore della proprietà.
* Hanno una cardinalità elevata. In altre parole, la proprietà deve avere un'ampia gamma di valori possibili.
* Diffondere il consumo delle unità richiesta e l'archiviazione dei dati in modo uniforme in tutte le partizioni logiche. In questo modo si garantisce anche il consumo di ur e la distribuzione di archiviazione tra le partizioni fisiche.

Se sono necessarie [transazioni ACID](database-transactions-optimistic-concurrency.md#multi-item-transactions) con più elementi in Azure Cosmos DB, sarà necessario usare [stored procedure o trigger](how-to-write-stored-procedures-triggers-udfs.md#stored-procedures). Tutte le stored procedure e i trigger basati su JavaScript hanno come ambito un'unica partizione logica.

## <a name="partition-keys-for-read-heavy-containers"></a>Chiavi di partizione per i contenitori con intensa attività di lettura

Per la maggior parte dei contenitori, i criteri indicati sopra sono tutti quelli da considerare quando si sceglie una chiave di partizione. Per i contenitori di grandi dimensioni, tuttavia, potrebbe essere necessario scegliere una chiave di partizione che viene visualizzata frequentemente come filtro nelle query. Le query possono essere [indirizzate in modo efficiente solo alle partizioni fisiche pertinenti](how-to-query-container.md#in-partition-query) includendo la chiave di partizione nel predicato del filtro.

Se la maggior parte delle richieste del carico di lavoro è costituita da query e la maggior parte delle query dispone di un filtro di uguaglianza sulla stessa proprietà, questa proprietà può essere una scelta ottimale per la chiave di partizione. Se, ad esempio, si esegue spesso una query che filtra `UserID`in, la `UserID` selezione come chiave di partizione ridurrebbe il numero di [query tra partizioni](how-to-query-container.md#avoiding-cross-partition-queries).

Tuttavia, se il contenitore è di dimensioni ridotte, è probabile che non si disponga di partizioni fisiche sufficienti per preoccuparsi dell'effetto sulle prestazioni delle query tra partizioni. La maggior parte dei contenitori di piccole dimensioni in Azure Cosmos DB richiede solo una o due partizioni fisiche.

Se il contenitore può raggiungere più di alcune partizioni fisiche, è necessario assicurarsi di selezionare una chiave di partizione che riduce al minimo le query tra partizioni. Il contenitore richiederà più di alcune partizioni fisiche quando si verifica una delle condizioni seguenti:

* Il provisioning del contenitore avrà più di 30.000 UR
* Nel contenitore vengono archiviati oltre 100 GB di dati

## <a name="using-item-id-as-the-partition-key"></a>Uso dell'ID elemento come chiave di partizione

Se il contenitore dispone di una proprietà con un'ampia gamma di valori possibili, probabilmente si tratta di una scelta ottimale per la chiave di partizione. Un esempio possibile di tale proprietà è l' *ID dell'elemento*. Per i contenitori di piccole dimensioni o con un elevato numero di operazioni di scrittura, l' *ID dell'elemento* è naturalmente un'ottima scelta per la chiave di partizione.

È garantito che l' *ID elemento* della proprietà di sistema esista in ogni elemento del contenitore Cosmos. Potrebbero essere presenti altre proprietà che rappresentano un ID logico dell'elemento. In molti casi, si tratta anche di opzioni di chiave di partizione eccezionali per gli stessi motivi dell' *ID dell'elemento*.

L' *ID dell'elemento* è un'ottima scelta per le chiavi di partizione per i motivi seguenti:

* È disponibile un'ampia gamma di valori possibili, ovvero un *ID* univoco per ogni elemento.
* Poiché è presente un *ID di elemento* univoco per ogni elemento, l' *ID dell'elemento* ha un ottimo lavoro per bilanciare uniformemente il consumo di ur e l'archiviazione dei dati.
* È possibile eseguire facilmente letture di punti efficienti, poiché si conosce sempre la chiave di partizione di un elemento se si conosce l' *ID elemento*.

Di seguito sono riportati alcuni aspetti da considerare quando si seleziona l' *ID elemento* come chiave di partizione:

* Se l' *ID dell'elemento* è la chiave di partizione, diventerà un identificatore univoco nell'intero contenitore. Non sarà possibile avere elementi con un *ID elemento*duplicato.
* Se si dispone di un contenitore con elevato numero di operazioni di lettura con numerose [partizioni fisiche](partition-data.md#physical-partitions), le query saranno più efficienti se hanno un filtro di uguaglianza con l' *ID dell'elemento*.
* Non è possibile eseguire stored procedure o trigger su più partizioni logiche.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sul [partizionamento e la scalabilità orizzontale in Azure Cosmos DB](partition-data.md).
* Informazioni sulla [velocità effettiva con provisioning in Azure Cosmos DB](request-units.md).
* Informazioni sulla [distribuzione globale in Azure Cosmos DB](distribute-data-globally.md).
