---
title: Spazi dei nomi abbinati del bus di servizio di Azure | Documentazione Microsoft
description: Dettagli di implementazione e costi relativi allo spazio dei nomi associato
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 2440c8d3-ed2e-47e0-93cf-ab7fbb855d2e
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/21/2017
ms.author: sethm
ms.openlocfilehash: f16c65286b0aa079889c9d53e98bf54e3d57c95f
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/22/2017
---
# <a name="paired-namespace-implementation-details-and-cost-implications"></a>Dettagli di implementazione e implicazioni in termini di costi relativi allo spazio dei nomi associato

Il metodo [PairNamespaceAsync][PairNamespaceAsync], mediante un'istanza di [SendAvailabilityPairedNamespaceOptions][SendAvailabilityPairedNamespaceOptions], esegue attività visibili per conto dell'utente. Poiché l'uso di questa funzionalità comporta considerazioni in termini di costi, è utile comprendere le attività eseguite in modo da prevederne il comportamento. L'API attiva automaticamente il comportamento seguente per conto dell'utente:

* Creazione di code di backlog.
* Creazione di un oggetto [MessageSender][MessageSender] che comunica con code o argomenti.
* Invio di messaggi ping a un'entità di messaggistica che diventa non disponibile, nel tentativo di rilevare il momento in cui questa diventa nuovamente disponibile.
* Creazione facoltativa di un set di message pump che spostano i messaggi dalle code di backlog a quelle primarie.
* Coordinamento di eventi di chiusura o errore delle istanze di [MessagingFactory][MessagingFactory] primarie e secondarie.

A livello generale, la funzionalità opera in questo modo: quando l'entità primaria è integra, non si verificano modifiche del comportamento. Quando la durata di [FailoverInterval][FailoverInterval] scade e l'entità primaria non rileva invii riusciti dopo un'eccezione di tipo [MessagingException][MessagingException] o [TimeoutException][TimeoutException] non temporanea, si verifica quanto segue:

1. Le operazioni di invio all'entità primaria vengono disabilitate e il sistema effettua il ping dell'entità primaria finché i ping non vengono recapitati correttamente.
2. Viene selezionata una coda di backlog casuale.
3. Gli oggetti [BrokeredMessage][BrokeredMessage] vengono instradati alla coda di backlog scelta.
4. Se un'operazione di invio alla coda di backlog scelta non riesce, la coda viene estratta dalla rotazione e viene selezionata una nuova coda. L'errore viene comunicato a tutti i mittenti nell'istanza di [MessagingFactory][MessagingFactory].

Le figure seguenti illustrano la sequenza. Prima di tutto il mittente invia i messaggi.

![Spazi dei nomi associati][0]

Dal momento in cui si verifica l'errore di invio alla coda primaria, il mittente inizia a inviare i messaggi a una coda di backlog scelta casualmente. Contemporaneamente avvia un'attività di ping.

![Spazi dei nomi associati][1]

A questo punto i messaggi si trovano ancora nella coda secondaria e non sono stati recapitati a quella primaria. Quando la coda primaria sarà di nuovo integra, è necessario che il sifone venga eseguito da almeno un processo. Il sifone recapita i messaggi dalle varie code di backlog alle entità di destinazione appropriate, ad esempio code e argomenti.

![Spazi dei nomi associati][2]

Nelle sezioni successive di questo argomento verranno illustrati i dettagli specifici del funzionamento di questi componenti.

## <a name="creation-of-backlog-queues"></a>Creazione di code di backlog
L'oggetto [SendAvailabilityPairedNamespaceOptions][SendAvailabilityPairedNamespaceOptions] passato al metodo [PairNamespaceAsync][PairNamespaceAsync] indica il numero di code di backlog da usare. Ogni coda di backlog viene quindi creata con le proprietà seguenti impostate in modo esplicito. Tutti gli altri valori vengono impostati in base all'oggetto [QueueDescription predefinito][QueueDescription]:

| path | [spazio dei nomi primario]/x-servicebus-transfer/[indice] dove [indice] è un valore espresso in [0, BacklogQueueCount] |
| --- | --- |
| MaxSizeInMegabytes |5120 |
| MaxDeliveryCount |int.MaxValue |
| DefaultMessageTimeToLive |TimeSpan.MaxValue |
| AutoDeleteOnIdle |TimeSpan.MaxValue |
| LockDuration |1 minuto |
| EnableDeadLetteringOnMessageExpiration |true |
| EnableBatchedOperations |true |

