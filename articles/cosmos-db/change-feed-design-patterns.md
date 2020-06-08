---
title: Schemi progettuali dei feed di modifiche in Azure Cosmos DB
description: Panoramica degli Schemi progettuali dei feed di modifiche comuni
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: abbf5d79da033a696890566d85bd24bb54577d2c
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715699"
---
# <a name="change-feed-design-patterns-in-azure-cosmos-db"></a>Schemi progettuali dei feed di modifiche in Azure Cosmos DB

Il feed di modifiche di Azure Cosmos DB consente l'elaborazione efficiente di set di dati di grandi dimensioni con un volume di scritture elevato. Il feed di modifiche offre anche un'alternativa all'esecuzione di query su un intero set di dati per identificare le modifiche apportate. Questo documento è incentrato sugli schemi progettuali dei feed di modifiche comuni, sui compromessi di progettazione e sulle limitazioni del feed di modifiche.

Azure Cosmos DB è particolarmente adatto per le applicazioni IoT, di videogiochi, del settore della vendita al dettaglio e di registrazioni di operazioni. Uno schema progettuale comune in queste applicazioni prevede l'uso delle modifiche ai dati per attivare azioni aggiuntive. Esempi di azioni aggiuntive:

* Attivazione di una notifica o di una chiamata a un'API quando un elemento viene inserito o modificato.
* Elaborazione di flussi in tempo reale per l'elaborazione IoT o di analisi in tempo reale sui dati operativi.
* Spostamento dati, ad esempio la sincronizzazione con una cache, un motore di ricerca, un data warehouse o un'archiviazione offline sicura.

Il feed di modifiche in Azure Cosmos DB consente di creare soluzioni efficienti e scalabili per ognuno di questi modelli, come illustrato nell'immagine seguente:

![Uso del feed delle modifiche di Azure Cosmos DB per agevolare le analisi in tempo reale e gli scenari di calcolo guidati dagli eventi](./media/change-feed/changefeedoverview.png)

## <a name="event-computing-and-notifications"></a>Elaborazione degli eventi e notifiche

Il feed di modifiche di Azure Cosmos DB può semplificare gli scenari in cui è necessario attivare una notifica o inviare una chiamata a un'API in base a un determinato evento. È possibile usare la [libreria dei processi dei feed di modifiche](change-feed-processor.md) per eseguire automaticamente il polling delle modifiche nel contenitore e chiamare un'API esterna ogni volta che si verifica una scrittura o un aggiornamento.

È anche possibile attivare selettivamente una notifica o inviare una chiamata a un'API in base a criteri specifici. Ad esempio, se viene eseguita la lettura dal feed di modifiche usando [Funzioni di Azure](change-feed-functions.md), è possibile inserire la logica nella funzione per inviare una notifica solo se sono stati soddisfatti criteri specifici. Mentre il codice della funzione di Azure viene eseguito durante ogni scrittura e aggiornamento, la notifica viene inviata solo se sono stati soddisfatti criteri specifici.

## <a name="real-time-stream-processing"></a>Elaborazione del flusso in tempo reale

Il feed di modifiche di Azure Cosmos DB può essere usato per l'elaborazione del flusso in tempo reale per IoT o l'elaborazione analitica in tempo reale nei dati operativi.
Ad esempio, è possibile ricevere e archiviare i dati degli eventi da dispositivi, sensori, infrastrutture e applicazioni ed elaborare gli eventi in tempo reale usando [Spark](../hdinsight/spark/apache-spark-overview.md). L'immagine seguente mostra come implementare un'architettura lambda usando Azure Cosmos DB tramite il feed di modifiche:

