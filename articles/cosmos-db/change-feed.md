---
title: Uso del supporto del feed delle modifiche in Azure Cosmos DB
description: Usare Azure Cosmos DB il supporto del feed delle modifiche per tenere traccia delle modifiche nei documenti, elaborazione basata su eventi come i trigger e mantenere aggiornati i sistemi di cache e analisi
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2020
ms.reviewer: sngun
ms.custom: seodec18
ms.openlocfilehash: e36e95aeb25c83ccd94f11e25bfe9f1b8f7bfdad
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80984862"
---
# <a name="change-feed-in-azure-cosmos-db"></a>Feed di modifiche in Azure Cosmos DB

Il supporto del feed di modifiche in Azure Cosmos DB ascolta eventuali modifiche in un contenitore di Azure Cosmos. Restituisce quindi l'elenco di documenti cambiati nell'ordine in cui sono stati modificati. Le modifiche sono persistenti, possono essere elaborate in modo asincrono e incrementale e l'output può essere distribuito a uno o più consumer per l'elaborazione parallela.

Altre informazioni sui [modelli di progettazione del feed di modifiche](change-feed-design-patterns.md).

## <a name="supported-apis-and-client-sdks"></a>API e SDK client supportati

La funzionalità è attualmente supportata dalle API e dagli SDK client Cosmos DB seguenti.

| **Driver client** | **API SQL** | **API di Azure Cosmos DB per Cassandra** | **API Azure Cosmos DB per MongoDB** | **API Gremlin**|**API di tabella** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | Sì | Sì | Sì | Sì | No |
|Java|Sì|Sì|Sì|Sì|No|
|Python|Sì|Sì|Sì|Sì|No|
|Node/JS|Sì|Sì|Sì|Sì|No|

## <a name="change-feed-and-different-operations"></a>Feed di modifiche e operazioni diverse

Attualmente, tutti gli inserimenti e gli aggiornamenti vengono visualizzati nel feed delle modifiche. Non è possibile filtrare il feed delle modifiche per per un tipo di operazione specifico. Una possibile alternativa consiste nell'aggiungere un "marcatore soft" sull'elemento per gli aggiornamenti e applicare un filtro in base a quanto avviene durante l'elaborazione di elementi nel feed delle modifiche.

Il feed di modifiche attualmente non registra le eliminazioni. Analogamente all'esempio precedente, è possibile aggiungere un marcatore Soft sugli elementi da eliminare. Ad esempio, è possibile aggiungere un attributo nell'elemento denominato "Deleted" e impostarlo su "true" e impostare una durata (TTL) per l'elemento, in modo che possa essere eliminato automaticamente. È possibile leggere il feed delle modifiche per gli elementi cronologici (la modifica più recente corrispondente all'elemento, non include le modifiche intermedie), ad esempio gli elementi che sono stati aggiunti cinque anni fa. È possibile leggere il feed delle modifiche fino all'origine del contenitore, ma se un elemento viene eliminato, verrà rimosso dal feed delle modifiche.

### <a name="sort-order-of-items-in-change-feed"></a>Ordinamento degli elementi nel feed di modifiche

Gli elementi nel feed di modifiche sono ordinati in base all'ora di modifica. Questo ordinamento è garantito per ogni chiave di partizione logica.

### <a name="consistency-level"></a>Livello di coerenza

Quando si utilizza il feed delle modifiche in un livello di coerenza finale, è possibile che si verifichino eventi duplicati tra le operazioni di lettura del feed di modifiche successive (l'ultimo evento di un'operazione di lettura viene visualizzato come primo dei prossimi).

### <a name="change-feed-in-multi-region-azure-cosmos-accounts"></a>Feed di modifiche in più aree negli account Azure Cosmos

In un account Azure Cosmos con più aree, se un'area di scrittura effettua il failover, il feed di modifiche verrà utilizzato nell'operazione di failover manuale e sarà contiguo.

### <a name="change-feed-and-time-to-live-ttl"></a>Feed di modifiche e durata (TTL)

Se una proprietà TTL (Time to Live) è impostata su un elemento su -1, il feed di modifiche verrà mantenuto per sempre. Se i dati non sono eliminati, rimarranno nel feed di modifiche.  

### <a name="change-feed-and-_etag-_lsn-or-_ts"></a>Feed di modifiche e _etag, _lsn or _ts

Il formato _etag è interno e non è consigliabile dipendere da esso, perché può cambiare in qualsiasi momento. _ts è un timestamp di creazione o di modifica. Si può usare _ts per confronti cronologici. _lsn è un ID batch aggiunto solo per il feed di modifiche. rappresenta l'ID della transazione. Molti elementi potrebbe avere lo stesso _lsn. ETag su FeedResponse è diverso dall'_etag che viene visualizzato nell'elemento. _etag è un identificatore interno utilizzato per il controllo della concorrenza e indica la versione dell'elemento mentre ETag viene utilizzato per la sequenziazione del feed.

