---
title: Messaggistica asincrona del bus di servizio | Documentazione Microsoft
description: Descrizione della messaggistica asincrona del bus di servizio di Azure.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: f1435549-e1f2-40cb-a280-64ea07b39fc7
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 50778ae742c1ec66857a6c2fa6250dc3d67e5601
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60531105"
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

In questi casi, l'applicazione client genera un'eccezione di tipo [System.TimeoutException][System.TimeoutException] o [MessagingException][MessagingException]. Il bus di servizio include una misura di prevenzione per questo problema basata sulla logica di ripetizione automatica dei tentativi del client. Al termine del periodo di ripetizione, se il messaggio non è stato recapitato, è possibile provare a usare altre funzionalità citate nell'articolo sulla [gestione delle interruzioni e delle emergenze][handling outages and disasters].

## <a name="next-steps"></a>Passaggi successivi
Dopo avere appreso le nozioni di base della messaggistica asincrona nel bus di servizio, vedere le altre informazioni sulla [gestione delle interruzioni e delle emergenze][handling outages and disasters].

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
[PairNamespaceAsync]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[EnableSyphon]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[System.TimeSpan.Zero]: https://msdn.microsoft.com/library/system.timespan.zero.aspx
[IsTransient]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[UnauthorizedAccessException]: https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx
[BacklogQueueCount]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions?redirectedfrom=MSDN
[handling outages and disasters]: service-bus-outages-disasters.md
