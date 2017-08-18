---
title: "Panoramica delle funzionalità di Hub eventi di Azure | Microsoft Docs"
description: "Panoramica e informazioni dettagliate sulle funzionalità di Hub eventi di Azure"
services: event-hubs
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/15/2017
ms.author: sethm
ms.translationtype: HT
ms.sourcegitcommit: 99523f27fe43f07081bd43f5d563e554bda4426f
ms.openlocfilehash: cb5ec1a105c632626c5caf39e4fd356177883123
ms.contentlocale: it-it
ms.lasthandoff: 08/05/2017

---

# <a name="event-hubs-features-overview"></a>Panoramica delle funzionalità di Hub eventi

Hub eventi di Azure è una servizio di elaborazione degli eventi scalabile che inserisce ed elabora grandi volumi di eventi e dati, con bassa latenza e affidabilità elevata. Vedere [Che cos'è Hub eventi?](event-hubs-what-is-event-hubs.md) per una panoramica generale del servizio.

Questo articolo si basa sulle informazioni presenti nella [panoramica](event-hubs-what-is-event-hubs.md) e contiene dettagli tecnici e informazioni sull'implementazione relativi ai componenti e alle funzionalità di Hub eventi.

## <a name="event-publishers"></a>Publisher di eventi

Qualsiasi entità che invia dati a un hub eventi è un produttore di eventi o *autore di eventi*. Gli autori di eventi possono pubblicare eventi usando HTTPS o AMQP 1.0. Gli autori di eventi usano un token di firma di accesso condiviso per identificarsi con un hub eventi e possono avere un'identità univoca oppure usare un token di firma di accesso condiviso comune.

### <a name="publishing-an-event"></a>Pubblicazione di un evento

È possibile pubblicare un evento tramite AMQP 1.0 o HTTPS. Hub eventi offre [classi e librerie client](event-hubs-dotnet-framework-api-overview.md) per la pubblicazione di eventi in un hub eventi dai client .NET. Per altre piattaforme e runtime, è possibile utilizzare qualsiasi client AMQP 1.0, ad esempio [Apache Qpid](http://qpid.apache.org/). È possibile pubblicare eventi singolarmente o in batch. Una singola pubblicazione (istanza dei dati dell'evento) ha un limite di 256 KB, indipendentemente dal fatto che si tratti di un singolo evento o di un batch. La pubblicazione di eventi di dimensioni superiori alla soglia determina un errore. È consigliabile che gli autori non rilevino le partizioni all'interno dell'hub eventi e specifichino solo una *chiave di partizione* (illustrata nella sezione successiva) o la propria identità tramite il token di firma di accesso condiviso.

La scelta di utilizzare AMQP o HTTPS dipende dallo scenario di utilizzo. AMQP richiede di stabilire un socket bidirezionale persistente oltre alla sicurezza a livello di trasporto (TLS) o SSL/TLS. AMQP comporta costi di rete superiori in fase di inizializzazione della sessione, ma HTTPS richiede un costo generale SSL aggiuntivo per ogni richiesta. AMQP offre prestazioni più elevate per i server di pubblicazione più attivi.

![Hub eventi](./media/event-hubs-features/partition_keys.png)

Hub eventi garantisce che tutti gli eventi che condividono un valore di chiave di partizione vengano recapitati in ordine e alla stessa partizione. Se si usano chiavi di partizione con i criteri di autore, l'identità dell’autore e il valore della chiave di partizione devono corrispondere. In caso contrario, si verifica un errore.

### <a name="publisher-policy"></a>Criteri di autore

Hub eventi consente un controllo granulare degli autori di eventi tramite *criteri di autore*. I criteri di autore sono funzionalità di runtime progettate per consentire un numero elevato di autori di eventi indipendenti. Con i criteri di autore, ogni autore usa il proprio identificatore univoco durante la pubblicazione di eventi in un hub eventi, con il meccanismo seguente:

```
//[my namespace].servicebus.windows.net/[event hub name]/publishers/[my publisher name]
```

Non è necessario creare nomi di autore prima di procedere, ma devono corrispondere al token SAS utilizzato quando si pubblica un evento, al fine di garantire le identità di autore indipendenti. Quando si utilizzano criteri di autore, il valore **PartitionKey** è impostato sul nome dell’autore. Per il corretto funzionamento, questi valori devono corrispondere.

## <a name="capture"></a>Acquisizione

[Acquisizione dell'hub eventi](event-hubs-capture-overview.md) consente di acquisire il flusso di dati nell'hub eventi e di archiviarlo automaticamente in un account di archiviazione Blob di propria scelta. È possibile abilitare la funzione di acquisizione dal portale di Azure e specificare una dimensione minima e l'intervallo di tempo per eseguire l'acquisizione. Acquisizione di Hub eventi consente di specificare l'account di archiviazione BLOB di Azure e il contenitore che vengono usati per salvare i dati acquisiti. I dati acquisiti vengono scritti nel formato di Apache Avro.

## <a name="partitions"></a>Partitions

Hub eventi fornisce lo streaming di messaggi tramite un modello consumer partizionato in cui ogni consumer legge solo un sottoinsieme specifico, o partizione, del flusso di messaggi. Questo modello consente la scalabilità orizzontale per l'elaborazione di eventi e fornisce altre funzionalità incentrate sul flusso non disponibili in code e argomenti.

Una partizione è una sequenza ordinata di eventi contenuta in un hub eventi. Man mano che arrivano, i nuovi eventi vengono aggiunti alla fine di questa sequenza. Una partizione può essere considerata come "registro commit".

![Hub eventi](./media/event-hubs-features/partition.png)

Hub eventi mantiene i dati per un periodo di conservazione configurato che viene applicato a tutte le partizioni nell'hub eventi. Gli eventi scadono su base temporale; non è possibile eliminarli in modo esplicito. Poiché le partizioni sono indipendenti e contengono una sequenza specifica di dati, presentano spesso velocità di crescita diverse.

![Hub eventi](./media/event-hubs-features/multiple_partitions.png)

Il numero di partizioni viene specificato in fase di creazione e deve essere compreso tra 2 e 32. Il numero di partizioni non può essere modificato. È quindi consigliabile valutare le dimensioni a lungo termine in fase di impostazione del numero di partizioni. Le partizioni sono un meccanismo di organizzazione dei dati correlato al parallelismo downstream necessario per utilizzare le applicazioni. Il numero di partizioni in un hub eventi è direttamente correlato al numero di lettori simultanei previsti. Per impostare un numero di partizioni superiore a 32, contattare il team di Hub eventi.

Anche se le partizioni sono identificabili e consentono l'invio diretto, questa operazione non è consigliata per una partizione. È invece possibile usare i costrutti più generici introdotti nelle sezioni [Autore di eventi](#event-publishers) e [Capacità](#capacity). 

Nelle partizioni viene inserita una sequenza di dati evento, che include il corpo dell'evento, un contenitore delle proprietà definito dall'utente e metadati quali il rispettivo offset nella partizione e il rispettivo numero nella sequenza di flusso.

Per altre informazioni sulle partizioni e il necessario equilibrio tra disponibilità e affidabilità, vedere la [Guida alla programmazione di Hub eventi](event-hubs-programming-guide.md#partition-key) e l'articolo [Disponibilità e coerenza nell'Hub eventi](event-hubs-availability-and-consistency.md).

### <a name="partition-key"></a>Chiave di partizione

È possibile usare una [chiave di partizione](event-hubs-programming-guide.md#partition-key) per mappare i dati dell'evento in ingresso in partizioni specifiche ai fini dell'organizzazione dei dati. La chiave di partizione è un valore fornito dal mittente che viene passato a un hub eventi. Viene elaborato tramite una funzione di hashing statica, che crea l'assegnazione di partizione. Se non si specifica una chiave di partizione quando si pubblica un evento, viene usata un'assegnazione round robin.

L'autore di eventi è a conoscenza solo della chiave di partizione, non la partizione in cui gli eventi vengono pubblicati. Questa separazione tra chiave e partizione evita che il mittente debba conoscere troppe informazioni sull'elaborazione downstream. Un’identità univoca per dispositivo o utente crea una chiave di partizione efficace, ma è possibile utilizzare anche altri attributi, ad esempio l’area geografica, per raggruppare gli eventi correlati in un'unica partizione.

## <a name="sas-tokens"></a>Token di firma di accesso condiviso

Hub eventi usa *firme di accesso condiviso*, disponibili a livello di spazio dei nomi e di hub eventi. Un token SAS viene generato da una chiave SAS ed è un hash SHA di un URL, codificato in un formato specifico. Usando il nome della chiave (criterio) e il token, Hub eventi può rigenerare l'hash e quindi autenticare il mittente. In genere, i token di firma di accesso condiviso per gli autori di eventi vengono creati con privilegi solo di **invio** per un hub eventi specifico. Questo meccanismo di URL token SAS costituisce la base per l'identificazione dell’autore introdotta nei criteri di autore. Per altre informazioni sull'uso di SAS, vedere [Autenticazione della firma di accesso condiviso con il bus di servizio](../service-bus-messaging/service-bus-sas.md).

## <a name="event-consumers"></a>Consumer di eventi

Qualsiasi entità che legge i dati dell'evento da un hub eventi è un *consumer eventi*. Tutti i consumer di Hub eventi si connettono tramite la sessione AMQP 1.0 e gli eventi vengono recapitati tramite la sessione appena disponibili. Il client non deve eseguire il polling per la disponibilità dei dati.

### <a name="consumer-groups"></a>Gruppi di utenti

Il meccanismo di pubblicazione/sottoscrizione degli Hub eventi è abilitato tramite i *gruppi di consumer*. Un gruppo di consumer è una vista (stato, posizione o offset) di un intero hub eventi. I gruppi di consumer consentono a più applicazioni costose di avere una visualizzazione separata del flusso di eventi e di leggere il flusso in modo indipendente in base alle proprie esigenze e con i propri gli offset.

In un’architettura di elaborazione flusso, ogni applicazione a valle equivale a un gruppo di consumer. Se si desidera scrivere i dati dell’evento nell’archiviazione a lungo termine, tale applicazione writer di archiviazione è un gruppo di consumer. L'elaborazione di eventi complessi può essere quindi eseguita da un altro gruppo di consumer separato. È possibile accedere alla partizioni solo tramite un gruppo di consumer. In una partizione per un gruppo di consumer ci possono essere al massimo cinque lettori simultanei; è tuttavia **consigliabile che in una partizione per un gruppo di consumer ci sia solo un ricevitore attivo**. In un hub eventi è sempre presente un gruppo di consumer predefinito e per un hub eventi di livello Standard è possibile creare fino a 20 gruppi di consumer.

Di seguito sono riportati esempi della convenzione dell'URI del gruppo di consumer:

```http
//[my namespace].servicebus.windows.net/[event hub name]/[Consumer Group #1]
//[my namespace].servicebus.windows.net/[event hub name]/[Consumer Group #2]
```

La figura seguente illustra l'architettura di elaborazione del flusso di Hub eventi:

![Hub eventi](./media/event-hubs-features/event_hubs_architecture.png)

### <a name="stream-offsets"></a>Offset di flusso

Un *offset* è la posizione di un evento all'interno di una partizione. Un offset può essere considerato come un cursore sul lato client. L'offset è la numerazione di byte dell'evento. Questo offset consente a un consumer di eventi (lettore) di specificare un punto nel flusso di eventi da cui iniziare la lettura degli eventi. È possibile specificare l'offset come un timestamp o un valore di offset. I consumer sono responsabili di archiviare i propri valori di offset all'esterno del servizio Hub eventi. All'interno di una partizione, ogni evento include un offset.

![Hub eventi](./media/event-hubs-features/partition_offset.png)

### <a name="checkpointing"></a>Checkpoint

*Checkpoint* è un processo mediante il quale i lettori contrassegnano o eseguono il commit della propria posizione all'interno di una sequenza di eventi di partizione. Il checkpoint è responsabilità del consumer e si verifica per partizione all'interno di un gruppo di consumer. Questa responsabilità significa che per ogni gruppo di consumer, ogni lettore di partizione deve tenere traccia della posizione corrente nel flusso di eventi e può informare il servizio quando considera completo il flusso di dati.

Se un lettore si disconnette da una partizione, quando riconnette inizia a leggere in corrispondenza del checkpoint inviato in precedenza dall’ulitimo lettore di tale partizione in tale gruppo di consumer. Quando il lettore si connette, passa l'offset all'hub eventi per specificare la posizione da cui iniziare la lettura. In questo modo è possibile usare la funzionalità di checkpoint sia per contrassegnare gli eventi come "completi" dalle applicazioni a valle sia per fornire la resilienza in caso di failover tra i lettori in esecuzione in computer diversi. È possibile tornare a dati precedenti specificando un offset inferiore da questo processo di checkpoint. Tramite questo meccanismo il checkpoint consente sia la resilienza del failover che la riproduzione del flusso di eventi.

### <a name="common-consumer-tasks"></a>Attività comuni del consumer

Tutti i consumer di Hub eventi si connettono tramite una sessione AMQP 1.0 e un canale di comunicazione bidirezionale in grado di riconoscere lo stato. Ogni partizione ha una sessione AMQP 1.0 che facilita il trasporto di eventi separati dalla partizione.

#### <a name="connect-to-a-partition"></a>Connettersi a una partizione

Quando ci si connette direttamente a partizioni, viene in genere usato un meccanismo di leasing per coordinare le connessioni di lettura per partizioni specifiche. In questo modo è possibile per ogni partizione in un gruppo di consumer avere un solo lettore attivo. Il checkpoint, il leasing e la gestione dei lettori vengono semplificati tramite la classe [EventProcessorHost](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost) per i client .NET. L'host processore di eventi è un agente consumer intelligente.

#### <a name="read-events"></a>Leggere gli eventi

Dopo l'apertura di una sessione AMQP 1.0 e del collegamento per una partizione specifica, gli eventi vengono recapitati al client AMQP 1.0 dal servizio Hub eventi. Questo meccanismo di recapito permette una velocità effettiva più elevata e una latenza più bassa rispetto ai meccanismi basati su pull, ad esempio HTTP GET. Quando gli eventi vengono inviati al client, ogni istanza dei dati dell'evento contiene metadati importanti, ad esempio l’offset e il numero di sequenza utilizzati per facilitare il checkpoint sulla in sequenza di eventi.

Dati evento:
* Offset
* Numero di sequenza
* Corpo
* Proprietà utente
* Proprietà di sistema

L'utente è responsabile della gestione dell'offset.

## <a name="capacity"></a>Capacità

Hub eventi è un'architettura parallela a scalabilità elevata ed è necessario valutare alcuni fattori chiave durante il ridimensionamento.

### <a name="throughput-units"></a>Unità elaborate

La capacità di velocità effettiva di Hub eventi è controllata dalle *unità elaborate*. Le unità elaborate sono unità di capacità pre-acquistate. Una singola unità elaborata include la capacità seguente:

* Ingresso: fino a 1 MB al secondo o 1000 eventi al secondo, qualunque valore venga raggiunto per primo.
* Uscita: fino a 2 MB al secondo

Oltre la capacità delle unità elaborate acquistate, i dati in ingresso vengono limitati e viene restituito un valore [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception). I dati in uscita non producono eccezioni di limitazione, ma sono ancora limitati alla capacità delle unità elaborate acquistate. Se si ricevono eccezioni di velocità di pubblicazione o sono previste uscite maggiori, controllare il numero di unità elaborate acquistate per lo spazio dei nomi. È possibile gestire le unità elaborate nel pannello **Ridimensionamento** dello spazio dei nomi nel [portale di Azure](https://portal.azure.com). È anche possibile gestire le unità elaborate a livello di programmazione usando le [API degli hub eventi](event-hubs-api-overview.md).

Le unità elaborate vengo o fatturate su base oraria e sono pre-acquistate. Una volta acquistate, le unità elaborate vengono fatturate per un minimo di un'ora. È possibile acquistare fino a 20 unità elaborate per uno spazio dei nomi di Hub eventi, che vengono condivise in tutti gli Hub eventi nello spazio dei nomi.

È possibile acquistare altre unità elaborate in blocchi di 20, fino a un massimo di 100 unità elaborate, contattando il Supporto tecnico di Azure. Inoltre, è possibile acquistare blocchi di 100 unità elaborate.

È consigliabile bilanciare unità elaborate e partizioni per ottenere una scalabilità ottimale. Una singola partizione ha una scala massima di una unità elaborata. Il numero di unità elaborate deve essere minore o uguale al numero di partizioni in un hub eventi.

Per informazioni dettagliate sui prezzi di Hub eventi, vedere [Prezzi di Hub eventi ](https://azure.microsoft.com/pricing/details/event-hubs/).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Hub eventi, vedere i collegamenti seguenti:

* Iniziare con un'[esercitazione di Hub eventi][Event Hubs tutorial]
* [Guida alla programmazione di Hub eventi](event-hubs-programming-guide.md)
* [Disponibilità e coerenza nell'Hub eventi](event-hubs-availability-and-consistency.md)
* [Domande frequenti su Hub eventi](event-hubs-faq.md)
* [Applicazioni di esempio che usano Hub eventi][]

[Event Hubs tutorial]: event-hubs-dotnet-standard-getstarted-send.md
[Applicazioni di esempio che usano Hub eventi]: https://github.com/Azure/azure-event-hubs/tree/master/samples

