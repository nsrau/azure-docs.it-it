---
title: Modelli di progettazione di feed di modifiche in Azure Cosmos DB
description: Panoramica dei modelli di progettazione del feed di modifiche comuni
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 012d27b44ecfbdd460adf241742df397880f78c6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81450352"
---
# <a name="change-feed-design-patterns-in-azure-cosmos-db"></a>Modelli di progettazione di feed di modifiche in Azure Cosmos DB

Il feed di modifiche Azure Cosmos DB consente l'elaborazione efficiente di set di impostazioni di grandi dimensioni con un volume elevato di Scritture. Il feed di modifiche offre anche un'alternativa all'esecuzione di query su un intero set di dati per identificare le modifiche apportate. Questo documento è incentrato sui modelli di progettazione del feed di modifiche comuni, sui compromessi di progettazione e sulle limitazioni del feed di modifiche.

Azure Cosmos DB è particolarmente adatto per le applicazioni IoT, di videogiochi, del settore della vendita al dettaglio e di registrazioni di operazioni. Uno schema progettuale comune in queste applicazioni prevede l'uso delle modifiche ai dati per attivare azioni aggiuntive. Esempi di azioni aggiuntive:

* Attivazione di una notifica o di una chiamata a un'API quando un elemento viene inserito o modificato.
* Elaborazione di flussi in tempo reale per l'elaborazione IoT o di analisi in tempo reale sui dati operativi.
* Spostamento dei dati, ad esempio la sincronizzazione con una cache, un motore di ricerca, un data warehouse o un'archiviazione a freddo.

Il feed di modifiche in Azure Cosmos DB consente di creare soluzioni efficienti e scalabili per ognuno di questi modelli, come illustrato nell'immagine seguente:

![Uso del feed delle modifiche di Azure Cosmos DB per agevolare le analisi in tempo reale e gli scenari di calcolo guidati dagli eventi](./media/change-feed/changefeedoverview.png)

## <a name="event-computing-and-notifications"></a>Elaborazione di eventi e notifiche

Il feed di modifiche Azure Cosmos DB può semplificare gli scenari in cui è necessario attivare una notifica o una chiamata a un'API in base a un determinato evento. È possibile usare la [libreria del processo del feed delle modifiche](change-feed-processor.md) per eseguire automaticamente il polling delle modifiche del contenitore e chiamare un'API esterna ogni volta che è presente una scrittura o un aggiornamento.

È anche possibile attivare selettivamente una notifica o inviare una chiamata a un'API in base a criteri specifici. Se ad esempio si esegue la lettura dal feed delle modifiche usando [funzioni di Azure](change-feed-functions.md), è possibile inserire la logica nella funzione per inviare una notifica solo se sono stati soddisfatti criteri specifici. Mentre il codice della funzione di Azure viene eseguito durante ogni scrittura e aggiornamento, la notifica viene inviata solo se sono stati soddisfatti criteri specifici.

## <a name="real-time-stream-processing"></a>Elaborazione di flussi in tempo reale

Il feed di modifiche Azure Cosmos DB può essere usato per l'elaborazione di flussi in tempo reale per l'elaborazione di analisi in tempo reale o per i dati operativi.
Ad esempio, è possibile ricevere e archiviare i dati degli eventi da dispositivi, sensori, infrastruttura e applicazioni ed elaborare questi eventi in tempo reale usando [Spark](../hdinsight/spark/apache-spark-overview.md). La figura seguente illustra come implementare un'architettura lambda usando il Azure Cosmos DB tramite il feed delle modifiche:

