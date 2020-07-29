---
title: Bus di servizio di Azure-numero messaggi
description: Recuperare il numero di messaggi contenuti in code e sottoscrizioni usando Azure Resource Manager e le API NamespaceManager del bus di servizio di Azure.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: d0e1a7a5c6eb0b281b4e6ac08135f41f28ecbec8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85341276"
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
> I messaggi inviati a un argomento del bus di servizio vengono inoltrati alle sottoscrizioni per tale argomento. Il numero di messaggi attivi nell'argomento stesso è 0, perché questi messaggi sono stati inviati correttamente alla sottoscrizione. Ottenere il numero di messaggi nella sottoscrizione e verificare che sia maggiore di 0. Anche se i messaggi vengono visualizzati nella sottoscrizione, vengono effettivamente archiviati in una risorsa di archiviazione di proprietà dell'argomento. 

Se si esaminano le sottoscrizioni, si otterrebbe un numero di messaggi diverso da zero (che aggiunge fino a 323MB di spazio per l'intera entità).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla messaggistica del bus di servizio, vedere gli argomenti seguenti:

* [Code, argomenti e sottoscrizioni del bus di servizio](service-bus-queues-topics-subscriptions.md)
* [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)
* [Come usare gli argomenti e le sottoscrizioni del bus di servizio](service-bus-dotnet-how-to-use-topics-subscriptions.md)
