---
title: Code e argomenti del bus di servizio come gestori per gli eventi di Griglia di eventi di Azure
description: Descrive come usare code e argomenti del bus di servizio come gestori per gli eventi di Griglia di eventi di Azure.
ms.topic: conceptual
ms.date: 09/03/2020
ms.openlocfilehash: 2b18009f8fb31f1a5f057c7395781f63f182847f
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "96024215"
---
# <a name="service-bus-queues-and-topics-as-event-handlers-for-azure-event-grid-events"></a>Code e argomenti del bus di servizio come gestori per gli eventi di Griglia di eventi di Azure
Un gestore eventi è la posizione in cui l'evento viene inviato. Il gestore esegue altre azioni per elaborare l'evento. Diversi servizi di Azure vengono configurati automaticamente per gestire eventi, uno dei quali è il **bus di servizio di Azure**. 

È possibile usare una coda o un argomento del servizio come gestore per eventi da Griglia di eventi. 

## <a name="service-bus-queues"></a>Code del bus di servizio
È possibile indirizzare eventi in Griglia di eventi direttamente a code del bus di servizio per l'uso nel buffering o negli scenari di controllo e comando nelle applicazioni aziendali.

Nel portale di Azure, durante la creazione di una sottoscrizione di eventi, selezionare **Coda del bus di servizio** come tipo di endpoint e quindi fare clic su **Selezionare un endpoint** per scegliere una coda del bus di servizio.

### <a name="using-cli-to-add-a-service-bus-queue-handler"></a>Uso dell'interfaccia della riga di comando per aggiungere un gestore di code del bus di servizio

Per l'interfaccia della riga di comando di Azure, l'esempio seguente sottoscrive e connette un argomento di Griglia di eventi a una coda del bus di servizio:

```azurecli-interactive
az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebusqueue \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/queues/queue1
```

## <a name="service-bus-topics"></a>Argomenti del bus di servizio

È possibile indirizzare eventi in Griglia di eventi direttamente ad argomenti del bus di servizio per gestire eventi di sistema di Azure con argomenti del bus di servizio o per scenari di messaggistica di controllo e comando.

Nel portale di Azure, durante la creazione di una sottoscrizione di eventi, selezionare **Argomento del bus di servizio** come tipo di endpoint e quindi fare clic su **Selezionare un endpoint** per scegliere un argomento del bus di servizio.

### <a name="using-cli-to-add-a-service-bus-topic-handler"></a>Uso dell'interfaccia della riga di comando per aggiungere un gestore di argomenti del bus di servizio

Per l'interfaccia della riga di comando di Azure, l'esempio seguente sottoscrive e connette un argomento di Griglia di eventi a una coda del bus di servizio:

```azurecli-interactive
az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebustopic \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/topics/topic1
```

[!INCLUDE [event-grid-message-headers](../../includes/event-grid-message-headers.md)]

Quando si invia un evento a una coda o a un argomento del bus di servizio come messaggio negoziato, il `messageid` del messaggio negoziato è un ID sistema interno.

L'ID sistema interno per il messaggio verrà mantenuto durante il recapito dell'evento, in modo che sia possibile evitare recapiti duplicati attivando il **rilevamento dei duplicati** sull'entità del bus di servizio. È consigliabile abilitare la durata del rilevamento dei duplicati per l'entità del bus di servizio in modo che corrisponda alla durata (TTL) dell'evento o alla durata massima dei tentativi, a seconda di quale è maggiore.

## <a name="rest-examples-for-put"></a>Esempi REST (per PUT)

### <a name="service-bus-queue"></a>Coda del bus di servizio

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "ServiceBusQueue",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/queues/<SERVICE BUS QUEUE NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-queue---delivery-with-managed-identity"></a>Coda del bus di servizio: recapito con identità gestita

```json
{
    "properties": {
        "deliveryWithResourceIdentity": 
        {
            "identity": 
            {
                "type": "SystemAssigned"
            },
            "destination": 
            {
                "endpointType": "ServiceBusQueue",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/queues/<SERVICE BUS QUEUE NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-topic"></a>Argomento del bus di servizio

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "ServiceBusTopic",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/topics/<SERVICE BUS TOPIC NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-topic---delivery-with-managed-identity"></a>Argomento del bus di servizio: recapito con identità gestita

```json
{
    "properties": 
    {
        "deliveryWithResourceIdentity": 
        {
            "identity": 
            {
                "type": "SystemAssigned"
            },
            "destination": 
            {
                "endpointType": "ServiceBusTopic",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/topics/<SERVICE BUS TOPIC NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>Passaggi successivi
Per un elenco di gestori eventi supportati, vedere l'articolo [Gestori eventi](event-handlers.md). 
