---
title: Bilanciare il carico della partizione tra più istanze - Hub eventi di Azure Documenti Microsoft
description: Descrive come bilanciare il carico della partizione tra più istanze dell'applicazione usando un processore di eventi e l'SDK di Hubs eventi di Azure.Describes how to balance partition load across multiple instances of your application using an event processor and the Azure Event Hubs SDK.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/16/2020
ms.author: shvija
ms.openlocfilehash: bf90120157bf64bd62a3b5ec9d8a6b2c6260e024
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398305"
---
# <a name="balance-partition-load-across-multiple-instances-of-your-application"></a>Bilanciare il carico delle partizioni tra più istanze dell'applicazione
Per ridimensionare l'applicazione di elaborazione degli eventi, è possibile eseguire più istanze dell'applicazione e fare in modo che equilibri il carico tra di loro. Nelle versioni precedenti, [EventProcessorHost](event-hubs-event-processor-host.md) consente di bilanciare il carico tra più istanze del programma e gli eventi di checkpoint durante la ricezione. Nelle versioni più recenti (5.0 in poi), **EventProcessorClient** (.NET e Java) o **EventHubConsumerClient** (Python e JavaScript) consente di eseguire la stessa operazione. Il modello di sviluppo viene semplificato utilizzando gli eventi. Si sottoscrive gli eventi che ti interessano registrando un gestore eventi.

In questo articolo viene descritto uno scenario di esempio per l'utilizzo di più istanze per leggere eventi da un hub eventi e quindi vengono fornite informazioni dettagliate sulle funzionalità del client del processore di eventi, che consente di ricevere eventi da più partizioni contemporaneamente e il bilanciamento del carico con altri consumer che utilizzano lo stesso hub eventi e lo stesso gruppo di consumer.

> [!NOTE]
> La chiave per ridurre il numero di istanze di Hub eventi è l'idea di consumer partizionato. A differenza dei criteri relativi ai [consumer concorrenti](https://msdn.microsoft.com/library/dn568101.aspx), il modello consumer partizionato consente un'elevata scalabilità rimuovendo il collo di bottiglia dovuto alla contesa e agevolare il parallelismo end to end.

## <a name="example-scenario"></a>Scenario di esempio

Come scenario di esempio, si consideri una società per la sicurezza delle abitazioni che monitora 100.000 abitazioni. Ogni minuto, ottiene dati da vari sensori come un rilevatore di movimento, sensore aperto porta / finestra, rilevatore di rottura del vetro, e così via, installato in ogni casa. L'azienda fornisce un sito Web ai residenti per monitorare l'attività della propria abitazione quasi in tempo reale.

Ogni sensore esegue il push dei dati a un hub eventi. L'hub eventi è configurato con 16 partizioni. Dall'utente finale, è necessario un meccanismo in grado di leggere questi eventi, consolidarli (filtro, aggregazione e così via) ed eseguire il dump dell'aggregazione in un BLOB di archiviazione, che viene quindi proiettato in una pagina Web di facile utilizzo.

## <a name="write-the-consumer-application"></a>Scrivere l'applicazione consumer

Quando si progetta il consumer in un ambiente distribuito, lo scenario deve gestire i requisiti seguenti:

1. **Scalabilità:** creare diversi consumer, ciascuno tra questi che dispone di proprietà di lettura derivanti da alcune partizioni di Hub eventi.
2. **Bilanciamento del carico:** aumentare o ridurre in modo dinamico i consumer. Ad esempio, quando un nuovo tipo di sensore (ad esempio, un rilevatore di monossido di carbonio) viene aggiunto a ciascuna abitazione, il numero di eventi aumenta. In tal caso, l'operatore (un essere umano) aumenta il numero di istanze del consumer. Quindi, il pool di consumer può ribilanciare il numero di partizioni di cui è proprietario, condividendo il carico con i consumer appena aggiunti.
3. **Ripresa senza soluzione di continuità in caso di errori:** Se si verifica un errore in un consumer (**consumer A**) (ad esempio, la macchina virtuale che ospita il consumer si blocca improvvisamente), gli altri consumer possono prelevare le partizioni di proprietà del **consumer A** e continuare. Inoltre, il punto di continuazione, chiamato *checkpoint* oppure *offset*, deve trovarsi nel punto esatto in cui il **consumer A** ha avuto esito negativo, o leggermente prima rispetto al punto.
4. **Consumare eventi:** Mentre i tre punti precedenti riguardano la gestione del consumer, ci deve essere codice per utilizzare gli eventi e fare qualcosa di utile con esso. Ad esempio, aggregarlo e caricarlo nell'archiviazione BLOB.

