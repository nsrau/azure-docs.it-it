---
title: Bilanciare il carico delle partizioni tra più istanze-Hub eventi di Azure | Microsoft Docs
description: Viene descritto come bilanciare il carico delle partizioni tra più istanze dell'applicazione usando un processore di eventi e l'SDK di hub eventi di Azure.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 8bf3f05b823a784f4f3fc2074719ed346f769f5e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88933794"
---
# <a name="balance-partition-load-across-multiple-instances-of-your-application"></a>Bilanciare il carico delle partizioni tra più istanze dell'applicazione
Per ridimensionare l'applicazione di elaborazione degli eventi, è possibile eseguire più istanze dell'applicazione e bilanciare il carico tra loro. Nelle versioni precedenti, [EventProcessorHost](event-hubs-event-processor-host.md) consentiva di bilanciare il carico tra più istanze del programma e gli eventi di checkpoint durante la ricezione. Nelle versioni più recenti (5,0 in poi), **EventProcessorClient** (.NET e Java) o **EventHubConsumerClient** (Python e JavaScript) consente di eseguire la stessa operazione. Il modello di sviluppo viene reso più semplice usando gli eventi. Per sottoscrivere gli eventi a cui si è interessati, è necessario registrare un gestore eventi.

Questo articolo descrive uno scenario di esempio per l'uso di più istanze per leggere gli eventi da un hub eventi e quindi fornire informazioni dettagliate sulle funzionalità del client processore di eventi, che consente di ricevere eventi da più partizioni contemporaneamente e di bilanciare il carico con altri consumer che usano lo stesso hub eventi e il gruppo di consumer.

> [!NOTE]
> La chiave per ridurre il numero di istanze di Hub eventi è l'idea di consumer partizionato. A differenza dei criteri relativi ai [consumer concorrenti](/previous-versions/msp-n-p/dn568101(v=pandp.10)), il modello consumer partizionato consente un'elevata scalabilità rimuovendo il collo di bottiglia dovuto alla contesa e agevolare il parallelismo end to end.

## <a name="example-scenario"></a>Scenario di esempio

Come scenario di esempio, si consideri una società per la sicurezza delle abitazioni che monitora 100.000 abitazioni. Ogni minuto ottiene i dati da diversi sensori, ad esempio un rilevatore di movimento, un sensore aperto sportello/finestra, un rilevatore di interruzioni di vetro e così via, installato in ogni Home. L'azienda fornisce un sito Web ai residenti per monitorare l'attività della propria abitazione quasi in tempo reale.

Ogni sensore esegue il push dei dati a un hub eventi. L'hub eventi è configurato con 16 partizioni. Sul lato utilizzo è necessario un meccanismo in grado di leggere questi eventi, consolidarli (filtro, aggregazione e così via) ed eseguire il dump dell'aggregazione in un BLOB di archiviazione, che viene quindi proiettato in una pagina Web intuitiva.

## <a name="write-the-consumer-application"></a>Scrivere l'applicazione consumer

Quando si progetta il consumer in un ambiente distribuito, lo scenario deve gestire i requisiti seguenti:

1. **Scalabilità:** creare diversi consumer, ciascuno tra questi che dispone di proprietà di lettura derivanti da alcune partizioni di Hub eventi.
2. **Bilanciamento del carico:** aumentare o ridurre in modo dinamico i consumer. Ad esempio, quando un nuovo tipo di sensore (ad esempio, un rilevatore di monossido di carbonio) viene aggiunto a ciascuna abitazione, il numero di eventi aumenta. In tal caso, l'operatore (un essere umano) aumenta il numero di istanze del consumer. Quindi, il pool di consumer può ribilanciare il numero di partizioni di cui è proprietario, condividendo il carico con i consumer appena aggiunti.
3. **Ripresa trasparente in caso di errori:** Se un consumer (**consumer a**) ha esito negativo (ad esempio, la macchina virtuale che ospita il consumer si arresta improvvisamente), altri utenti possono scegliere le partizioni di proprietà del **consumer a** e continuare. Inoltre, il punto di continuazione, chiamato *checkpoint* oppure *offset*, deve trovarsi nel punto esatto in cui il **consumer A** ha avuto esito negativo, o leggermente prima rispetto al punto.
4. **USA eventi:** Sebbene i tre punti precedenti gestiscano la gestione del consumer, è necessario che sia presente codice per utilizzare gli eventi ed eseguire un'operazione utile. Ad esempio, aggregarlo e caricarlo nell'archivio BLOB.

