---
title: Modificare i modelli di progettazione dei feed in Azure Cosmos DBChange feed design patterns in Azure Cosmos DB
description: Panoramica dei modelli di progettazione dei feed di modifiche comuni
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 7e6981fb57421846b491693bb6195ecef31a3773
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986304"
---
# <a name="change-feed-design-patterns-in-azure-cosmos-db"></a>Modificare i modelli di progettazione dei feed in Azure Cosmos DBChange feed design patterns in Azure Cosmos DB

Il feed di modifiche di Azure Cosmos DB consente un'elaborazione efficiente di set di dati di grandi dimensioni con un volume elevato di scritture. Il feed di modifiche offre anche un'alternativa all'esecuzione di query su un intero set di dati per identificare le modifiche apportate. Questo documento è incentrato sui modelli di progettazione dei feed di modifiche comuni, sui compromessi di progettazione e sulle limitazioni dei feed di modifiche.

Azure Cosmos DB è particolarmente adatto per le applicazioni IoT, di videogiochi, del settore della vendita al dettaglio e di registrazioni di operazioni. Uno schema progettuale comune in queste applicazioni prevede l'uso delle modifiche ai dati per attivare azioni aggiuntive. Esempi di azioni aggiuntive:

* Attivazione di una notifica o di una chiamata a un'API quando un elemento viene inserito o modificato.
* Elaborazione di flussi in tempo reale per l'elaborazione IoT o di analisi in tempo reale sui dati operativi.
* Spostamento dei dati, ad esempio la sincronizzazione con una cache, un motore di ricerca, un data warehouse o un archivio a freddo.

Il feed di modifiche in Azure Cosmos DB consente di creare soluzioni efficienti e scalabili per ognuno di questi modelli, come illustrato nell'immagine seguente:

![Uso del feed delle modifiche di Azure Cosmos DB per agevolare le analisi in tempo reale e gli scenari di calcolo guidati dagli eventi](./media/change-feed/changefeedoverview.png)

## <a name="event-computing-and-notifications"></a>Elaborazione di eventi e notifiche

Il feed di modifiche di Azure Cosmos DB può semplificare gli scenari che devono attivare una notifica o una chiamata a un'API in base a un determinato evento. È possibile usare [Change Feed Process Library](change-feed-processor.md) per eseguire automaticamente il polling delle modifiche nel contenitore e chiamare un'API esterna ogni volta che è presente una scrittura o un aggiornamento.

È anche possibile attivare in modo selettivo una notifica o inviare una chiamata a un'API in base a criteri specifici. Ad esempio, se si esegue la lettura dal feed di modifiche usando Funzioni di [Azure](change-feed-functions.md), è possibile inserire la logica nella funzione per inviare una notifica solo se sono stati soddisfatti criteri specifici. Mentre il codice della funzione di Azure verrebbe eseguito durante ogni scrittura e aggiornamento, la notifica viene inviata solo se sono stati soddisfatti criteri specifici.

## <a name="real-time-stream-processing"></a>Elaborazione del flusso in tempo reale

Il feed di modifiche del database Cosmos di Azure può essere usato per l'elaborazione del flusso in tempo reale per l'ioT o l'elaborazione dell'analisi in tempo reale sui dati operativi.
Ad esempio, è possibile ricevere e archiviare i dati degli eventi da dispositivi, sensori, infrastrutture e applicazioni ed elaborare questi eventi in tempo reale, utilizzando [Spark](../hdinsight/spark/apache-spark-overview.md). L'immagine seguente mostra come è possibile implementare un'architettura lambda usando il database Cosmos di Azure tramite il feed di modifiche:The following image shows how you can implement a lambda architecture using the Azure Cosmos DB via change feed:

