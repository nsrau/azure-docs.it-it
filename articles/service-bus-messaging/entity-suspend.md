---
title: Bus di servizio di Azure-sospendere le entità di messaggistica
description: Questo articolo illustra come sospendere temporaneamente e riattivare le entità messaggio del bus di servizio di Azure (code, argomenti e sottoscrizioni).
ms.topic: article
ms.date: 09/29/2020
ms.openlocfilehash: f89e17e494cc777691b7f7ca47538cd29114d2dc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91575247"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>Sospendere e riattivare le entità di messaggistica (disabilitazione)

Le code, gli argomenti e le sottoscrizioni possono essere temporaneamente sospesi. Con la sospensione, l'entità passa allo stato disabilitato. In questo stato, tutti i messaggi vengono mantenuti nella risorsa di archiviazione, ma non è possibile rimuovere o aggiungere messaggi e le rispettive operazioni di protocollo restituiscono errori.

La sospensione di un'entità viene in genere eseguita per motivi amministrativi urgenti. Uno scenario è la distribuzione di un ricevitore non corretto che preleva messaggi dalla coda, non riesce a elaborarli, ma erroneamente completa i messaggi e li rimuove. Se viene diagnosticato tale comportamento, è possibile disabilitare la coda per le ricezioni finché non viene distribuito il codice corretto, in modo da impedire un'ulteriore perdita di dati a causa del codice errato.

Una sospensione o una riattivazione può essere eseguita dall'utente o dal sistema. Il sistema sospende le entità solo per gravi motivi amministrativi, ad esempio per il raggiungimento del limite di spesa della sottoscrizione. Le entità disabilitate dal sistema non possono essere riattivate dall'utente, ma vengono ripristinate dopo che la causa della sospensione è stata risolta.

## <a name="queue-status"></a>Stato coda 
Per una coda possono essere impostati gli stati seguenti.

-   **Active**: la coda è attiva.
-   **Disabled**: la coda è sospesa. Equivale a impostare sia **SendDisabled** che **ReceiveDisabled**. 
-   **SendDisabled**: la coda è parzialmente sospesa e la ricezione è consentita.
-   **ReceiveDisabled**: la coda è parzialmente sospesa e l'invio è consentito.

### <a name="change-the-queue-status-in-the-azure-portal"></a>Modificare lo stato della coda nel portale di Azure: 

1. Nella portale di Azure passare allo spazio dei nomi del bus di servizio. 
1. Selezionare la coda per cui si desidera modificare lo stato. Le code vengono visualizzate nel riquadro inferiore al centro. 
1. Nella pagina **coda del bus di servizio** , vedere lo stato corrente della coda come collegamento ipertestuale. Se la **Panoramica** non è selezionata nel menu a sinistra, selezionarla per visualizzare lo stato della coda. Selezionare lo stato corrente della coda per modificarlo. 

    :::image type="content" source="./media/entity-suspend/select-state.png" alt-text="Selezionare lo stato della coda":::
4. Selezionare il nuovo stato per la coda e selezionare **OK**. 

    :::image type="content" source="./media/entity-suspend/entity-state-change.png" alt-text="Selezionare lo stato della coda":::
    
Il portale consente solo la disabilitazione completa delle code. È anche possibile disabilitare separatamente le operazioni di invio e di ricezione usando le API [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) per il bus di servizio in .NET Framework SDK oppure con un modello di Azure Resource Manager, tramite l'interfaccia della riga di comando di Azure o Azure PowerShell.

### <a name="change-the-queue-status-using-azure-powershell"></a>Modificare lo stato della coda usando Azure PowerShell
L'esempio seguente mostra il comando di PowerShell per disabilitare una coda. Il comando di riattivazione è equivalente, con l'impostazione di `Status` su **Active**.

```powershell
$q = Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="topic-status"></a>Stato argomento
La modifica dello stato dell'argomento nell'portale di Azure è simile alla modifica dello stato di una coda. Quando si seleziona lo stato corrente dell'argomento, viene visualizzata la pagina seguente che consente di modificare lo stato. 

:::image type="content" source="./media/entity-suspend/topic-state-change.png" alt-text="Selezionare lo stato della coda":::

Gli Stati che possono essere impostati per un argomento sono i seguenti:
- **Attivo**: l'argomento è attivo.
- **Disabled**: l'argomento è sospeso.
- **SendDisabled**: lo stesso effetto di **disabled**.

## <a name="subscription-status"></a>Stato della sottoscrizione
La modifica dello stato della sottoscrizione nella portale di Azure è simile alla modifica dello stato di un argomento o di una coda. Quando si seleziona lo stato corrente della sottoscrizione, viene visualizzata la pagina seguente che consente di modificare lo stato. 

:::image type="content" source="./media/entity-suspend/subscription-state-change.png" alt-text="Selezionare lo stato della coda":::

Gli Stati che possono essere impostati per un argomento sono i seguenti:
- **Attivo**: l'argomento è attivo.
- **Disabled**: l'argomento è sospeso.
- **ReceiveDisabled**: lo stesso effetto di **disabled**.

## <a name="other-statuses"></a>Altri Stati
L'enumerazione [EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) definisce anche un set di stati di transizione che possono essere impostati solo dal sistema. 


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla messaggistica del bus di servizio, vedere gli argomenti seguenti:

* [Code, argomenti e sottoscrizioni del bus di servizio](service-bus-queues-topics-subscriptions.md)
* [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)
* [Come usare gli argomenti e le sottoscrizioni del bus di servizio](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/entity-state-change.png

