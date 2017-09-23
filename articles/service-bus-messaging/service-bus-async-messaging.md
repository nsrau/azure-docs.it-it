---
title: Messaggistica asincrona del bus di servizio | Documentazione Microsoft
description: Descrizione della messaggistica asincrona del bus di servizio di Azure.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: f1435549-e1f2-40cb-a280-64ea07b39fc7
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: e0c999b2bf1dd38d8a0c99c6cdd4976cc896dd99
ms.openlocfilehash: 95d6f295ba145a55fe4ed3fc7c6f627c9d419a3c
ms.contentlocale: it-it
ms.lasthandoff: 04/20/2017

---
# <a name="asynchronous-messaging-patterns-and-high-availability"></a>Modelli di messaggistica asincrona e disponibilità elevata

La messaggistica asincrona può essere implementata in molti modi diversi. Con code, argomenti e sottoscrizioni, il bus di servizio di Azure supporta la messaggistica asincrona tramite un meccanismo di archiviazione e inoltro. In un'operazione normale (sincrona) i messaggi vengono inviati a code e argomenti e ricevuti da code e sottoscrizioni. Le applicazioni create dipendono dalla disponibilità continua di queste entità. Quando l'integrità dell'entità cambia a causa di diverse circostanze, è necessario fornire un'entità a capacità limitata che possa soddisfare la maggior parte delle esigenze.

In genere, le applicazioni usano modelli di messaggistica asincrona per consentire diversi scenari di comunicazione. È possibile creare applicazioni in cui i client possono inviare messaggi ai servizi anche quando questi non sono in esecuzione. Per le applicazioni in cui si verificano picchi nelle comunicazioni, si può usare una coda per livellare il carico fornendo un'area per il buffer delle comunicazioni.  Infine è possibile ottenere un semplice ma efficace servizio di bilanciamento del carico per distribuire i messaggi tra più computer.

Per mantenere la disponibilità di una qualsiasi di queste entità, è necessario considerare i diversi casi in cui le entità possono risultare non disponibili per un sistema di messaggistica permanente. In generale, l'entità risulta non disponibile alle applicazioni nelle circostanze seguenti:

* Impossibilità di inviare messaggi.
* Impossibilità di ricevere messaggi.
* Impossibilità di gestire le entità, ad esempio eseguire operazioni di creazione, recupero, aggiornamento o eliminazione.
* Impossibilità di contattare il servizio.

Per ognuno di questi problemi, esistono diverse modalità di errore che consentono a un'applicazione di continuare a funzionare a un certo livello di capacità limitata. Ad esempio, un sistema che può inviare messaggi, ma non riceverli, e continuare comunque a ricevere ordini dai clienti senza però poterli elaborare. Questo argomento illustra i problemi che possono verificarsi e le tecniche per attenuarne gli effetti. Nel bus di servizio sono state introdotte numerose misure di prevenzione che richiedono un consenso esplicito, le cui regole d'uso sono illustrate in questo argomento.

## <a name="reliability-in-service-bus"></a>Affidabilità nel bus di servizio
Sono disponibili diverse modalità di gestione dei problemi relativi a entità e messaggi, oltre a linee guida per l'utilizzo appropriato di queste misure di prevenzione. Per comprendere le linee guida, è necessario comprendere prima di tutto i motivi per cui nel bus di servizio si possono verificare errori. Considerata la progettazione dei sistemi Azure, tutti questi problemi sono in genere di breve durata. Ad alto livello, le diverse cause della mancata disponibilità si manifestano nei modi seguenti:

* Limitazione da un sistema esterno dal quale dipende il bus di servizio. La limitazione è generata da interazioni con le risorse di archiviazione e calcolo.
* Problema dovuto a un sistema dal quale dipende il bus di servizio. Ad esempio, problemi che possono verificarsi in una determinata risorsa di archiviazione.
* Errore del bus di servizio in un singolo sottosistema. In questo caso, è possibile che un nodo di calcolo si trovi in uno stato incoerente e debba essere riavviato, causando il bilanciamento del carico in altri nodi di tutte le entità servite. Questa situazione può causare a sua volta un breve periodo di elaborazione lenta dei messaggi.
* Errore del bus di servizio in un data center di Azure. Questo è un "errore irreversibile" durante il quale il sistema risulta irraggiungibile per diversi minuti o per alcune ore.