## <a name="event-processor-or-consumer-client"></a>Processore di eventi o client consumer

Non è necessario creare una soluzione personalizzata per soddisfare questi requisiti. Gli SDK di hub eventi di Azure forniscono questa funzionalità. Negli SDK .NET o Java si usa un client di elaborazione eventi (EventProcessorClient) e negli SDK Python e JavaScript si usa EventHubConsumerClient. Nella versione precedente di SDK, era l'host processore di eventi (EventProcessorHost) che supportava queste funzionalità.

Per la maggior parte degli scenari di produzione, è consigliabile usare il client processore di eventi per la lettura e l'elaborazione degli eventi. Il client del processore è progettato per offrire un'esperienza affidabile per l'elaborazione di eventi in tutte le partizioni di un hub eventi in modo efficiente e a tolleranza di errore, fornendo allo stesso tempo un mezzo per il checkpoint dello stato di avanzamento. I client del processore di eventi sono inoltre in grado di funzionare in modo cooperativo all'interno del contesto di un gruppo di consumer per un determinato Hub eventi. I client gestiranno automaticamente la distribuzione e il bilanciamento del lavoro man mano che le istanze diventano disponibili o non disponibili per il gruppo.

## <a name="partition-ownership-tracking"></a>Rilevamento della proprietà di partizione

Un'istanza del processore di eventi in genere possiede ed elabora gli eventi da una o più partizioni. La proprietà delle partizioni viene distribuita uniformemente tra tutte le istanze del processore di eventi attive associate a una combinazione di hub eventi e gruppo di consumer. 

A ogni processore di eventi viene assegnato un identificatore univoco e la relativa proprietà delle partizioni mediante l'aggiunta o l'aggiornamento di una voce in un archivio di checkpoint. Tutte le istanze del processore di eventi comunicano con questo archivio periodicamente per aggiornare il proprio stato di elaborazione e per ottenere informazioni su altre istanze attive. Questi dati vengono quindi utilizzati per bilanciare il carico tra i processori attivi. Le nuove istanze possono partecipare al pool di elaborazione per la scalabilità verticale. Quando le istanze vengono arrestate, a causa di errori o di riduzione, la proprietà della partizione viene normalmente trasferita ad altri processori attivi.

I record di proprietà della partizione nell'archivio Checkpoint tengono traccia dello spazio dei nomi di hub eventi, del nome dell'hub eventi, del gruppo di consumer, dell'identificatore del processore di eventi (noto anche come proprietario), dell'ID partizione e dell'ora dell'Ultima modifica



| Spazio dei nomi di Hub eventi               | Nome dell'hub eventi | **Gruppo di consumer** | Proprietario                                | Partition ID | Ora ultima modifica  |
| ---------------------------------- | -------------- | :----------------- | :----------------------------------- | :----------- | :------------------ |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 3be3f9d3-9d9e-4c50-9491-85ece8334ff6 | 0            | 2020-01-15T01:22:15 |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | f5cc5176-ce96-4bb4-bbaa-a0e3a9054ecf | 1            | 2020-01-15T01:22:17 |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 72b980e9-2efc-4ca7-ab1b-ffd7bece8472 | 2            | 2020-01-15T01:22:10 |
|                                    |                | :                  |                                      |              |                     |
|                                    |                | :                  |                                      |              |                     |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 844bd8fb-1f3a-4580-984d-6324f9e208af | 15           | 2020-01-15T01:22:00 |

Ogni istanza del processore di eventi acquisisce la proprietà di una partizione e inizia a elaborare la partizione dall'ultimo [Checkpoint](# Checkpointing)noto. Se un processore ha esito negativo (la macchina virtuale viene arrestata), le altre istanze rilevano questo aspetto osservando l'ora dell'Ultima modifica. Altre istanze tentano di ottenere la proprietà delle partizioni precedentemente di proprietà dell'istanza inattiva e l'archivio Checkpoint garantisce che solo una delle istanze riesca a richiedere la proprietà di una partizione. Quindi, in un determinato momento, al massimo un processore riceve gli eventi da una partizione.

## <a name="receive-messages"></a>Ricevere messaggi

