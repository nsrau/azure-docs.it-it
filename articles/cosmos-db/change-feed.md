---
title: Uso del supporto del feed delle modifiche in Azure Cosmos DB
description: Usare il supporto del feed di modifiche di Azure Cosmos DB per tenere traccia delle modifiche nei documenti, eseguire elaborazioni basate su eventi come i trigger e mantenere aggiornati i sistemi di cache e analisi.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2020
ms.reviewer: sngun
ms.custom: seodec18
ms.openlocfilehash: 4cd0ad1553f04a781349a4664fbb408108015632
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88510284"
---
# <a name="change-feed-in-azure-cosmos-db"></a>Feed di modifiche in Azure Cosmos DB

Il supporto del feed di modifiche in Azure Cosmos DB ascolta eventuali modifiche in un contenitore di Azure Cosmos. Restituisce quindi l'elenco di documenti cambiati nell'ordine in cui sono stati modificati. Le modifiche sono persistenti, possono essere elaborate in modo asincrono e incrementale e l'output può essere distribuito a uno o più consumer per l'elaborazione parallela.

Altre informazioni sui [modelli di progettazione del feed di modifiche](change-feed-design-patterns.md).

## <a name="supported-apis-and-client-sdks"></a>API e SDK client supportati

La funzionalità è attualmente supportata dalle API e dagli SDK client Cosmos DB seguenti.

| **Driver client** | **API SQL** | **API Azure Cosmos DB per Cassandra** | **API di Azure Cosmos DB per MongoDB** | **API Gremlin**|**API di tabella** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | Sì | Sì | Sì | Sì | No |
|Java|Sì|Sì|Sì|Sì|No|
|Python|Sì|Sì|Sì|Sì|No|
|Node/JS|Sì|Sì|Sì|Sì|No|

## <a name="change-feed-and-different-operations"></a>Feed di modifiche e operazioni diverse

Attualmente, nel feed di modifiche vengono visualizzati tutti gli inserimenti e gli aggiornamenti e non è possibile filtrare il feed di modifiche per un tipo specifico di operazione. Una possibile alternativa, quindi, è aggiungere un "soft marker" di aggiornamento sugli elementi corrispondenti e filtrare in base a esso durante l'elaborazione degli elementi nel feed di modifiche.

Attualmente, le eliminazioni non vengono registrate nel feed di modifiche. Analogamente all'esempio precedente, è possibile tuttavia aggiungere un soft marker sugli elementi da eliminare. È possibile, ad esempio, aggiungere all'elemento un attributo denominato "deleted", impostarlo su "true" e impostare una durata (TTL) nell'elemento, in modo che possa essere eliminato automaticamente. È possibile leggere il feed di modifiche per gli elementi cronologici (la modifica più recente corrispondente all'elemento, senza includere le modifiche intermedie), come, ad esempio, gli elementi che sono stati aggiunti cinque anni fa. È possibile leggere il feed di modifiche a ritroso fino all'origine del contenitore, ma se un elemento viene eliminato, verrà rimosso dal feed di modifiche.

### <a name="sort-order-of-items-in-change-feed"></a>Ordinamento degli elementi nel feed di modifiche

Gli elementi nel feed di modifiche sono ordinati in base all'ora di modifica. Questo ordinamento è garantito a livello di chiave di partizione logica.

### <a name="consistency-level"></a>Livello di coerenza