![Pipeline lambda basate su Azure Cosmos DB per l'inserimento e le query](./media/change-feed/lambda.png)

In molti casi, le implementazioni di elaborazione del flusso ricevono prima di tutto un volume elevato di dati in ingresso in una coda di messaggi temporanea, ad esempio hub eventi di Azure o Apache Kafka. Il feed di modifiche è un'ottima alternativa grazie alla capacità Azure Cosmos DB di supportare una frequenza elevata prolungata di inserimento di dati con latenza di lettura e scrittura ridotta garantita. I vantaggi del feed di modifiche Azure Cosmos DB in una coda di messaggi includono:

### <a name="data-persistence"></a>Salvataggio permanente dei dati

I dati scritti in Azure Cosmos DB verranno visualizzati nel feed delle modifiche e conservati fino a quando non vengono eliminati. Le code di messaggi in genere hanno un periodo di conservazione massimo. [Hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/) , ad esempio, offre una conservazione dei dati massima di 90 giorni.

### <a name="querying-ability"></a>Funzionalità di query

Oltre a leggere dal feed di modifiche di un contenitore Cosmos, è anche possibile eseguire query SQL sui dati archiviati in Azure Cosmos DB. Il feed di modifiche non è una duplicazione di dati già presenti nel contenitore, ma piuttosto un meccanismo diverso per la lettura dei dati. Pertanto, se si leggono i dati dal feed delle modifiche, sarà sempre coerente con le query dello stesso contenitore Azure Cosmos DB.

### <a name="high-availability"></a>Disponibilità elevata

Azure Cosmos DB offre fino al 99,999% di disponibilità in lettura e scrittura. A differenza di molte code di messaggi, Azure Cosmos DB dati possono essere distribuiti facilmente a livello globale e configurati con un [RTO (obiettivo del tempo di ripristino)](consistency-levels-tradeoffs.md#rto) pari a zero.

Una volta elaborati gli elementi nel feed delle modifiche, è possibile creare una vista materializzata e mantenere i valori aggregati nuovamente in Azure Cosmos DB. Se si usa Azure Cosmos DB per creare un gioco, è ad esempio possibile usare il feed delle modifiche per implementare classifiche in tempo reale in base ai punteggi delle partite completate.

## <a name="data-movement"></a>Spostamento dati

È anche possibile leggere dal feed delle modifiche per lo spostamento di dati in tempo reale.

Il feed di modifiche, ad esempio, consente di eseguire in modo efficiente le seguenti attività:

* Aggiornare una cache, un indice di ricerca o data warehouse con i dati archiviati nel Azure Cosmos DB.

* Eseguire migrazioni senza alcun tempo di inattività a un altro account Azure Cosmos o un altro contenitore di Azure Cosmos con una chiave di partizione logica diversa.

* Implementare un'archiviazione e una suddivisione in livelli dei dati a livello di applicazione. Ad esempio, è possibile archiviare i "dati attivi" in Azure Cosmos DB ed escludere "dati a freddo" in altri sistemi di archiviazione, ad esempio [archiviazione BLOB di Azure](../storage/common/storage-introduction.md).

Quando è necessario [denormalizzare i dati tra partizioni e contenitori](how-to-model-partition-example.md#v2-introducing-denormalization-to-optimize-read-queries
), è possibile leggere dal feed di modifiche del contenitore come origine per la replica dei dati. La replica dei dati in tempo reale con il feed delle modifiche può garantire la coerenza finale. È possibile [monitorare la distanza con cui il processore del feed di modifiche](how-to-use-change-feed-estimator.md) è in ritardo nell'elaborazione delle modifiche nel contenitore Cosmos.

## <a name="event-sourcing"></a>Origine eventi

Il [modello](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) di origine eventi prevede l'uso di un archivio di solo accodamento per registrare la serie completa di azioni su tali dati. Il feed di modifiche di Azure Cosmos DB è un'ottima scelta come archivio dati centrale nelle architetture di origine eventi in cui tutti i dati vengono modellati come Scritture (senza aggiornamenti o eliminazioni). In questo caso, ogni operazione di scrittura nel Azure Cosmos DB è un "evento" ed è presente un record completo degli eventi passati nel feed delle modifiche. Gli usi tipici degli eventi pubblicati dall'archivio centrale degli eventi sono per la manutenzione di viste materializzate o per l'integrazione con sistemi esterni. Poiché non esiste alcun limite di tempo per la conservazione nel feed delle modifiche, è possibile riprodurre tutti gli eventi passati leggendo dall'inizio del feed di modifiche del contenitore Cosmos.

È possibile fare [in modo che più consumer del feed di modifiche sottoscrivano il feed di modifiche del contenitore stesso](how-to-create-multiple-cosmos-db-triggers.md#optimizing-containers-for-multiple-triggers). Oltre alla velocità effettiva di provisioning del [contenitore di lease](change-feed-processor.md#components-of-the-change-feed-processor) , non è previsto alcun costo per l'uso del feed delle modifiche. Il feed delle modifiche è disponibile in ogni contenitore indipendentemente dal fatto che venga utilizzato.

Azure Cosmos DB è un ottimo archivio dati persistente di sola Accodamento nel modello di origine eventi a causa dei vantaggi della scalabilità orizzontale e della disponibilità elevata. Inoltre, la libreria del processore dei feed delle modifiche offre una garanzia [di "almeno una volta"](change-feed-processor.md#error-handling) , assicurando che non si perda l'elaborazione di alcun evento.

## <a name="current-limitations"></a>Limitazioni correnti

Il feed di modifiche ha importanti limitazioni da comprendere. Mentre gli elementi in un contenitore Cosmos rimarranno sempre nel feed delle modifiche, il feed delle modifiche non è un log delle operazioni completo. Quando si progetta un'applicazione che utilizza il feed delle modifiche, è necessario considerare alcune aree importanti.

### <a name="intermediate-updates"></a>Aggiornamenti intermedi

Nel feed di modifiche viene inclusa solo la modifica più recente per un determinato elemento. Quando si elaborano le modifiche, viene letta la versione più recente dell'elemento disponibile. Se sono presenti più aggiornamenti per lo stesso elemento in un breve periodo di tempo, è possibile che si verifichi la mancata elaborazione degli aggiornamenti intermedi. Se si desidera tenere traccia degli aggiornamenti ed essere in grado di riprodurre gli aggiornamenti passati a un elemento, è consigliabile modellare questi aggiornamenti come una serie di Scritture.

### <a name="deletes"></a>Eliminazioni

Il feed delle modifiche non acquisisce le eliminazioni. Se si elimina un elemento dal contenitore, questo viene rimosso anche dal feed delle modifiche. Il metodo di gestione più comune è l'aggiunta di un marcatore Soft sugli elementi che vengono eliminati. È possibile aggiungere una proprietà denominata "Deleted" e impostarla su "true" al momento dell'eliminazione. Questo aggiornamento del documento verrà visualizzato nel feed delle modifiche. È possibile impostare un valore TTL in questo elemento in modo che possa essere eliminato automaticamente in un secondo momento.

### <a name="guaranteed-order"></a>Ordine garantito

Il feed delle modifiche in un valore della chiave di partizione è un ordine garantito, ma non tra i valori della chiave di partizione. È necessario selezionare una chiave di partizione che fornisca una garanzia di ordine significativa.

Si consideri, ad esempio, un'applicazione per la vendita al dettaglio usando lo schema progettuale di sourcing In questa applicazione, diverse azioni utente sono costituite da "eventi", che vengono modellate come Scritture Azure Cosmos DB. Si supponga che alcuni eventi di esempio si siano verificati nella sequenza seguente:

1. Il cliente aggiunge l'elemento a al carrello della spesa
2. Il cliente aggiunge l'elemento B al carrello della spesa
3. Il cliente rimuove l'elemento A dal carrello della spesa
4. Il cliente estrae e il contenuto del carrello acquisti viene fornito

Per ogni cliente viene mantenuta una vista materializzata del contenuto del carrello acquisti corrente. Questa applicazione deve garantire che questi eventi vengano elaborati nell'ordine in cui si verificano. Se, ad esempio, l'estrazione del carrello veniva elaborata prima della rimozione dell'elemento A, è probabile che il cliente avesse un elemento spedito, anziché l'elemento B desiderato. Per garantire che questi quattro eventi vengano elaborati in ordine di occorrenza, devono rientrare nello stesso valore della chiave di partizione. Se si seleziona **username** (ogni cliente ha un nome utente univoco) come chiave di partizione, è possibile garantire che questi eventi vengano visualizzati nel feed delle modifiche nello stesso ordine in cui vengono scritti in Azure Cosmos DB.

## <a name="examples"></a>Esempi

Ecco alcuni esempi di codice del feed di modifiche reali che si estendono oltre l'ambito degli esempi forniti in Microsoft Docs:

- [Introduzione al feed delle modifiche](https://azurecosmosdb.github.io/labs/dotnet/labs/08-change_feed_with_azure_functions.html)
- [Caso d'uso del tutto centrato sul feed delle modifiche](https://github.com/AzureCosmosDB/scenario-based-labs)
- [Caso d'uso al dettaglio centrato sul feed delle modifiche](https://github.com/AzureCosmosDB/scenario-based-labs)

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica sul feed di modifiche](change-feed.md)
* [Opzioni per la lettura di feed di modifiche](read-change-feed.md)
* [Feed di modifiche con Funzioni di Azure](change-feed-functions.md)