## <a name="working-with-change-feed"></a>Utilizzo del feed di modifiche

È possibile utilizzare il feed di modifiche utilizzando le opzioni seguenti:

* [Feed di modifiche con Funzioni di Azure](change-feed-functions.md)
* [Uso del feed delle modifiche con il processore del feed delle modifiche](change-feed-processor.md) 

Il feed di modifiche è disponibile per ogni chiave di partizione logica nel contenitore, ed è quindi possibile distribuirlo a uno o più consumer per l'elaborazione parallela, come illustrato nell'immagine seguente.

![Elaborazione distribuita del feed delle modifiche di Azure Cosmos DB](./media/change-feed/changefeedvisual.png)

## <a name="features-of-change-feed"></a>Funzionalità del feed di modifiche

* Il feed di modifiche è abilitato per impostazione predefinita per tutti gli account Azure Cosmos.

* È possibile usare la [velocità effettiva con provisioning](request-units.md) per leggere dal feed di modifiche, proprio come ogni altra operazione di Azure Cosmos DB, in ogni area associata al database di Azure Cosmos.

* Il feed di modifiche include inserimenti e operazioni di aggiornamenti eseguite sugli elementi all'interno del contenitore. È possibile acquisire le eliminazioni impostando un flag "eliminazione temporanea" all'interno degli elementi (ad esempio, documenti) al posto delle eliminazioni. In alternativa, è possibile impostare un periodo di scadenza limitato per gli elementi con la [funzionalità TTL](time-to-live.md). Per esempio, 24 ore e utilizzare il valore di quella proprietà per acquisire le eliminazioni. Con questa soluzione è necessario elaborare le modifiche in un intervallo di tempo minore rispetto al periodo di scadenza TTL.

* Ogni modifica apportata a un elemento viene visualizzata una sola volta nel feed di modifiche e i client devono gestire la logica di checkpoint. Se si desidera evitare la complessità della gestione dei checkpoint, il processore del feed di modifiche fornisce il checkpoint automatico e la semantica "almeno una volta". Vedere [uso del feed di modifiche con il processore del feed delle modifiche](change-feed-processor.md).

* Solo la modifica più recente per un determinato elemento viene inclusa nel registro modifiche. Le modifiche intermedie potrebbero non essere disponibili.

* Il feed di modifiche è ordinato in base all'ordine di modifica in ciascun valore della chiave di partizione. Non esiste alcun ordine garantito tra i valori della chiave di partizione.

* Le modifiche possono essere sincronizzate da qualsiasi punto nel tempo, in altre parole non è previsto un periodo di conservazione dei dati fisso per cui sono disponibili le modifiche.

* Le modifiche sono disponibili in parallelo per tutte le chiavi di partizione logica di un contenitore di Azure Cosmos. Questa funzionalità consente di apportare modifiche da contenitori di grandi dimensioni per poi elaborarle in parallelo da più consumer.

* Le applicazioni possono richiedere più feed di modifiche nello stesso contenitore contemporaneamente. Changefeedoptions.StartTime è utilizzabile per fornire un punto di partenza iniziale. Ad esempio, per trovare il token di continuazione corrispondente a un tempo specificato. ContinuationToken, se specificato, ha la precedenza sui valori StartTime e StartFromBeginning. La precisione di ChangeFeedOptions.StartTime è circa di 5 secondi.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Feed delle modifiche nelle API per Cassandra e MongoDB

La funzionalità del feed delle modifiche viene rilevata come flusso di modifica nell'API MongoDB ed esegue una query con predicato in API Cassandra. Per altre informazioni sui dettagli di implementazione per l'API MongoDB, vedere i [flussi di modifiche nell'api Azure Cosmos DB per MongoDB](mongodb-change-streams.md).

Apache Cassandra nativo fornisce Change Data Capture (CDC), un meccanismo per contrassegnare le tabelle specifiche per l'archiviazione e rifiutare le Scritture nelle tabelle quando viene raggiunta una dimensione configurabile su disco per il log CDC. La funzionalità del feed delle modifiche nell'API Azure Cosmos DB per Cassandra consente di eseguire query sulle modifiche con predicato tramite CQL. Per altre informazioni sui dettagli di implementazione, vedere [feed delle modifiche nell'API Azure Cosmos DB per Cassandra](cassandra-change-feed.md).

## <a name="next-steps"></a>Passaggi successivi

È possibile ottenere altre informazioni sul feed di modifiche negli articoli seguenti:

* [Opzioni per la lettura di feed di modifiche](read-change-feed.md)
* [Feed di modifiche con Funzioni di Azure](change-feed-functions.md)
* [Uso del processore dei feed di modifiche](change-feed-processor.md)