Quando si usa il feed di modifiche in un livello di coerenza finale, è possibile che si verifichino eventi duplicati tra le operazioni di lettura di feed di modifiche successivi (l'ultimo evento di un'operazione di lettura viene visualizzato come il primo dell'operazione successiva).

### <a name="change-feed-in-multi-region-azure-cosmos-accounts"></a>Feed di modifiche in più aree negli account Azure Cosmos

In un account Azure Cosmos con più aree, se un'area di scrittura effettua il failover, il feed di modifiche verrà utilizzato nell'operazione di failover manuale e sarà contiguo.

### <a name="change-feed-and-time-to-live-ttl"></a>Feed di modifiche e durata (TTL)

Se una proprietà TTL (Time to Live) è impostata su un elemento su -1, il feed di modifiche verrà mantenuto per sempre. Se i dati non sono eliminati, rimarranno nel feed di modifiche.  

### <a name="change-feed-and-_etag-_lsn-or-_ts"></a>Feed di modifiche e _etag, _lsn or _ts

Il formato _etag è interno e non è consigliabile dipendere da esso, perché può cambiare in qualsiasi momento. _ts è un timestamp di creazione o di modifica. Si può usare _ts per confronti cronologici. _lsn è un ID batch che viene aggiunto solo per i feed di modifiche e rappresenta l'ID transazione. Molti elementi potrebbe avere lo stesso _lsn. ETag su FeedResponse è diverso dall'_etag che viene visualizzato nell'elemento. _etag è un identificatore interno e viene usato per il controllo della concorrenza. La proprietà _etag indica la versione dell'elemento, mentre la proprietà ETag viene utilizzata per la sequenziazione del feed.

## <a name="working-with-change-feed"></a>Utilizzo del feed di modifiche

È possibile utilizzare il feed di modifiche utilizzando le opzioni seguenti:

* [Feed di modifiche con Funzioni di Azure](change-feed-functions.md)
* [Uso del feed di modifiche con il processore dei feed di modifiche](change-feed-processor.md) 

Il feed di modifiche è disponibile per ogni chiave di partizione logica nel contenitore, ed è quindi possibile distribuirlo a uno o più consumer per l'elaborazione parallela, come illustrato nell'immagine seguente.

:::image type="content" source="./media/change-feed/changefeedvisual.png" alt-text="Elaborazione distribuita del feed delle modifiche di Azure Cosmos DB" border="false":::

## <a name="features-of-change-feed"></a>Funzionalità del feed di modifiche

* Il feed di modifiche è abilitato per impostazione predefinita per tutti gli account Azure Cosmos.

* È possibile usare la [velocità effettiva con provisioning](request-units.md) per leggere dal feed di modifiche, proprio come ogni altra operazione di Azure Cosmos DB, in ogni area associata al database di Azure Cosmos.

* Il feed di modifiche include inserimenti e operazioni di aggiornamenti eseguite sugli elementi all'interno del contenitore. È possibile acquisire le eliminazioni impostando un flag "eliminazione temporanea" all'interno degli elementi (ad esempio, documenti) al posto delle eliminazioni. In alternativa, è possibile impostare un periodo di scadenza limitato per gli elementi con la [funzionalità TTL](time-to-live.md). Per esempio, 24 ore e utilizzare il valore di quella proprietà per acquisire le eliminazioni. Con questa soluzione è necessario elaborare le modifiche in un intervallo di tempo minore rispetto al periodo di scadenza TTL.

* Ogni modifica apportata a un elemento viene visualizzata una sola volta nel feed di modifiche e i client devono gestire la logica di checkpoint. Per evitare la complessità del checkpoint di gestione, il processore del feed di modifiche fornisce funzionalità di checkpoint automatiche e semantica di tipo "at least once". Vedere [Uso del feed di modifiche con il processore dei feed di modifiche](change-feed-processor.md).

* Solo la modifica più recente per un determinato elemento viene inclusa nel registro modifiche. Le modifiche intermedie potrebbero non essere disponibili.

* Il feed di modifiche è ordinato in base all'ordine di modifica in ciascun valore della chiave di partizione. Non esiste alcun ordine garantito tra i valori della chiave di partizione.

* Le modifiche possono essere sincronizzate da qualsiasi punto nel tempo, in altre parole non è previsto un periodo di conservazione dei dati fisso per cui sono disponibili le modifiche.

* Le modifiche sono disponibili in parallelo per tutte le chiavi di partizione logica di un contenitore di Azure Cosmos. Questa funzionalità consente di apportare modifiche da contenitori di grandi dimensioni per poi elaborarle in parallelo da più consumer.

* Le applicazioni possono richiedere più feed di modifiche alla volta nello stesso contenitore. Changefeedoptions.StartTime è utilizzabile per fornire un punto di partenza iniziale. Ad esempio, per trovare il token di continuazione corrispondente a un tempo specificato. Se si specifica ContinuationToken, questo avrà la priorità rispetto ai valori di StartTime e StartFromBeginning. La precisione di ChangeFeedOptions.StartTime è circa di 5 secondi.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Feed di modifiche nelle API per Cassandra e MongoDB

La funzionalità del feed di modifiche è disponibile come flusso di modifiche nell'API MongoDB e come query con predicato nell'API Cassandra. Per altre informazioni sui dettagli di implementazione per l'API MongoDB, vedere [Flussi di modifiche nell'API Azure Cosmos DB per MongoDB](mongodb-change-streams.md).

La versione nativa di Apache Cassandra include Change Data Capture (CDC), un meccanismo per contrassegnare tabelle specifiche per l'archiviazione e rifiutare operazioni di scrittura in tali tabelle quando viene raggiunta una dimensione su disco configurabile per il log CDC. La funzionalità del feed di modifiche nell'API Azure Cosmos DB per Cassandra consente di eseguire query relative alle modifiche con predicato tramite CQL. Per altre informazioni sui dettagli di implementazione, vedere [Feed di modifiche nell'API Azure Cosmos DB per Cassandra](cassandra-change-feed.md).

## <a name="next-steps"></a>Passaggi successivi

È possibile ottenere altre informazioni sul feed di modifiche negli articoli seguenti:

* [Opzioni per la lettura di feed di modifiche](read-change-feed.md)
* [Feed di modifiche con Funzioni di Azure](change-feed-functions.md)
* [Uso del processore dei feed di modifiche](change-feed-processor.md)
