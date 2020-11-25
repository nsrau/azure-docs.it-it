---
title: includere file
description: includere file
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 11/19/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: dac82692c76d9d36b1f25d7b93b5c3a2e2400672
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002793"
---
### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Quali porte è necessario aprire nel firewall? 
È possibile usare i protocolli seguenti con hub eventi di Azure per inviare e ricevere eventi:

- Advance Message Queueing Protocol 1,0 (AMQP)
- Hypertext Transfer Protocol 1,1 con TLS (HTTPS)
- Apache Kafka

Vedere la tabella seguente per le porte in uscita da aprire per usare questi protocolli per comunicare con Hub eventi di Azure. 

| Protocollo | Porte | Dettagli | 
| -------- | ----- | ------- | 
| AMQP | 5671 e 5672 | Vedere [Guida al protocollo AMQP](../articles/service-bus-messaging/service-bus-amqp-protocol-guide.md) | 
| HTTPS | 443 | Questa porta viene usata per l'API HTTP/REST e per AMQP-over-WebSocket. |
| Kafka | 9093 | Vedere [Usare Hub eventi da applicazioni Apache Kafka](../articles/event-hubs/event-hubs-for-kafka-ecosystem-overview.md)

La porta HTTPS è necessaria per le comunicazioni in uscita anche quando AMQP viene usato sulla porta 5671, perché diverse operazioni di gestione eseguite dagli SDK client e l'acquisizione di token da Azure Active Directory (se usate) vengono eseguite su HTTPS. 

Gli SDK di Azure ufficiali usano in genere il protocollo AMQP per l'invio e la ricezione di eventi da Hub eventi. L'opzione del protocollo AMQP-over-WebSockets viene eseguita sulla porta TCP 443 esattamente come l'API HTTP, ma è identica dal punto di vista funzionale al AMQP normale. Questa opzione ha una latenza di connessione iniziale superiore a causa di round trip di handshake aggiuntivi e un sovraccarico leggermente maggiore come compromesso per la condivisione della porta HTTPS. Se questa modalità è selezionata, la porta TCP 443 è sufficiente per la comunicazione. Le opzioni seguenti consentono di selezionare la modalità AMQP normale o AMQP WebSocket:

| Linguaggio | Opzione   |
| -------- | ----- |
| .NET     | Proprietà [EventHubConnectionOptions. TransportType](/dotnet/api/azure.messaging.eventhubs.eventhubconnectionoptions.transporttype?view=azure-dotnet&preserve-view=true) con [EventHubsTransportType. AmqpTcp](/dotnet/api/azure.messaging.eventhubs.eventhubstransporttype?view=azure-dotnet&preserve-view=true) o [EventHubsTransportType. AmqpWebSockets](/dotnet/api/azure.messaging.eventhubs.eventhubstransporttype?view=azure-dotnet&preserve-view=true) |
| Java     | [com. Microsoft. Azure. Eventhubs. EventProcessorClientBuilder. TransportType](/java/api/com.azure.messaging.eventhubs.eventprocessorclientbuilder.transporttype?view=azure-java-stable&preserve-view=true) con [AmqpTransportType. AMQP](/java/api/com.azure.core.amqp.amqptransporttype?view=azure-java-stable&preserve-view=true) o [AmqpTransportType.AMQP_WEB_SOCKETS](/java/api/com.azure.core.amqp.amqptransporttype?view=azure-java-stable&preserve-view=true) |
| Nodo  | [EventHubConsumerClientOptions](/javascript/api/@azure/event-hubs/eventhubconsumerclientoptions?view=azure-node-latest&preserve-view=true) dispone di una `webSocketOptions` Proprietà. |
| Python | [EventHubConsumerClient.transport_type](/python/api/azure-eventhub/azure.eventhub.eventhubconsumerclient?view=azure-python&preserve-view=true) con [TransportType. AMQP](/python/api/azure-eventhub/azure.eventhub.transporttype?view=azure-python) o [TransportType. AmqpOverWebSocket](/python/api/azure-eventhub/azure.eventhub.transporttype?view=azure-python&preserve-view=true) |

### <a name="what-ip-addresses-do-i-need-to-allow"></a>Quali indirizzi IP è necessario consentire?
Quando si lavora con Azure, a volte è necessario consentire URL o intervalli di indirizzi IP specifici nel firewall o nel proxy aziendale per accedere a tutti i servizi di Azure in uso o che si sta tentando di usare. Verificare che il traffico sia consentito sugli indirizzi IP usati da Hub eventi. Per gli indirizzi IP usati da Hub eventi di Azure: vedere [intervalli IP e tag di servizio di Azure-cloud pubblico](https://www.microsoft.com/download/details.aspx?id=56519).

Verificare anche che l'indirizzo IP per lo spazio dei nomi sia consentito. Per trovare gli indirizzi IP giusti per consentire le connessioni, attenersi alla procedura seguente:

1. Al prompt dei comandi eseguire il comando seguente: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Annotare l'indirizzo IP restituito in `Non-authoritative answer`. 

Se si usa la **ridondanza della zona** per lo spazio dei nomi, è necessario eseguire alcuni passaggi aggiuntivi: 

1. Per prima cosa, eseguire nslookup nello spazio dei nomi.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Annotare il nome nella sezione di **risposta non autorevole**, presente in uno dei formati seguenti: 

    ```
    <name>-s1.cloudapp.net
    <name>-s2.cloudapp.net
    <name>-s3.cloudapp.net
    ```
3. Eseguire nslookup per ciascuna di esse con suffissi S1, S2 e S3 per ottenere gli indirizzi IP di tutte e tre le istanze in esecuzione in tre zone di disponibilità. 

    > [!NOTE]
    > L'indirizzo IP restituito dal `nslookup` comando non è un indirizzo IP statico. Tuttavia rimane costante fino a quando la distribuzione sottostante non viene eliminata o spostata in un cluster diverso.

### <a name="where-can-i-find-client-ip-sending-or-receiving-messages-to-my-namespace"></a>Dove è possibile trovare gli indirizzi IP client che inviano o ricevono messaggi nello spazio dei nomi?
Per prima cosa, abilitare il [filtro IP](../articles/event-hubs/event-hubs-ip-filtering.md) nello spazio dei nomi. 

Abilitare quindi i log di diagnostica per [gli eventi di connessione alla rete virtuale di hub eventi](../articles/event-hubs/event-hubs-diagnostic-logs.md#event-hubs-virtual-network-connection-event-schema) seguendo le istruzioni riportate nell' [Abilitazione dei log di diagnostica](../articles/event-hubs/event-hubs-diagnostic-logs.md#enable-diagnostic-logs). Verrà visualizzato l'indirizzo IP per il quale la connessione viene negata.

```json
{
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "IPAddress": "1.2.3.4",
    "Action": "Deny Connection",
    "Reason": "IPAddress doesn't belong to a subnet with Service Endpoint enabled.",
    "Count": "65",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "EventHubVNetConnectionEvent"
}
```

> [!IMPORTANT]
> I log di rete virtuale vengono generati solo se lo spazio dei nomi consente l'accesso da **indirizzi IP specifici** (regole di filtro IP). Se non si vuole limitare l'accesso allo spazio dei nomi usando queste funzionalità e si vuole comunque ottenere i log di rete virtuale per tenere traccia degli indirizzi IP dei client che si connettono allo spazio dei nomi di hub eventi, è possibile usare la soluzione alternativa seguente: abilitare il filtro IP e aggiungere l'intervallo IPv4 totale indirizzabile (1.0.0.0/1-255.0.0.0/1). Hub eventi non supporta gli intervalli di indirizzi IPv6. 
