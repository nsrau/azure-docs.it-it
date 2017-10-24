---
title: Scadenza dei messaggi del bus di servizio di Azure | Microsoft Docs
description: Scadenza e durata (TTL) dei messaggi del bus di servizio di Azure
services: service-bus-messaging
documentationcenter: 
author: clemensv
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: sethm
ms.openlocfilehash: 504010a39a4012b9a9edb60bb9a5b33ac20499c1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="message-expiration-time-to-live"></a>Scadenza dei messaggi (durata)

Il payload all'interno di un messaggio, oppure un comando o una richiesta che un messaggio trasmette a un ricevitore, è quasi sempre soggetto a una forma di scadenza a livello di applicazione. Dopo tale scadenza, il contenuto non viene più recapitato oppure l'operazione richiesta non viene più eseguita.

Per gli ambienti di sviluppo e test in cui code e argomenti vengono spesso usati nel contesto di esecuzioni parziali di applicazioni o parti di applicazioni, è anche consigliabile che i messaggi di test abbandonati vengano automaticamente sottoposti a Garbage Collection, in modo che l'esecuzione dei test successiva possa iniziare in modo pulito.

La scadenza dei singoli messaggi può essere controllata impostando la proprietà di sistema [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive), che specifica una durata relativa. La scadenza diventa un istante assoluto quando il messaggio viene accodato nell'entità. In quel momento, la proprietà [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc) assume il valore [**EnqueuedTimeUtc**](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc#Microsoft_ServiceBus_Messaging_BrokeredMessage_EnqueuedTimeUtc) + [**TimeToLive**](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive).

Passato l'istante definito da **ExpiresAtUtc**, i messaggi non sono più idonei per il recupero. La scadenza non influisce sui messaggi attualmente bloccati per il recapito. Tali messaggi vengono comunque gestiti normalmente. Se il blocco scade o il messaggio viene abbandonato, la scadenza ha effetto immediato.

Quando il messaggio è bloccato, l'applicazione potrebbe essere in possesso di un messaggio che nominalmente è scaduto. Il fatto che l'applicazione proceda con l'elaborazione o scelga di abbandonare il messaggio dipende dall'implementatore.

## <a name="entity-level-expiration"></a>Scadenza a livello di entità

Tutti i messaggi inviati a una coda o un argomento sono soggetti a una scadenza predefinita che viene impostata a livello di entità con la proprietà [defaultMessageTimeToLive](/azure/templates/microsoft.servicebus/namespaces/queues) e che può essere impostata anche nel portale durante la creazione e modificata in un secondo momento. La scadenza predefinita viene usata per tutti i messaggi inviati all'entità in cui il valore di [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) non è impostato in modo esplicito. La scadenza predefinita funge anche da limite massimo per il valore **TimeToLive**. I messaggi con un valore della scadenza **TimeToLive** superiore rispetto al valore predefinito vengono modificati automaticamente impostando il valore **defaultMessageTimeToLive** prima di essere accodati.

Facoltativamente, i messaggi scaduti possono essere spostati in una [coda di messaggi non recapitabili](service-bus-dead-letter-queues.md) impostando la proprietà [EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enabledeadletteringonmessageexpiration#Microsoft_ServiceBus_Messaging_QueueDescription_EnableDeadLetteringOnMessageExpiration) oppure selezionando la casella corrispondente nel portale. Se l'opzione viene lasciata disabilitata, i messaggi scaduti vengono eliminati. I messaggi scaduti spostati nella coda di messaggi non recapitabili possono essere distinti dagli altri messaggi della coda di messaggi non recapitabili valutando la proprietà [DeadletterReason](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) che il broker archivia nella sezione delle proprietà utente. In questo caso, il valore è [TTLExpiredException](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq).

Nel caso menzionato in precedenza in cui il messaggio è protetto da scadenza fino a quando è bloccato e se il flag è impostato sull'entità, il messaggio viene spostato nella coda di messaggi non recapitabili non appena il blocco viene abbandonato o scade. Non viene tuttavia spostato se il messaggio viene finalizzato correttamente, operazione che presuppone che l'applicazione lo abbia gestito correttamente nonostante la scadenza nominale.

La combinazione di [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) e inserimento automatico (e transazionale) nella coda di messaggi non recapitabili alla scadenza rappresenta uno strumento prezioso per stabilire in modo affidabile se un processo assegnato a un gestore o a un gruppo di gestori con una determinata scadenza viene recuperato per l'elaborazione quando la scadenza viene raggiunta.

Si consideri, ad esempio, un sito Web che deve eseguire in modo affidabile i processi in un back-end con vincoli di scalabilità e che in alcuni casi è soggetto a picchi di traffico o richiede un isolamento rispetto a episodi di disponibilità di tale back-end. In una situazione normale, il gestore sul lato server per i dati utente inviati esegue il push delle informazioni in una coda e successivamente riceve una risposta che conferma la corretta gestione della transazione in una coda di risposta. Se c'è un picco di traffico e il gestore di back-end non è in grado di elaborare gli elementi del backlog in tempo, i processi scaduti vengono restituiti nella coda di messaggi non recapitabili. L'utente interattivo può ricevere una notifica che indica che l'operazione richiesta impiegherà un po' più tempo del solito e la richiesta può quindi essere inserita in un'altra coda per un percorso di elaborazione in cui il risultato dell'elaborazione finale viene inviato all'utente tramite posta elettronica. 

## <a name="temporary-entities"></a>Entità temporanee

È possibile creare sottoscrizioni, argomenti e code del bus di servizio come entità temporanee, che vengono rimosse automaticamente se non vengono usate per un periodo di tempo specificato.
 
La pulizia automatica è utile negli scenari di sviluppo e test in cui le entità vengono create in modo dinamico e non vengono eliminate dopo l'uso, a causa di un'interruzione dell'esecuzione dei test o del debug. È utile anche quando un'applicazione crea entità dinamiche, ad esempio una coda di risposta, per la ricezione di risposte in un processo del server Web o in un altro oggetto di durata relativamente breve, in cui è difficile eseguire la pulizia delle entità in modo affidabile quando l'istanza dell'oggetto non è più presente.

La funzionalità viene abilitata usando la proprietà [autoDeleteOnIdle](/azure/templates/microsoft.servicebus/namespaces/queues), impostata sulla durata per cui un'entità deve rimanere inattiva (non usata) prima di essere eliminata automaticamente. La durata minima è 5 minuti.
 
La proprietà deve essere impostata tramite un'operazione di Azure Resource Manager o tramite le API [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) client .NET Framework. Non può essere impostata nel portale.


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla messaggistica del bus di servizio, vedere gli argomenti seguenti:

* [Dati fondamentali del bus di servizio](service-bus-fundamentals-hybrid-solutions.md)
* [Code, argomenti e sottoscrizioni del bus di servizio](service-bus-queues-topics-subscriptions.md)
* [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)
* [Come usare gli argomenti e le sottoscrizioni del bus di servizio](service-bus-dotnet-how-to-use-topics-subscriptions.md)