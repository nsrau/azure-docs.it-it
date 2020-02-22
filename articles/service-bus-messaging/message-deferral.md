---
title: Bus di servizio di Azure-rinvio messaggio
description: Questo articolo illustra come rinviare il recapito dei messaggi del bus di servizio di Azure. Il messaggio rimane nella coda o nella sottoscrizione, ma viene messo da parte.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 5e32c461902c1e340c6cece22669a59847e660cd
ms.sourcegitcommit: 163be411e7cd9c79da3a3b38ac3e0af48d551182
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2020
ms.locfileid: "77538397"
---
# <a name="message-deferral"></a>Differimento di messaggi

Se un client di coda o di sottoscrizione riceve un messaggio che è disposto a elaborare, ma l'elaborazione non è possibile a causa di circostanze particolari all'interno dell'applicazione, il client può rinviare a un secondo momento il recupero del messaggio. Il messaggio rimane nella coda o nella sottoscrizione, ma viene messo da parte.

Il differimento è una funzionalità creata appositamente per gli scenari di elaborazione del flusso di lavoro. Per i framework del flusso di lavoro potrebbe essere necessario elaborare determinate operazioni in un ordine particolare e potrebbe essere necessario posticipare l'elaborazione di alcuni messaggi ricevuti fino al completamento del lavoro precedente indicato negli altri messaggi.

Un semplice esempio illustrativo è dato da una sequenza di elaborazione degli ordini in cui la notifica di pagamento del fornitore esterno dei servizi di pagamento viene visualizzata in un sistema prima che l'ordine di acquisto corrispondente sia stato propagato dal negozio al sistema di evasione degli ordini. In questo caso, il sistema di evasione degli ordini può rinviare l'elaborazione della notifica di pagamento finché non è disponibile un ordine a cui associarla. Negli scenari rendezvous in cui i messaggi di origini diverse fanno andare avanti un flusso di lavoro, l'ordine di esecuzione in tempo reale potrebbe essere corretto, ma i messaggi che mostrano i risultati potrebbero arrivare non in ordine.

Il differimento consente di riordinare i messaggi, sostituendo l'ordine di arrivo con un ordine in cui possono essere elaborati, lasciando nell'archivio i messaggi per cui l'elaborazione deve essere posticipata.

## <a name="message-deferral-apis"></a>API di differimento di messaggi

L'API è [BrokeredMessage. posticipate](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.defer?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_Defer) o [BrokeredMessage. DeferAsync](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deferasync?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_DeferAsync) nel client di .NET Framework, [MessageReceiver. DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) nel client di .NET standard e [IMessageReceiver. posticipa](/java/api/com.microsoft.azure.servicebus.imessagereceiver.defer?view=azure-java-stable) o [IMessageReceiver. DeferAsync](/java/api/com.microsoft.azure.servicebus.imessagereceiver.deferasync?view=azure-java-stable) nel client Java. 

I messaggi rinviati rimangono nella coda principale insieme a tutti gli altri messaggi attivi (a differenza dei messaggi non recapitabili che rimangono in una coda secondaria), ma non possono essere più ricevuti usando le normali funzioni Receive/ReceiveAsync. I messaggi rinviati possono essere individuati tramite l'[esplorazione dei messaggi](message-browsing.md) se un'applicazione ne perde traccia.

Per recuperare un messaggio rinviato, il proprietario deve ricordarsi il [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) perché è quello che rinvia il messaggio. Qualsiasi ricevitore che conosce il SequenceNumber di un messaggio rinviato può successivamente ricevere il messaggio in modo esplicito con `Receive(sequenceNumber)`.

Se non è possibile elaborare un messaggio perché una determinata risorsa che deve gestirlo non è temporaneamente disponibile ma l'elaborazione del messaggio non deve essere sospesa sommariamente, per mettere da parte quel messaggio per alcuni minuti, memorizzare il **SequenceNumber** in un [messaggio pianificato](message-sequencing.md) da pubblicare dopo qualche minuto e recuperare di nuovo il messaggio rinviato quando arriva il messaggio pianificato. Se un gestore di messaggi dipende da un database per tutte le operazioni e quel database è temporaneamente non disponibile, è preferibile non usare il differimento, ma sospendere completamente la ricezione dei messaggi finché il database non sarà di nuovo disponibile.


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla messaggistica del bus di servizio, vedere gli argomenti seguenti:

* [Code, argomenti e sottoscrizioni del bus di servizio](service-bus-queues-topics-subscriptions.md)
* [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)
* [Come usare gli argomenti e le sottoscrizioni del bus di servizio](service-bus-dotnet-how-to-use-topics-subscriptions.md)
