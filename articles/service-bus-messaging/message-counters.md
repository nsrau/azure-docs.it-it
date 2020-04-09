---
title: Bus di servizio di Azure - conteggio messaggiAzure Service Bus - message count
description: Recuperare il numero di messaggi contenuti nelle code e nelle sottoscrizioni usando Azure Resource Manager e le API NamespaceManager del bus di servizio di Azure.Retrieve the count of messages held in queues and subscriptions by using Azure Resource Manager and the Azure Service Bus NamespaceManager APIs.
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
ms.date: 04/08/2020
ms.author: aschhab
ms.openlocfilehash: 8020b12ca892fbf7dec6fed6259526d958fb110f
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80891765"
---
# <a name="message-counters"></a>Contatori di messaggi

È possibile recuperare il conteggio dei messaggi salvati nelle code e nelle sottoscrizioni usando Azure Resource Manager e le API [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) per il bus di servizio in .NET Framework SDK.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Con PowerShell è possibile ottenere il conteggio come indicato di seguito:

```powershell
(Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue).CountDetails
```

## <a name="message-count-details"></a>Dettagli conteggio dei messaggi

Il conteggio dei messaggi attivi è utile per stabilire se una coda crea un backlog che richiede più risorse per essere elaborato rispetto a quelle attualmente distribuite. Nella classe [MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) sono disponibili i seguenti dettagli del contatore:

-   [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ActiveMessageCount): messaggi nella coda o nella sottoscrizione che sono in stato attivo e pronti per la distribuzione.
-   [DeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.deadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_DeadLetterMessageCount): messaggi nella coda di messaggi non recapitabili.
-   [ScheduledMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.scheduledmessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ScheduledMessageCount): messaggi in stato pianificato.
-   [TransferDeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transferdeadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferDeadLetterMessageCount): messaggi che non sono stati trasferiti in un'altra coda o in un altro argomento e sono stati spostati nella coda dei messaggi non recapitabili di trasferimento.
-   [TransferMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transfermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferMessageCount): messaggi in attesa di trasferimento in un'altra coda o in un altro argomento.

Se un'applicazione vuole ridimensionare le risorse in base alla lunghezza della coda, deve farlo in maniera limitata. L'acquisizione dei contatori di messaggi è un'operazione impegnativa all'interno del broker messaggi e, se viene eseguita frequentemente, influisce direttamente e negativamente sulle prestazioni dell'entità.

> [!NOTE]
> I messaggi inviati a un argomento del bus di servizio vengono inoltrati alle sottoscrizioni per tale argomento. Pertanto, il numero di messaggi attivi sull'argomento stesso è 0, poiché tali messaggi sono stati inoltrati correttamente alla sottoscrizione. Ottenere il numero di messaggi nella sottoscrizione e verificare che sia maggiore di 0.Get the message count at the subscription and verify that it's greater than 0. Anche se vengono visualizzati messaggi nella sottoscrizione, vengono effettivamente archiviati in uno spazio di archiviazione di proprietà dell'argomento. 

Se si esaminano le sottoscrizioni, il numero di messaggi diverso da zero (che aggiunge fino a 323 MB di spazio per l'intera entità).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla messaggistica del bus di servizio, vedere gli argomenti seguenti:

* [Code, argomenti e sottoscrizioni del bus di servizio](service-bus-queues-topics-subscriptions.md)
* [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)
* [Come usare gli argomenti e le sottoscrizioni del bus di servizio](service-bus-dotnet-how-to-use-topics-subscriptions.md)