> [!NOTE]
> Il termine **archiviazione** può indicare sia archiviazione di Azure sia di SQL Azure.
> 
> 

Nel bus di servizio sono disponibili diverse misure di prevenzione per questi problemi. Le sezioni seguenti illustrano ogni problema e le relative misure di prevenzione.

### <a name="throttling"></a>Limitazione
Nel bus di servizio la limitazione consente la gestione congiunta della frequenza dei messaggi. Ogni singolo nodo del bus di servizio ospita molte entità, ognuna delle quali invia richieste al sistema in termini di CPU, memoria, archiviazione e altri facet. Quando uno di questi facet individua un utilizzo superiore alla soglia predefinita, il bus di servizio può negare una richiesta specifica. Il chiamante riceve un'eccezione di tipo [ServerBusyException][ServerBusyException] e riprova dopo 10 secondi.

Come misura di prevenzione, il codice deve leggere l'errore e bloccare ogni nuovo tentativo del messaggio per almeno 10 secondi. Poiché l'errore può verificarsi in varie parti dell'applicazione del cliente, si presuppone che ogni parte esegua indipendentemente la logica di ripetizione dei tentativi. Il codice può ridurre l'occorrenza delle limitazioni abilitando il partizionamento di una coda o un argomento.

### <a name="issue-for-an-azure-dependency"></a>Problema per una dipendenza di Azure
Anche per altri componenti di Azure possono occasionalmente verificarsi problemi di servizio. Quando, ad esempio, si aggiorna un sistema usato dal bus di servizio, è possibile che il sistema funzioni temporaneamente con capacità limitate. Per risolvere questo tipo di problemi, il bus di servizio analizza e implementa regolarmente le misure di prevenzione. È tuttavia possibile che si verifichino effetti collaterali di misure di prevenzione. Ad esempio, per gestire problemi temporanei con l'archiviazione, il bus di servizio implementa un sistema che consente il funzionamento uniforme delle operazioni di invio dei messaggi.  Considerata la natura della misura di prevenzione, un messaggio inviato può impiegare fino a 15 minuti prima di essere visualizzato nella sottoscrizione o nella coda interessata ed essere pronto per un'operazione di ricezione. In genere, la maggior parte delle entità non è interessata da questo problema. Dato tuttavia il numero di entità presenti nel bus di servizio all'interno di Azure, questa misura di prevenzione può essere utile a volte per un limitato sottoinsieme di clienti del bus di servizio.

### <a name="service-bus-failure-on-a-single-subsystem"></a>Errore del bus di servizio in un singolo sottosistema
In qualsiasi applicazione possono verificarsi casi in cui un componente interno del bus di servizio diventa incoerente. Quando il bus di servizio rileva questo problema, raccoglie dati dall'applicazione a fini diagnostici. Una volta raccolti i dati, l'applicazione viene riavviata nel tentativo di ripristinarne un stato coerente. Questo processo avviene abbastanza velocemente e causa la mancata disponibilità di un'entità per alcuni minuti, anche se in genere i tempi di inattività sono molto più brevi.

In questi casi, l'applicazione client genera un'eccezione di tipo [System.TimeoutException][System.TimeoutException] o [MessagingException][MessagingException]. Il bus di servizio include una misura di prevenzione per questo problema basata sulla logica di ripetizione automatica dei tentativi del client. Al termine del periodo di ripetizione, se il messaggio non è stato recapitato, è possibile provare a usare altre funzionalità, ad esempio gli [spazi dei nomi associati][paired namespaces]. Questi ultimi presentano altri ostacoli che sono illustrati in questo articolo.