## <a name="event-processor-or-consumer-client"></a>Processore di eventi o client consumer

Non è necessario creare una soluzione personalizzata per soddisfare questi requisiti. Gli SDK di Hub di eventi di Azure offrono questa funzionalità. In .NET o Java SDK, si utilizza un client del processore di eventi (EventProcessorClient) e in Python e Java Script SDK, si utilizza EventHubConsumerClient. Nella versione precedente di SDK, era l'host del processore di eventi (EventProcessorHost) che supportava queste funzionalità.

Per la maggior parte degli scenari di produzione, è consigliabile usare il client del processore di eventi per la lettura e l'elaborazione degli eventi. Il client del processore ha lo scopo di fornire un'esperienza affidabile per l'elaborazione di eventi in tutte le partizioni di un hub eventi in modo da eseguire e a tolleranza di errore, fornendo al contempo un mezzo per eseguire il checkpoint dello stato di avanzamento. I client del processore di eventi sono anche in grado di lavorare in modo cooperativo nel contesto di un gruppo di consumer per un determinato hub eventi. I client gestiranno automaticamente la distribuzione e il bilanciamento del lavoro non appena le istanze diventano disponibili o non disponibili per il gruppo.

## <a name="partition-ownership-tracking"></a>Rilevamento della proprietà di partizione

Un'istanza del processore di eventi in genere possiede ed elabora gli eventi da una o più partizioni. La proprietà delle partizioni viene distribuita in modo uniforme tra tutte le istanze del processore di eventi attivo associate a una combinazione di hub eventi e gruppo di consumer. 

A ogni elaboratore di eventi viene assegnato un identificatore univoco e la proprietà delle attestazioni delle partizioni aggiungendo o aggiornando una voce in un archivio di checkpoint. Tutte le istanze del processore di eventi comunicano periodicamente con questo archivio per aggiornare il proprio stato di elaborazione e per ottenere informazioni su altre istanze attive. Questi dati vengono quindi utilizzati per bilanciare il carico tra i processori attivi. Le nuove istanze possono unirsi al pool di elaborazione per aumentare le dimensioni. Quando le istanze si arrestano, a causa di errori o per la scalabilità verso il basso, la proprietà della partizione viene trasferita normalmente ad altri processori attivi.

I record di proprietà della partizione nell'archivio checkpoint tengono traccia dello spazio dei nomi degli hub eventi, del nome dell'hub eventi, del gruppo di consumer, dell'identificatore del processore di eventi (noto anche come proprietario), dell'ID di partizione e dell'ora dell'ultima modifica.



| Spazio dei nomi di Hub eventi               | Nome dell'hub eventi | **Gruppo di consumer** | Proprietario                                | Partition ID | Ora ultima modifica  |
| ---------------------------------- | -------------- | :----------------- | :----------------------------------- | :----------- | :------------------ |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 3be3f9d3-9d9e-4c50-9491-85ece8334ff6 | 0            | 2020-01-15T01:22:15 |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | f5cc5176-ce96-4bb4-bbaa-a0e3a9054ecf | 1            | 2020-01-15T01:22:17 |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 72b980e9-2efc-4ca7-ab1b-ffd7bece8472 | 2            | 2020-01-15T01:22:10 |
|                                    |                | :                  |                                      |              |                     |
|                                    |                | :                  |                                      |              |                     |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 844bd8fb-1f3a-4580-984d-6324f9e208af | 15           | 2020-01-15T01:22:00 |