![Pipeline lambda basate su Azure Cosmos DB per l'inserimento e le query](./media/change-feed/lambda.png)

In many cases, stream processing implementations first receive a high volume of incoming data into a temporary message queue such as Azure Event Hub or Apache Kafka. Il feed di modifiche è un'ottima alternativa grazie alla capacità di Azure Cosmos DB di supportare una frequenza sostenuta di inserimento di dati con bassa latenza di lettura e scrittura garantita. I vantaggi del feed di modifiche di Azure Cosmos DB su una coda di messaggi includono:The advantages of the Azure Cosmos DB change feed over a message queue include:

### <a name="data-persistence"></a>Salvataggio permanente dei dati

I dati scritti in Azure Cosmos DB verranno visualizzati nel feed di modifiche e verranno conservati fino all'eliminazione. Le code di messaggi hanno in genere un periodo di conservazione massimo. Ad esempio, [l'hub eventi](https://azure.microsoft.com/services/event-hubs/) di Azure offre una conservazione massima dei dati di 90 giorni.

### <a name="querying-ability"></a>Capacità di interrogazione

In addition to reading from a Cosmos container's change feed, you can also run SQL queries on the data stored in Azure Cosmos DB. Il feed di modifiche non è una duplicazione dei dati già presenti nel contenitore, ma solo un meccanismo diverso di lettura dei dati. Pertanto, se si leggono i dati dal feed di modifiche, sarà sempre coerente con le query dello stesso contenitore di database Cosmos di Azure.Therefore, if you read data from the change feed, it will always be consistent with queries of the same Azure Cosmos DB container.

### <a name="high-availability"></a>Disponibilità elevata

Azure Cosmos DB offre fino al 99,999% di disponibilità di lettura e scrittura. A differenza di molte code di messaggi, i dati del database Cosmos di Azure possono essere facilmente distribuiti a livello globale e configurati con un [RTO (Recovery Time Objective)](consistency-levels-tradeoffs.md#rto) pari a zero.

Dopo l'elaborazione degli elementi nel feed di modifiche, è possibile creare una visualizzazione materializzata e rendere persistenti i valori aggregati in Azure Cosmos DB. Se si usa Azure Cosmos DB per creare un gioco, è ad esempio possibile usare il feed delle modifiche per implementare classifiche in tempo reale in base ai punteggi delle partite completate.

## <a name="data-movement"></a>Spostamento dati

Puoi anche leggere dal feed delle modifiche per lo spostamento dei dati in tempo reale.

Ad esempio, il feed di modifiche consente di eseguire le attività seguenti in modo efficiente:

* Aggiornare una cache, un indice di ricerca o un data warehouse con i dati archiviati in Azure Cosmos DB.

* Eseguire migrazioni senza alcun tempo di inattività a un altro account Azure Cosmos o un altro contenitore di Azure Cosmos con una chiave di partizione logica diversa.

* Implementare un livello di livelli e un archivio dati a livello di applicazione. Ad esempio, è possibile archiviare "dati caldi" in Azure Cosmos DB e uscire da "dati freddi" in altri sistemi di archiviazione, ad esempio [Archiviazione BLOB](../storage/common/storage-introduction.md)di Azure .

Quando è necessario [denormalizzare i dati tra partizioni e contenitori,](how-to-model-partition-example.md#v2-introducing-denormalization-to-optimize-read-queries
)è possibile leggere dal feed di modifiche del contenitore come origine per questa replica dei dati. La replica dei dati in tempo reale con il feed di modifiche può garantire solo la coerenza finale. È possibile [monitorare l'ritardo del processore](how-to-use-change-feed-estimator.md) di avanzamento delle modifiche nell'elaborazione delle modifiche nel contenitore Cosmos.

## <a name="event-sourcing"></a>Origine evento

Il modello di [origine eventi](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) prevede l'utilizzo di un archivio di solo accodamento per registrare la serie completa di azioni su tali dati. Il feed delle modifiche di Azure Cosmos DB è un'ottima scelta come archivio dati centrale nelle architetture di origine eventi in cui l'inserimento di tutti i dati è modellato come scritture (nessun aggiornamento o eliminazione). In questo caso, ogni scrittura in Azure Cosmos DB è un "evento" e si dirà un record completo di eventi passati nel feed di modifiche. Gli usi tipici degli eventi pubblicati dall'archivio eventi centrale sono per la gestione di viste materializzate o per l'integrazione con sistemi esterni. Poiché non esiste alcun limite di tempo per la conservazione nel feed di modifiche, puoi riprodurre tutti gli eventi passati leggendo dall'inizio del feed di modifiche del contenitore Cosmos.

È possibile fare in modo che più consumer di feed di [modifiche sottoscrivano lo stesso feed di modifiche dello stesso contenitore.](how-to-create-multiple-cosmos-db-triggers.md#optimizing-containers-for-multiple-triggers) A parte la velocità effettiva di provisioning [del contenitore di lease,](change-feed-processor.md#components-of-the-change-feed-processor) l'utilizzo del feed di modifiche non prevede alcun costo. Il feed di modifiche è disponibile in ogni contenitore, indipendentemente dal fatto che venga utilizzato o meno.

Azure Cosmos DB è un archivio dati persistente di solo accodamento centrale nel modello di origine eventi a causa dei suoi punti di forza nella scalabilità orizzontale e nella disponibilità elevata. Inoltre, la libreria del processore del feed di modifiche offre una garanzia ["almeno una volta",](change-feed-processor.md#error-handling) assicurandoti di non perdere l'elaborazione di alcun evento.

## <a name="current-limitations"></a>Limitazioni correnti

Il feed di modifiche presenta importanti limitazioni che è necessario comprendere. Mentre gli elementi in un contenitore Cosmos rimarranno sempre nel feed di modifiche, il feed di modifiche non è un log delle operazioni completo. Esistono aree importanti da considerare quando si progetta un'applicazione che utilizza il feed di modifiche.

### <a name="intermediate-updates"></a>Aggiornamenti intermedi

Solo la modifica più recente per un determinato elemento è inclusa nel feed di modifiche. Durante l'elaborazione delle modifiche, si leggerà l'ultima versione dell'elemento disponibile. Se sono presenti più aggiornamenti per lo stesso elemento in un breve periodo di tempo, è possibile perdere l'elaborazione degli aggiornamenti intermedi. Se si desidera tenere traccia degli aggiornamenti ed essere in grado di riprodurre gli aggiornamenti precedenti di un elemento, è consigliabile modellare questi aggiornamenti come una serie di scritture.

### <a name="deletes"></a>Eliminazioni

Il feed di modifiche non acquisisce le eliminazioni. Se elimini un elemento dal contenitore, questo viene rimosso anche dal feed di modifiche. Il metodo più comune per gestire questa operazione consiste nell'aggiungere un indicatore soft agli elementi che vengono eliminati. È possibile aggiungere una proprietà denominata "deleted" e impostarla su "true" al momento dell'eliminazione. Questo aggiornamento del documento verrà visualizzato nel feed di modifiche. È possibile impostare un valore TTL su questo elemento in modo che possa essere eliminato automaticamente in un secondo momento.

### <a name="guaranteed-order"></a>Ordine garantito

È garantito l'ordine nel feed di modifiche all'interno di un valore della chiave di partizione, ma non tra i valori della chiave di partizione. È necessario selezionare una chiave di partizione che offre una garanzia di ordine significativa.

Si consideri, ad esempio, un'applicazione di vendita al dettaglio che utilizza il modello di progettazione di origine eventi. In questa applicazione, diverse azioni utente sono ogni "eventi" che sono modellati come scritture in Azure Cosmos DB. Si supponga che si siano verificati alcuni eventi di esempio nella sequenza seguente:Imagine if some example events occurred in the following sequence:

1. Cliente aggiunge l'articolo A al carrello
2. Cliente aggiunge l'articolo B al carrello
3. Il cliente aggiunge rimuove l'articolo A dal carrello
4. I check-out dei clienti e i contenuti del carrello vengono spediti

Una vista materializzata del contenuto corrente del carrello della spesa viene mantenuta per ogni cliente. Questa applicazione deve garantire che questi eventi vengano elaborati nell'ordine in cui si verificano. Se, ad esempio, il pagamento del carrello dovesse essere elaborato prima della rimozione dell'articolo A, è probabile che il cliente abbia dovuto spedire l'articolo A, anziché l'articolo B desiderato. Per garantire che questi quattro eventi vengano elaborati in ordine di occorrenza, devono rientrare nello stesso valore della chiave di partizione. Se si seleziona **nome utente** (ogni cliente ha un nome utente univoco) come chiave di partizione, è possibile garantire che questi eventi vengano visualizzati nel feed di modifiche nello stesso ordine in cui vengono scritti in Azure Cosmos DB.

## <a name="examples"></a>Esempi

Ecco alcuni esempi di codice del feed di modifiche reali che si estendono oltre l'ambito degli esempi forniti nei documenti Microsoft:

- [Introduzione al feed di modifiche](https://azurecosmosdb.github.io/labs/dotnet/labs/08-change_feed_with_azure_functions.html)
- [Caso d'uso IoT incentrato sul feed di modifiche](https://github.com/AzureCosmosDB/scenario-based-labs)
- [Caso d'uso di vendita al dettaglio centrato intorno al feed di modifiche](https://github.com/AzureCosmosDB/scenario-based-labs)

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica sul feed di modifiche](change-feed.md)
* [Opzioni per la lettura di feed di modifiche](read-change-feed.md)
* [Feed di modifiche con Funzioni di Azure](change-feed-functions.md)