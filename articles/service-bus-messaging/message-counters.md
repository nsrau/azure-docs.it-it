---
title: Conteggio dei messaggi del bus di servizio di Azure | Microsoft Docs
description: Recuperare il conteggio dei messaggi del bus di servizio di Azure.
services: service-bus-messaging
documentationcenter: ''
author: clemensv
manager: timlt
editor: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2018
ms.author: spelluru
ms.openlocfilehash: bd9d1b813c90be09301eeff1de7580f22c15500b
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2018
ms.locfileid: "47407587"
---
# <a name="message-counters"></a>Contatori di messaggi

È possibile recuperare il conteggio dei messaggi salvati nelle code e nelle sottoscrizioni usando Azure Resource Manager e le API [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) per il bus di servizio in .NET Framework SDK.

Con PowerShell è possibile ottenere il conteggio come indicato di seguito:

```powershell
(Get-AzureRmServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue).CountDetails
```

## <a name="message-count-details"></a>Dettagli conteggio dei messaggi

Il conteggio dei messaggi attivi è utile per stabilire se una coda crea un backlog che richiede più risorse per essere elaborato rispetto a quelle attualmente distribuite. Nella classe [MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) sono disponibili i seguenti dettagli del contatore:

-   [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ActiveMessageCount): messaggi nella coda o nella sottoscrizione che sono in stato attivo e pronti per la distribuzione.
-   [DeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.deadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_DeadLetterMessageCount): messaggi nella coda di messaggi non recapitabili.
-   [ScheduledMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.scheduledmessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ScheduledMessageCount): messaggi in stato pianificato.
-   [TransferDeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transferdeadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferDeadLetterMessageCount): messaggi che non sono stati trasferiti in un'altra coda o in un altro argomento e sono stati spostati nella coda dei messaggi non recapitabili di trasferimento.
-   [TransferMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transfermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferMessageCount): messaggi in attesa di trasferimento in un'altra coda o in un altro argomento.

Se un'applicazione vuole ridimensionare le risorse in base alla lunghezza della coda, deve farlo in maniera molto limitata. L'acquisizione dei contatori di messaggi è un'operazione impegnativa all'interno del broker messaggi e, se viene eseguita frequentemente, influisce direttamente e negativamente sulle prestazioni dell'entità.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla messaggistica del bus di servizio, vedere gli argomenti seguenti:

* [Dati fondamentali del bus di servizio](service-bus-fundamentals-hybrid-solutions.md)
* [Code, argomenti e sottoscrizioni del bus di servizio](service-bus-queues-topics-subscriptions.md)
* [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)
* [Come usare gli argomenti e le sottoscrizioni del bus di servizio](service-bus-dotnet-how-to-use-topics-subscriptions.md)