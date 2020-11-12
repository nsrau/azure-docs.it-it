---
title: Bus di servizio di Azure-sospendere le entità di messaggistica
description: Questo articolo illustra come sospendere temporaneamente e riattivare le entità messaggio del bus di servizio di Azure (code, argomenti e sottoscrizioni).
ms.topic: article
ms.date: 09/29/2020
ms.openlocfilehash: ea1acab3d0a86b0064f8b3eef7bfd1496bd17041
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94543052"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>Sospendere e riattivare le entità di messaggistica (disabilitazione)

Le code, gli argomenti e le sottoscrizioni possono essere temporaneamente sospesi. Con la sospensione, l'entità passa allo stato disabilitato. In questo stato, tutti i messaggi vengono mantenuti nella risorsa di archiviazione, ma non è possibile rimuovere o aggiungere messaggi e le rispettive operazioni di protocollo restituiscono errori.

Potrebbe essere necessario sospendere un'entità per motivi amministrativi urgenti. Un ricevitore difettoso, ad esempio, preleva i messaggi dalla coda, non elabora i messaggi e li rimuove. In questo caso, potrebbe essere necessario disabilitare la coda per le ricevute finché non si corregge e si distribuisce il codice. 

Una sospensione o una riattivazione può essere eseguita dall'utente o dal sistema. Il sistema sospende solo le entità a causa di gravi motivi amministrativi, ad esempio il raggiungimento del limite di spesa della sottoscrizione. Le entità disabilitate dal sistema non possono essere riattivate dall'utente, ma vengono ripristinate dopo che la causa della sospensione è stata risolta.

## <a name="queue-status"></a>Stato coda 
Gli Stati che possono essere impostati per una **coda** sono:

-   **Active** : la coda è attiva. È possibile inviare e ricevere messaggi dalla coda. 
-   **Disabled** : la coda è sospesa. Equivale a impostare sia **SendDisabled** che **ReceiveDisabled**. 
-   **SendDisabled** : non è possibile inviare messaggi alla coda, ma è possibile ricevere messaggi da tale coda. Si otterrà un'eccezione se si tenta di inviare messaggi alla coda. 
-   **ReceiveDisabled** : è possibile inviare messaggi alla coda, ma non è possibile ricevere messaggi da tale coda. Si otterrà un'eccezione se si tenta di ricevere messaggi nella coda.


### <a name="change-the-queue-status-in-the-azure-portal"></a>Modificare lo stato della coda nel portale di Azure: 

1. Nella portale di Azure passare allo spazio dei nomi del bus di servizio. 
1. Selezionare la coda per cui si desidera modificare lo stato. Le code vengono visualizzate nel riquadro inferiore al centro. 
1. Nella pagina **coda del bus di servizio** , vedere lo stato corrente della coda come collegamento ipertestuale. Se la **Panoramica** non è selezionata nel menu a sinistra, selezionarla per visualizzare lo stato della coda. Selezionare lo stato corrente della coda per modificarlo. 

    :::image type="content" source="./media/entity-suspend/select-state.png" alt-text="Selezionare lo stato della coda":::
4. Selezionare il nuovo stato per la coda e selezionare **OK**. 

    :::image type="content" source="./media/entity-suspend/entity-state-change.png" alt-text="Impostare lo stato della coda":::
    
È anche possibile disabilitare le operazioni di invio e ricezione usando le API [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) del bus di servizio in .NET SDK oppure usando un modello di Azure Resource Manager tramite l'interfaccia della riga di comando di Azure o Azure PowerShell.

### <a name="change-the-queue-status-using-azure-powershell"></a>Modificare lo stato della coda usando Azure PowerShell
L'esempio seguente mostra il comando di PowerShell per disabilitare una coda. Il comando di riattivazione è equivalente, con l'impostazione di `Status` su **Active**.

```powershell
$q = Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="topic-status"></a>Stato argomento
È possibile modificare lo stato dell'argomento nella portale di Azure. Selezionare lo stato corrente dell'argomento per visualizzare la pagina seguente, che consente di modificare lo stato. 

:::image type="content" source="./media/entity-suspend/topic-state-change.png" alt-text="Modifica stato argomento":::

Gli Stati che possono essere impostati per un **argomento** sono i seguenti:
- **Attivo** : l'argomento è attivo. È possibile inviare messaggi all'argomento. 
- **Disabled** : l'argomento è sospeso. Non è possibile inviare messaggi all'argomento. 
- **SendDisabled** : lo stesso effetto di **disabled**. Non è possibile inviare messaggi all'argomento. Si otterrà un'eccezione se si tenta di inviare messaggi all'argomento. 

## <a name="subscription-status"></a>Stato della sottoscrizione
È possibile modificare lo stato della sottoscrizione nella portale di Azure. Selezionare lo stato corrente della sottoscrizione per visualizzare la pagina seguente, che consente di modificare lo stato. 

:::image type="content" source="./media/entity-suspend/subscription-state-change.png" alt-text="Modificare lo stato della sottoscrizione":::

Gli Stati che possono essere impostati per una **sottoscrizione** sono:
- **Attivo** : la sottoscrizione è attiva. È possibile ricevere messaggi frm per la sottoscrizione.
- **Disabled** : la sottoscrizione è sospesa. Non è possibile ricevere messaggi dalla sottoscrizione. 
- **ReceiveDisabled** : lo stesso effetto di **disabled**. Non è possibile ricevere messaggi dalla sottoscrizione. Si otterrà un'eccezione se si tenta di ricevere messaggi nella sottoscrizione.

| Stato argomento | Stato della sottoscrizione | Comportamento | 
| ------------ | ------------------- | -------- | 
| Attivo | Attivo | È possibile inviare messaggi all'argomento e ricevere messaggi dalla sottoscrizione. | 
| Attivo | Disabled o Receive disabled | È possibile inviare messaggi all'argomento, ma non è possibile ricevere messaggi dalla sottoscrizione | 
| Disabilitato o inviato disabilitato | Attivo | Non è possibile inviare messaggi all'argomento, ma è possibile ricevere messaggi già presenti nella sottoscrizione. | 
| Disabilitato o inviato disabilitato | Disabled o Receive disabled | Non è possibile inviare messaggi all'argomento e non è possibile ricevere una sottoscrizione. | 

## <a name="other-statuses"></a>Altri Stati
L'enumerazione [EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) definisce anche un set di stati di transizione che possono essere impostati solo dal sistema. 


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla messaggistica del bus di servizio, vedere gli argomenti seguenti:

* [Code, argomenti e sottoscrizioni del bus di servizio](service-bus-queues-topics-subscriptions.md)
* [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)
* [Come usare gli argomenti e le sottoscrizioni del bus di servizio](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/entity-state-change.png