### <a name="failure-of-service-bus-within-an-azure-datacenter"></a>Errore del bus di servizio in un data center di Azure
Il motivo più probabile di un errore in un data center di Azure è una distribuzione di aggiornamento non riuscita del bus di servizio o di un sistema dipendente. Poiché la piattaforma è stata migliorata, la probabilità di questo tipo di errore è diminuita. Un errore di un data center può verificarsi anche per i motivi seguenti:

* Interruzione elettrica (arresto di alimentazione e produzione di energia).
* Connettività (interruzione della connessione Internet tra i client e Azure).

In entrambi i casi, il problema è dovuto a una calamità naturale o a un errore umano. Per aggirare il problema e assicurarsi di poter continuare a inviare messaggi, è possibile usare gli [spazi dei nomi associati][paired namespaces] per attivare l'invio dei messaggi a una seconda destinazione mentre viene ristabilita l'integrità della prima destinazione. Per altre informazioni, vedere [Procedure consigliate per isolare le applicazioni del bus di servizio da interruzioni ed emergenze del servizio][Best practices for insulating applications against Service Bus outages and disasters].

## <a name="paired-namespaces"></a>Spazi dei nomi associati
La funzionalità degli [spazi dei nomi associati][paired namespaces] supporta scenari in cui un'entità o una distribuzione del bus di servizio in un data center diventa non disponibile. Anche se eventi di questo tipo si verificano raramente, i sistemi distribuiti devono comunque essere in grado di gestire gli scenari peggiori. In genere, questo tipo di evento si verifica perché in qualche elemento dal quale dipende il bus di servizio avviene un problema di breve durata. Per garantire la disponibilità di un'applicazione durante un'interruzione, gli utenti del bus di servizio possono usare due diversi spazi dei nomi, preferibilmente in due distinti data center, per ospitare le proprie entità di messaggistica. Nel resto di questa sezione si userà la terminologia seguente:

* Spazio dei nomi primario: spazio dei nomi con cui interagisce l'applicazione per operazioni di invio e ricezione.
* Spazio dei nomi secondario: spazio dei nomi che agisce come backup dello spazio dei nomi primario. La logica dell'applicazione non interagisce con questo spazio dei nomi.
* Intervallo di failover: intervallo di tempo per accettare gli errori normali prima che l'applicazione passi dallo spazio dei nomi primario a quello secondario.

Gli spazi dei nomi associati supportano *sendavailability*, che consente di preservare la capacità di inviare messaggi. Per usare la funzionalità SendAvailability, l'applicazione deve soddisfare i requisiti seguenti:

1. I messaggi vengono ricevuti solo dallo spazio dei nomi primario.
2. I messaggi inviati a una coda o a un argomento specifico possono arrivare senza un ordine preciso.
3. I messaggi in una sessione possono arrivare senza un ordine preciso. Questa è un'interruzione rispetto alla normale funzionalità delle sessioni, che sta a indicare che l'applicazione usa le sessioni per il raggruppamento logico dei messaggi.
4. Lo stato delle sessioni viene mantenuto solo nello spazio dei nomi primario.
5. La coda primaria può tornare online e iniziare ad accettare i messaggi prima che la coda secondaria recapiti tutti i messaggi alla coda primaria.

Le sezioni seguenti illustrano l'API, la relativa modalità di implementazione e il codice di esempio che usa la funzionalità. Si noti che a questa funzionalità sono associati costi di fatturazione.

### <a name="the-messagingfactorypairnamespaceasync-api"></a>API MessagingFactory.PairNamespaceAsync
La funzionalità degli spazi dei nomi associati include il metodo [PairNamespaceAsync][PairNamespaceAsync] sulla classe [Microsoft.ServiceBus.Messaging.MessagingFactory][Microsoft.ServiceBus.Messaging.MessagingFactory]:

```csharp
public Task PairNamespaceAsync(PairedNamespaceOptions options);
```