Ad esempio, la prima coda di backlog creata per lo spazio dei nomi **contoso** è denominata `contoso/x-servicebus-transfer/0`.

Quando si creano le code, il codice verifica prima di tutto se una coda esiste. In caso contrario, la coda viene creata. Il codice non esegue la pulizia di code di backlog "aggiuntive". In particolare, se per l'applicazione con lo spazio dei nomi primario **contoso** sono necessarie cinque code di backlog, ma esiste già una coda con percorso `contoso/x-servicebus-transfer/7`, la coda aggiuntiva risulta presente, ma non viene usata. Il sistema consente in modo esplicito la presenza di code di backlog aggiuntive che tuttavia non vengono usate. Come proprietario dello spazio dei nomi, l'utente è responsabile della pulizia di eventuali code di backlog inutilizzate o indesiderate. Questa decisione è dovuta al fatto che il bus di servizio non è in grado di riconoscere lo scopo dell'esistenza di tutte le code nello spazio dei nomi. Se esiste già anche una coda con il nome specificato, ma non soddisfa quanto definito in precedenza per l'oggetto [QueueDescription][QueueDescription], la modifica del comportamento predefinito dipende esclusivamente dai motivi definiti dall'utente. Non vengono fornite garanzie riguardo alle modifiche apportate alle code di backlog dal codice dell'utente. Verificare quindi con attenzione le modifiche apportate.

## <a name="custom-messagesender"></a>MessageSender personalizzato
Quando vengono inviati, tutti i messaggi passano attraverso un oggetto [MessageSender][MessageSender] interno, che ha un comportamento normale quando tutto funziona correttamente, ma che esegue il reindirizzamento alle code di backlog in caso di errori. Se viene rilevato un errore non temporaneo, viene avviato un timer. Dopo un periodo definito in [TimeSpan][TimeSpan] corrispondente al valore della proprietà [FailoverInterval][FailoverInterval], durante il quale non viene eseguito l'invio di messaggi, viene avviata la procedura di failover. A questo punto, per ogni entità si verifica quanto segue:

* Viene eseguita un'attività ping per ogni [PingPrimaryInterval][PingPrimaryInterval], per verificare se l'entità è disponibile. Se questa attività riesce, tutto il codice client che usa l'entità inizia immediatamente a inviare nuovi messaggi allo spazio dei nomi primario.
* Le richieste successive di invio alla stessa entità da qualsiasi altro mittente avranno come risultato l'invio di un oggetto [BrokeredMessage][BrokeredMessage] che dovrà essere modificato per entrare nella coda di backlog. La modifica comporta la rimozione di alcune proprietà dall'oggetto [BrokeredMessage][BrokeredMessage] e la relativa archiviazione in un'altra area. Le proprietà seguenti vengono eliminate e aggiunte con un nuovo alias, per consentire al bus di servizio e all'SDK di elaborare i messaggi in modo uniforme:

| Nome proprietà precedente | Nuovo nome proprietà |
| --- | --- |
| SessionId |x-ms-sessionid |
| TimeToLive |x-ms-timetolive |
| ScheduledEnqueueTimeUtc |x-ms-path |

Il percorso di destinazione originale viene archiviato anche nel messaggio come proprietà x-ms-path. Questa progettazione consente la coesistenza in un'unica coda di backlog di messaggi appartenenti a più entità. Le proprietà vengono ritrasferite dal sifone.

Per l'oggetto [MessageSender][MessageSender] possono verificarsi problemi quando i messaggi raggiungono il limite di 256 KB e viene avviato il failover. L'oggetto [MessageSender][MessageSender] personalizzato archivia nelle code di backlog i messaggi per tutte le code e tutti gli argomenti. Questo oggetto unisce i messaggi provenienti da numerose code primarie nelle code di backlog. Per gestire il bilanciamento del carico tra più client che non hanno relazioni tra loro, l'SDK sceglie casualmente una coda di backlog per ogni oggetto [QueueClient][QueueClient] o [TopicClient][TopicClient] creato nel codice.