![Pipeline lambda basate su Azure Cosmos DB per l'inserimento e le query](./media/change-feed/lambda.png)

In molti casi, le implementazioni dell'elaborazione del flusso ricevono innanzitutto un volume elevato di dati in ingresso in una coda di messaggi temporanea, ad esempio l'hub eventi di Azure o Apache Kafka. Il feed di modifiche è un'ottima alternativa grazie alla capacità di Azure Cosmos DB di supportare una frequenza elevata prolungata di inserimento di dati con latenza di lettura e scrittura ridotta garantita. I vantaggi del feed di modifiche di Azure Cosmos DB in una coda di messaggi sono i seguenti:

### <a name="data-persistence"></a>Salvataggio permanente dei dati

I dati scritti in Azure Cosmos DB verranno visualizzati nel feed di modifiche e conservati fino a quando non vengono eliminati. Le code di messaggi in genere hanno un periodo di conservazione massimo. Ad esempio, l'[hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/) offre una conservazione dati massima di 90 giorni.

### <a name="querying-ability"></a>Funzionalità di query

Oltre a leggere dal feed di modifiche di un contenitore Cosmos, è anche possibile eseguire query SQL nei dati archiviati in Azure Cosmos DB. Il feed di modifiche non è una duplicazione dei dati già presenti nel contenitore, ma piuttosto un diverso meccanismo di lettura dei dati. Pertanto, se vengono letti dal feed di modifiche, i dati saranno sempre coerenti con le query dello stesso contenitore di Azure Cosmos DB.

### <a name="high-availability"></a>Disponibilità elevata

Azure Cosmos DB offre una disponibilità massima in lettura e scrittura del 99,999%. A differenza di molte code di messaggi, i dati di Azure Cosmos DB possono essere distribuiti e configurati facilmente a livello globale con un [obiettivo del tempo di ripristino (RTO)](consistency-levels-tradeoffs.md#rto) uguale a zero.

Dopo aver elaborato gli elementi nel feed di modifiche, è possibile creare una vista materializzata e mantenere i valori aggregati in Azure Cosmos DB. Se si usa Azure Cosmos DB per creare un gioco, è ad esempio possibile usare il feed delle modifiche per implementare classifiche in tempo reale in base ai punteggi delle partite completate.

## <a name="data-movement"></a>Spostamento dati

È anche possibile leggere dal feed di modifiche per lo spostamento dati in tempo reale.

Ad esempio, il feed di modifiche consente di eseguire in modo efficiente le attività seguenti:

* Aggiornare una cache, un indice di ricerca o un data warehouse con i dati archiviati in Azure Cosmos DB.

* Eseguire migrazioni senza alcun tempo di inattività a un altro account Azure Cosmos o un altro contenitore di Azure Cosmos con una chiave di partizione logica diversa.

* Implementare la suddivisione in livelli e l'archiviazione dei dati a livello di applicazione. Ad esempio, è possibile archiviare i "dati ad accesso frequente" in Azure Cosmos DB ed escludere i "dati ad accesso sporadico" in altri sistemi di archiviazione, ad esempio [Archiviazione BLOB di Azure](../storage/common/storage-introduction.md).

Quando è necessario [denormalizzare i dati tra partizioni e contenitori](how-to-model-partition-example.md#v2-introducing-denormalization-to-optimize-read-queries
), è possibile leggere dal feed di modifiche del contenitore come origine per la replica dei dati. La replica dei dati in tempo reale con il feed di modifiche può garantire solo la coerenza finale. È possibile [monitorare il ritardo del processore dei feed di modifiche](how-to-use-change-feed-estimator.md) nell'elaborazione delle modifiche nel contenitore Cosmos.

## <a name="event-sourcing"></a>Origine degli eventi

Lo [schema di origine degli eventi](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) prevede l'uso di un archivio di solo accodamento per registrare la serie completa di azioni nei dati. Il feed di modifiche di Azure Cosmos DB è un'ottima scelta come archivio dati centrale nelle architetture di origine degli eventi in cui tutti gli inserimenti dati vengono modellati come scritture (senza aggiornamenti o eliminazioni). In questo caso, ogni scrittura in Azure Cosmos DB è un "evento" e nel feed di modifiche sono registrati tutti gli eventi passati. Gli eventi pubblicati dall'archivio eventi centrale vengono solitamente usati per la gestione delle viste materializzate o per l'integrazione con sistemi esterni. Poiché non esiste alcun limite di tempo per la conservazione nel feed di modifiche, è possibile riprodurre tutti gli eventi passati leggendo dall'inizio il feed di modifiche del contenitore Cosmos.

È possibile che [più consumer eseguano una sottoscrizione allo stesso feed di modifiche del contenitore](how-to-create-multiple-cosmos-db-triggers.md#optimizing-containers-for-multiple-triggers). Indipendentemente dalla velocità effettiva con provisioning del [contenitore del lease](change-feed-processor.md#components-of-the-change-feed-processor), non viene addebitato alcun costo per l'utilizzo del feed di modifiche. Il feed di modifiche è disponibile in ogni contenitore indipendentemente dal fatto che venga usato.

Azure Cosmos DB è un ottimo archivio dati centrale persistente di solo accodamento nello schema di origine eventi grazie ai vantaggi della scalabilità orizzontale e della disponibilità elevata. Inoltre, la libreria del processore dei feed di modifiche garantisce ["almeno un'elaborazione"](change-feed-processor.md#error-handling), assicurando l'elaborazione di tutti gli eventi.

## <a name="current-limitations"></a>Limitazioni correnti

Il feed di modifiche ha importanti limitazioni che è necessario conoscere. Sebbene gli elementi di un contenitore Cosmos rimangano sempre nel feed di modifiche, il feed di modifiche non è un log delle operazioni completo. Quando si progetta un'applicazione che utilizza il feed di modifiche è necessario considerare alcune aree importanti.

### <a name="intermediate-updates"></a>Aggiornamenti intermedi

Solo la modifica più recente di un determinato elemento viene inclusa nel feed di modifiche. Quando si elaborano le modifiche, viene letta la versione più recente dell'elemento disponibile. Se sono presenti più aggiornamenti dello stesso elemento in un breve periodo di tempo, è possibile che gli aggiornamenti intermedi non vengano elaborati. Se si vuole tenere traccia degli aggiornamenti ed essere in grado di riprodurre gli aggiornamenti precedenti di un elemento, è consigliabile modellare gli aggiornamenti come una serie di scritture.

### <a name="deletes"></a>Eliminazioni

Il feed di modifiche non acquisisce le eliminazioni. Se si elimina un elemento dal contenitore, l'elemento viene rimosso anche dal feed di modifiche. Per risolvere questo problema viene in genere aggiunto un soft marker negli elementi che vengono eliminati. È possibile aggiungere una proprietà denominata "eliminato" e impostarla su "true" al momento dell'eliminazione. Questo aggiornamento del documento verrà visualizzato nel feed di modifiche. È possibile impostare un valore TTL nell'elemento in modo che possa essere eliminato automaticamente in un secondo momento.

### <a name="guaranteed-order"></a>Ordine garantito

Esiste un ordine garantito nel feed di modifiche in un valore della chiave di partizione ma non tra i valori della chiave di partizione. È necessario selezionare una chiave di partizione che offra una garanzia di ordine significativa.

Si consideri, ad esempio, un'applicazione per la vendita al dettaglio che usa lo schema progettuale di origine degli eventi. In questa applicazione diverse azioni utente sono "eventi" che vengono modellati come scritture in Azure Cosmos DB. Si supponga che alcuni eventi di esempio si siano verificati nella sequenza seguente:

1. Il cliente aggiunge l'elemento A al carrello acquisti
2. Il cliente aggiunge l'elemento B al carrello acquisti
3. Il cliente rimuove l'elemento A dal carrello acquisti
4. Il cliente esegue il checkout e il contenuto del carrello acquisti viene spedito

Viene conservata una vista materializzata del contenuto corrente del carrello acquisti per ogni cliente. L'applicazione deve garantire che gli eventi vengano elaborati nell'ordine in cui si verificano. Ad esempio, se il checkout del carrello foste stato eseguito prima della rimozione dell'elemento A, probabilmente il cliente avrebbe ricevuto l'elemento A anziché l'elemento B desiderato. Per assicurarsi che questi quattro eventi vengano elaborati nell'ordine in cui si sono verificati, è necessario che rientrino nello stesso valore della chiave di partizione. Se si seleziona il **nome utente** (ogni cliente ha un nome utente univoco) come chiave di partizione, è possibile assicurarsi che questi eventi vengano visualizzati nel feed di modifiche nello stesso ordine in cui vengono scritti in Azure Cosmos DB.

## <a name="examples"></a>Esempi

Gli esempi di codice del feed di modifiche seguenti si estendono oltre l'ambito degli esempi forniti nella documentazione Microsoft:

- [Introduzione al feed di modifiche](https://azurecosmosdb.github.io/labs/dotnet/labs/08-change_feed_with_azure_functions.html)
- [Caso d'uso IoT centrato sul feed di modifiche](https://github.com/AzureCosmosDB/scenario-based-labs)
- [Caso d'uso di vendita al dettaglio centrato sul feed di modifiche](https://github.com/AzureCosmosDB/scenario-based-labs)

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica sul feed di modifiche](change-feed.md)
* [Opzioni per la lettura di feed di modifiche](read-change-feed.md)
* [Feed di modifiche con Funzioni di Azure](change-feed-functions.md)