Quando l'attività è terminata, l'associazione degli spazi dei nomi è completa e pronta ad avere effetto su qualsiasi oggetto [MessageReceiver][MessageReceiver], [QueueClient][QueueClient] o [TopicClient][TopicClient] creato con l'istanza [MessagingFactory][MessagingFactory]. [Microsoft.ServiceBus.Messaging.PairedNamespaceOptions][Microsoft.ServiceBus.Messaging.PairedNamespaceOptions] è la classe di base per i vari tipi di associazioni disponibili con un oggetto [MessagingFactory][MessagingFactory]. [SendAvailabilityPairedNamespaceOptions][SendAvailabilityPairedNamespaceOptions] è attualmente l'unica classe derivata che implementa i requisiti della funzionalità SendAvailability. La classe [SendAvailabilityPairedNamespaceOptions][SendAvailabilityPairedNamespaceOptions] include un set di costruttori che si basano tra loro. Se si osserva il costruttore con il maggior numero di parametri, è possibile comprendere il comportamento degli altri.

```csharp
public SendAvailabilityPairedNamespaceOptions(
    NamespaceManager secondaryNamespaceManager,
    MessagingFactory messagingFactory,
    int backlogQueueCount,
    TimeSpan failoverInterval,
    bool enableSyphon)
```

Ecco il significato di questi parametri:

* *secondaryNamespaceManager*: istanza inizializzata di [NamespaceManager][NamespaceManager] per lo spazio dei nomi secondario che può essere usata dal metodo [PairNamespaceAsync][PairNamespaceAsync] per configurare lo spazio dei nomi secondario. Il gestore dello spazio dei nomi viene usato per ottenere l'elenco di code nello spazio dei nomi e assicurarsi che esistano le code di backlog necessarie. Se le code non esistono, vengono create. [NamespaceManager][NamespaceManager] richiede la possibilità di creare un token con l'attestazione **Manage**.
* *messagingFactory*: istanza di [MessagingFactory][MessagingFactory] per lo spazio dei nomi secondario. L'oggetto [MessagingFactory][MessagingFactory] viene usato per inviare e, se la proprietà [EnableSyphon][EnableSyphon] è impostata su **true**, ricevere messaggi dalle code di backlog.
* *backlogQueueCount*: numero di code di backlog da creare. Il valore deve essere almeno 1. Quando si inviano messaggi al backlog, viene scelta casualmente una di queste code. Se il valore è impostato su 1, è possibile usare una sola coda. In questo caso e se l'unica coda di backlog genera errori, il client non è in grado di usare un'altra coda di backlog e potrebbe non riuscire a inviare il messaggio. È quindi consigliabile impostare questo parametro su un valore maggiore e impostare il valore predefinito su 10. Il parametro può essere impostato su un valore superiore o inferiore in base ai dati che vengono inviati dall'applicazione ogni giorno. Ogni coda di backlog può contenere fino a 5 GB di messaggi.
* *failoverInterval*: intervallo di tempo durante il quale vengono accettati errori nello spazio dei nomi primario, prima di passare ogni singola entità allo spazio dei nomi secondario. I failover si verificano su un'entità alla volta. Le entità in un singolo spazio dei nomi di solito risiedono in nodi diversi del bus di servizio. Un errore in un'entità non implica che si verifichi in un'altra entità. È possibile impostare questo valore su [System.TimeSpan.Zero][System.TimeSpan.Zero] per eseguire il failover allo spazio dei nomi secondario immediatamente dopo il primo errore non temporaneo. Gli errori che attivano il timer del failover sono eccezioni di tipo [MessagingException][MessagingException], in cui la proprietà [IsTransient][IsTransient] è false, o di tipo [System.TimeoutException][System.TimeoutException]. Altri tipi di eccezioni, ad esempio [UnauthorizedAccessException][UnauthorizedAccessException], non causano failover, perché indicano che il client non è configurato in modo corretto. Un'eccezione di tipo [ServerBusyException][ServerBusyException] non causa failover perché il modello corretto prevede un'attesa di 10 secondi e determina un nuovo invio del messaggio.
* *enableSyphon*: indica che l'associazione specificata dovrebbe spostare i messaggi dallo spazio dei nomi secondario a quello primario. In genere nelle applicazioni che inviano messaggi questo valore deve essere impostato su **false** e in quelle che ricevono messaggi deve essere impostato su **true**. Il motivo per cui si verifica questo tipo di errore è dato dal fatto che spesso il numero dei destinatari dei messaggi è inferiore a quello dei mittenti. A seconda del numero dei destinatari, è possibile scegliere di impostare un'unica istanza dell'applicazione per la gestione delle attività del sifone. All'uso di più destinatari sono associati costi di fatturazione per ogni coda di backlog.