## <a name="pings"></a>Ping
Un messaggio ping è un oggetto [BrokeredMessage][BrokeredMessage] vuoto con la proprietà [ContentType][ContentType] impostata su application/vnd.ms-servicebus-ping e un valore [TimeToLive][TimeToLive] di 1 secondo. Questo ping presenta una caratteristica particolare nel bus di servizio. Il server non recapita mai un ping quando un chiamante qualsiasi richiede un oggetto [BrokeredMessage][BrokeredMessage]. Di conseguenza, non sarà mai necessario comprendere come ricevere e ignorare questi messaggi. A ogni entità, argomento o coda univoca, per istanza di [MessagingFactory][MessagingFactory] e per client che risulta non disponibile viene inviato un ping. Per impostazione predefinita, ciò accade una volta al minuto. I messaggi ping sono considerati come normali messaggi del bus di servizio e possono comportare l'addebito di costi in base alla larghezza di banda e al numero di messaggi. Non appena i client rilevano che il sistema è disponibile, l'invio di messaggi viene arrestato.

## <a name="the-syphon"></a>Sifone
Almeno un programma eseguibile nell'applicazione deve eseguire attivamente il sifone, che avvia un'operazione di ricezione mediante polling prolungato della durata di 15 minuti. Quando tutte le entità sono disponibili e sono presenti dieci code di backlog, l'applicazione che ospita il sifone chiama l'operazione di ricezione 40 volte all'ora, 960 volte al giorno e 28800 volte in 30 giorni. Quando il sifone sposta attivamente i messaggi dalla coda di backlog a quella primaria, per ciascun messaggio vengono applicati i costi riportati di seguito (costi standard relativi alle dimensioni del messaggio e all'impiego delle larghezza di banda nelle varie fasi):

1. Invio alla coda di backlog.
2. Ricezione dalla coda di backlog.
3. Invio alla coda primaria.
4. Ricezione dalla coda primaria.

## <a name="closefault-behavior"></a>Comportamento di chiusura o errore
In un'applicazione che ospita il sifone, non appena l'oggetto [MessagingFactory][MessagingFactory] primario o secondario restituisce un errore o viene chiuso, senza che l'oggetto correlato restituisca un errore o venga chiuso, e non appena questo stato viene rilevato, il sifone si attiva automaticamente. Se l'oggetto [MessagingFactory][MessagingFactory] correlato al primo non viene chiuso entro 5 secondi, il sifone restituisce un errore per l'oggetto [MessagingFactory][MessagingFactory] ancora aperto.

## <a name="next-steps"></a>Passaggi successivi
Per informazioni dettagliate sulla messaggistica asincrona del bus di servizio, vedere [Modelli di messaggistica asincrona e disponibilità elevata][Asynchronous messaging patterns and high availability]. 

[PairNamespaceAsync]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_PairNamespaceAsync_Microsoft_ServiceBus_Messaging_PairedNamespaceOptions_
[SendAvailabilityPairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[MessageSender]: /dotnet/api/microsoft.servicebus.messaging.messagesender
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[FailoverInterval]: /dotnet/api/microsoft.servicebus.messaging.pairednamespaceoptions#Microsoft_ServiceBus_Messaging_PairedNamespaceOptions_FailoverInterval
[MessagingException]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[TimeoutException]: https://msdn.microsoft.com/library/azure/system.timeoutexception.aspx
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[QueueDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[TimeSpan]: https://msdn.microsoft.com/library/azure/system.timespan.aspx
[PingPrimaryInterval]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions#Microsoft_ServiceBus_Messaging_SendAvailabilityPairedNamespaceOptions_PingPrimaryInterval
[QueueClient]: /dotnet/api/microsoft.servicebus.messaging.queueclient
[TopicClient]: /dotnet/api/microsoft.servicebus.messaging.topicclient
[ContentType]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_ContentType
[TimeToLive]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_TimeToLive
[Asynchronous messaging patterns and high availability]: service-bus-async-messaging.md
[0]: ./media/service-bus-paired-namespaces/IC673405.png
[1]: ./media/service-bus-paired-namespaces/IC673406.png
[2]: ./media/service-bus-paired-namespaces/IC673407.png
