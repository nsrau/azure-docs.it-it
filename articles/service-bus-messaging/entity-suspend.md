---
title: Bus di servizio di Azure-sospendere le entità di messaggistica
description: Questo articolo illustra come sospendere temporaneamente e riattivare le entità messaggio del bus di servizio di Azure (code, argomenti e sottoscrizioni).
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 4c4e554ada8c66b56d5d466c7becfc813701402a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85339998"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>Sospendere e riattivare le entità di messaggistica (disabilitazione)

Le code, gli argomenti e le sottoscrizioni possono essere temporaneamente sospesi. Con la sospensione, l'entità passa allo stato disabilitato. In questo stato, tutti i messaggi vengono mantenuti nella risorsa di archiviazione, ma non è possibile rimuovere o aggiungere messaggi e le rispettive operazioni di protocollo restituiscono errori.

La sospensione di un'entità viene in genere eseguita per motivi amministrativi urgenti. Uno scenario è la distribuzione di un ricevitore non corretto che preleva messaggi dalla coda, non riesce a elaborarli, ma erroneamente completa i messaggi e li rimuove. Se viene diagnosticato tale comportamento, è possibile disabilitare la coda per le ricezioni finché non viene distribuito il codice corretto, in modo da impedire un'ulteriore perdita di dati a causa del codice errato.

Una sospensione o una riattivazione può essere eseguita dall'utente o dal sistema. Il sistema sospende le entità solo per gravi motivi amministrativi, ad esempio per il raggiungimento del limite di spesa della sottoscrizione. Le entità disabilitate dal sistema non possono essere riattivate dall'utente, ma vengono ripristinate dopo che la causa della sospensione è stata risolta.

Nel portale la sezione **Properties** per la rispettiva entità consente di modificare lo stato; lo screenshot seguente mostra l'interruttore per una coda:

![][1]

Il portale consente solo la disabilitazione completa delle code. È anche possibile disabilitare separatamente le operazioni di invio e di ricezione usando le API [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) per il bus di servizio in .NET Framework SDK oppure con un modello di Azure Resource Manager, tramite l'interfaccia della riga di comando di Azure o Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="suspension-states"></a>Stati di sospensione

Per una coda possono essere impostati gli stati seguenti.

-   **Active**: la coda è attiva.
-   **Disabled**: la coda è sospesa.
-   **SendDisabled**: la coda è parzialmente sospesa e la ricezione è consentita.
-   **ReceiveDisabled**: la coda è parzialmente sospesa e l'invio è consentito.

Per le sottoscrizioni e gli argomenti è possibile impostare solo **Active** e **Disabled**.

L'enumerazione [EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) definisce anche un set di stati di transizione che possono essere impostati solo dal sistema. L'esempio seguente mostra il comando di PowerShell per disabilitare una coda. Il comando di riattivazione è equivalente, con l'impostazione di `Status` su **Active**.

```powershell
$q = Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla messaggistica del bus di servizio, vedere gli argomenti seguenti:

* [Code, argomenti e sottoscrizioni del bus di servizio](service-bus-queues-topics-subscriptions.md)
* [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)
* [Come usare gli argomenti e le sottoscrizioni del bus di servizio](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/queue-disable.png