Ogni istanza del processore di eventi acquisisce la proprietà di una partizione e avvia l'elaborazione della partizione dall'ultimo [checkpoint](# Checkpointing)noto. Se si verifica un errore in un processore (la macchina virtuale viene arrestata), le altre istanze lo rilevano esaminando l'ora dell'ultima modifica. Altre istanze tentano di ottenere la proprietà delle partizioni precedentemente possedute dall'istanza inattiva e l'archivio checkpoint garantisce che solo una delle istanze riesca a rivendicare la proprietà di una partizione. Quindi, in un dato momento, c'è al massimo un processore che riceve eventi da una partizione.

## <a name="receive-messages"></a>Ricevere messaggi

Quando si crea un processore di eventi, si specificano le funzioni che elaboreranno eventi ed errori. Ogni chiamata alla funzione che elabora gli eventi fornisce un singolo evento da una partizione specifica. È tua responsabilità gestire questo evento. Se si desidera assicurarsi che il consumer elabora ogni messaggio almeno una volta, è necessario scrivere il proprio codice con la logica di ripetizione dei tentativi. Fare attenzione, tuttavia, a inserire messaggi non elaborabili.

Si consiglia di fare le cose relativamente veloce. Cioè, fare meno elaborazione possibile. Se è necessario scrivere nell'archiviazione ed eseguire alcune operazioni di routing, è preferibile usare due gruppi di consumer e disporre di due processori di eventi.

## <a name="checkpointing"></a>Checkpoint

Il checkpoint è un processo *mediante* il quale un processore di eventi contrassegna o esegue il commit della posizione dell'ultimo evento elaborato correttamente all'interno di una partizione. Il contrassegno di un checkpoint viene in genere eseguito all'interno della funzione che elabora gli eventi e si verifica in base alla partizione all'interno di un gruppo di consumer. 

Se un processore di eventi si disconnette da una partizione, un'altra istanza può riprendere l'elaborazione della partizione al checkpoint di cui è stato eseguito il commit in precedenza dall'ultimo processore di tale partizione in tale gruppo di consumer. Quando il processore si connette, passa l'offset all'hub eventi per specificare la posizione in cui avviare la lettura. In questo modo, è possibile usare il checkpoint per contrassegnare gli eventi come "completi" dalle applicazioni downstream e per fornire resilienza quando un processore di eventi si arresta. È possibile tornare a dati precedenti specificando un offset inferiore da questo processo di checkpoint. 

Quando il checkpoint viene eseguito per contrassegnare un evento come elaborato, una voce nell'archivio checkpoint viene aggiunta o aggiornata con l'offset e il numero di sequenza dell'evento. Gli utenti devono decidere la frequenza di aggiornamento del checkpoint. L'aggiornamento dopo ogni evento elaborato correttamente può avere implicazioni in termini di prestazioni e costi in quanto attiva un'operazione di scrittura nell'archivio checkpoint sottostante. Inoltre, il checkpoint di ogni singolo evento è indicativo di un modello di messaggistica in coda per il quale una coda del bus di servizio potrebbe essere un'opzione migliore rispetto a un hub eventi. L'idea alla base di Hub eventi è di ottenere "almeno un" recapito su larga scala. Per rendere idempotenti i sistemi a valle, è facile eseguire il ripristino a seguito di errori o il riavvio del risultato negli stessi eventi ricevuti più volte.

> [!NOTE]
> Se si usa Archiviazione BLOB di Azure come archivio di checkpoint in un ambiente che supporta una versione diversa di Storage Blob SDK rispetto a quelle in genere disponibili in Azure, è necessario usare il codice per modificare la versione dell'API del servizio di archiviazione alla versione specifica supportata da tale ambiente. Ad esempio, se si eseguono hub eventi in una versione 2002 di Hub di Azure Stack, la versione più alta disponibile per il servizio di archiviazione è la versione 2017-11-09.For example, if you are running [Event Hubs on an Azure Stack Hub version 2002](https://docs.microsoft.com/azure-stack/user/event-hubs-overview), the highest available version for the Storage service is version 2017-11-09. In questo caso, è necessario usare il codice per la versione dell'API del servizio di archiviazione a 2017-11-09.In this case, you need to use code to target the Storage service API version to 2017-11-09. Per un esempio su come scegliere come destinazione una versione dell'API di archiviazione specifica, vedere questi esempi in GitHub:For an example on how to target a specific Storage API version, see these samples on GitHub: 
> - [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample10_RunningWithDifferentStorageVersion.cs). 
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithOlderStorageVersion.java)
> - [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsWithDownleveledStorage.js) o [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsWithDownleveledStorage.ts)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/event_processor_blob_storage_example_with_storage_api_version.py)

## <a name="thread-safety-and-processor-instances"></a>Istanze di elaborazione e sicurezza del thread

Per impostazione predefinita, il processore di eventi o il consumer è thread-safe e si comporta in modo sincrono. Quando arrivano eventi per una partizione, viene chiamata la funzione che elabora gli eventi. I messaggi e le chiamate successivi a questa funzione vengono accodati dietro le quinte mentre il message pump continua a essere eseguito in background su altri thread. Questa sicurezza per i thread elimina la necessità di raccolte thread-safe e un aumenta significativamente le prestazioni.

## <a name="next-steps"></a>Passaggi successivi
Vedere le seguenti guide introduttive:

- [.NET Core](get-started-dotnet-standard-send-v2.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](get-started-python-send-v2.md)
- [JavaScript](get-started-node-send-v2.md)