Per usare il codice, creare un'istanza primaria di [MessagingFactory][MessagingFactory], un'istanza secondaria di [MessagingFactory][MessagingFactory] e di [NamespaceManager][NamespaceManager] e un'istanza di [SendAvailabilityPairedNamespaceOptions][SendAvailabilityPairedNamespaceOptions]. La chiamata può essere semplice come la seguente:

```csharp
SendAvailabilityPairedNamespaceOptions sendAvailabilityOptions = new SendAvailabilityPairedNamespaceOptions(secondaryNamespaceManager, secondary);
primary.PairNamespaceAsync(sendAvailabilityOptions).Wait();
```

Quando l'attività restituita dal metodo [PairNamespaceAsync][PairNamespaceAsync] è completa, ogni componente è configurato e pronto per l'uso. Prima della restituzione dell'attività, è possibile che non siano state completate tutte le attività in background necessarie per il corretto funzionamento dell'associazione. È quindi consigliabile non iniziare a inviare messaggi prima della restituzione dell'attività. Se si verifica un errore, dovuto ad esempio a credenziali errate o all'impossibilità di creare code di backlog, le eccezioni verranno restituite al completamento dell'attività. Dopo la restituzione dell'attività, verificare che siano state trovate o create le code esaminando la proprietà [BacklogQueueCount][BacklogQueueCount] nell'istanza di [SendAvailabilityPairedNamespaceOptions][SendAvailabilityPairedNamespaceOptions]. In base al codice precedente, questa operazione viene visualizzata nel modo seguente:

```csharp
if (sendAvailabilityOptions.BacklogQueueCount < 1)
{
    // Handle case where no queues were created.
}
```

## <a name="next-steps"></a>Passaggi successivi
Dopo avere appreso le nozioni di base della messaggistica asincrona nel bus di servizio, leggere altre informazioni sugli [spazi dei nomi associati][paired namespaces].

[ServerBusyException]: /dotnet/api/microsoft.servicebus.messaging.serverbusyexception
[System.TimeoutException]: https://msdn.microsoft.com/library/system.timeoutexception.aspx
[MessagingException]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Microsoft.ServiceBus.Messaging.MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[MessageReceiver]: /dotnet/api/microsoft.servicebus.messaging.messagereceiver
[QueueClient]: /dotnet/api/microsoft.servicebus.messaging.queueclient
[TopicClient]: /dotnet/api/microsoft.servicebus.messaging.topicclient
[Microsoft.ServiceBus.Messaging.PairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.pairednamespaceoptions
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[SendAvailabilityPairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[PairNamespaceAsync]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_PairNamespaceAsync_Microsoft_ServiceBus_Messaging_PairedNamespaceOptions_
[EnableSyphon]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions#Microsoft_ServiceBus_Messaging_SendAvailabilityPairedNamespaceOptions_EnableSyphon
[System.TimeSpan.Zero]: https://msdn.microsoft.com/library/system.timespan.zero.aspx
[IsTransient]: /dotnet/api/microsoft.servicebus.messaging.messagingexception#Microsoft_ServiceBus_Messaging_MessagingException_IsTransient
[UnauthorizedAccessException]: https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx
[BacklogQueueCount]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions?redirectedfrom=MSDN#Microsoft_ServiceBus_Messaging_SendAvailabilityPairedNamespaceOptions_BacklogQueueCount
[paired namespaces]: service-bus-paired-namespaces.md