Quando si crea un processore di eventi, si specificano le funzioni che elaborerà gli eventi e gli errori. Ogni chiamata alla funzione che elabora gli eventi recapita un singolo evento da una partizione specifica. È responsabilità dell'utente gestire questo evento. Per assicurarsi che il consumer elabori ogni messaggio almeno una volta, è necessario scrivere codice personalizzato con la logica di ripetizione dei tentativi. Fare attenzione, tuttavia, a inserire messaggi non elaborabili.

Si consiglia di eseguire le operazioni in modo relativamente rapido. Ovvero eseguire il minor grado di elaborazione possibile. Se è necessario scrivere nella risorsa di archiviazione ed eseguire alcune operazioni di routing, è preferibile usare due gruppi di consumer e due processori di eventi.

## <a name="checkpointing"></a>Checkpoint

Il *Checkpoint* è un processo mediante il quale un processore di eventi contrassegna o conferma la posizione dell'ultimo evento elaborato correttamente all'interno di una partizione. Il contrassegno di un checkpoint viene in genere eseguito all'interno della funzione che elabora gli eventi e si verifica in base a ogni partizione all'interno di un gruppo di consumer. 

Se un processore di eventi si disconnette da una partizione, un'altra istanza può riprendere l'elaborazione della partizione in corrispondenza del checkpoint di cui è stato eseguito il commit in precedenza dall'ultimo processore di tale partizione in tale gruppo di consumer. Quando il processore si connette, passa l'offset all'hub eventi per specificare la posizione da cui iniziare la lettura. In questo modo, è possibile usare il checkpoint per contrassegnare gli eventi come "completi" dalle applicazioni downstream e per fornire resilienza quando un processore di eventi diventa inattivo. È possibile tornare a dati precedenti specificando un offset inferiore da questo processo di checkpoint. 

Quando si esegue il checkpoint per contrassegnare un evento come elaborato, viene aggiunta o aggiornata una voce nell'archivio checkpoint con l'offset e il numero di sequenza dell'evento. Gli utenti devono decidere la frequenza di aggiornamento del checkpoint. L'aggiornamento dopo ogni evento elaborato correttamente può avere implicazioni in termini di prestazioni e costi, poiché attiva un'operazione di scrittura nell'archivio dei checkpoint sottostante. Inoltre, il checkpoint ogni singolo evento è indicativo di un modello di messaggistica in coda per cui una coda del bus di servizio può essere un'opzione migliore rispetto a un hub eventi. L'idea alla base di Hub eventi è di ottenere "almeno un" recapito su larga scala. Per rendere idempotenti i sistemi a valle, è facile eseguire il ripristino a seguito di errori o il riavvio del risultato negli stessi eventi ricevuti più volte.

> [!NOTE]
> Se si usa l'archivio BLOB di Azure come archivio di checkpoint in un ambiente che supporta una versione diversa di storage BLOB SDK rispetto a quelli generalmente disponibili in Azure, sarà necessario usare il codice per modificare la versione dell'API del servizio di archiviazione nella versione specifica supportata da tale ambiente. Ad esempio, se si esegue [Hub eventi in un hub Azure stack versione 2002](/azure-stack/user/event-hubs-overview), la versione più recente disponibile per il servizio di archiviazione è la versione 2017-11-09. In questo caso, è necessario usare il codice per fare riferimento alla versione dell'API del servizio di archiviazione a 2017-11-09. Per un esempio su come definire come destinazione una versione specifica dell'API di archiviazione, vedere questi esempi su GitHub: 
> - [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample10_RunningWithDifferentStorageVersion.cs). 
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/)
> - [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript) o  [typescript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/)

## <a name="thread-safety-and-processor-instances"></a>Istanze di elaborazione e sicurezza del thread

Per impostazione predefinita, la funzione che elabora gli eventi viene chiamata in sequenza per una determinata partizione. Gli eventi successivi e le chiamate a questa funzione dalla stessa partizione si accodano dietro le quinte perché il pump dell'evento continua a essere eseguito in background su altri thread. Si noti che gli eventi da partizioni diverse possono essere elaborati contemporaneamente ed è necessario sincronizzare qualsiasi stato condiviso a cui si accede tra le partizioni.

## <a name="next-steps"></a>Passaggi successivi
Vedere le guide introduttive seguenti:

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [JavaScript](event-hubs-node-get-started-send.md)
