---
title: Informazioni sul routing dei messaggi di hub IoT di Azure | Microsoft Docs
description: Guida per gli sviluppatori - Come usare il routing dei messaggi per inviare messaggi da dispositivo a cloud. Include informazioni sull'invio di dati di telemetria e non.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.openlocfilehash: 7c36ab2f0d4d3e5c772f8ef62c13161a2649362f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46966742"
---
# <a name="use-message-routing-to-send-device-to-cloud-messages-to-different-endpoints"></a>Usare il routing dei messaggi per inviare messaggi da dispositivo a cloud a endpoint diversi

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Il routing dei messaggi consente di inviare messaggi dai dispositivi ai servizi cloud in modo automatizzato, scalabile e affidabile. Il routing dei messaggi può essere usato per: 

* **Inviare i messaggi di telemetria del dispositivo nonché gli eventi** in particolare gli eventi del ciclo di vita del dispositivo e gli eventi di modifica del dispositivo gemello all'endpoint predefinito e agli endpoint personalizzati. Informazioni sugli [endpoint di routing](##routing-endpoints).

* **Filtrare i dati prima del routing a vari endpoint** applicando query complesse. Il routing dei messaggi consente di eseguire query sulle proprietà dei messaggi e sul corpo del messaggio, nonché sui tag e sulle proprietà del dispositivo gemello. Altre informazioni sull'uso delle [query nel routing dei messaggi](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

Hub IoT richiede l'accesso in scrittura a questi endpoint di servizio affinché il routing dei messaggi funzioni correttamente. Se si configurano gli endpoint tramite il Portale di Azure, verranno aggiunte le autorizzazioni necessarie. Accertarsi di configurare i servizi per supportare la velocità effettiva prevista. Durante la prima configurazione della soluzione IoT, potrebbe essere necessario monitorare gli endpoint aggiuntivi e quindi apportare le modifiche necessarie per il carico effettivo.

L'hub IoT definisce un [formato comune](../iot-hub/iot-hub-devguide-messages-construct.md) per tutta la messaggistica da dispositivo a cloud per l'interoperabilità tra i protocolli. Se un messaggio corrisponde a più route che puntano allo stesso endpoint, l'hub IoT invia il messaggio a questo endpoint una sola volta. Pertanto, non è necessario configurare la deduplicazione nella coda o nell'argomento del bus di servizio. Nelle code partizionate, l'affinità della partizione garantisce l'ordinamento dei messaggi. Usare questa esercitazione per imparare a [configurare il routing dei messaggi] (https://docs.microsoft.com/azure/iot-hub/tutorial-routing).

## <a name="routing-endpoints"></a>Endpoint di routing

Un hub IoT ha un endpoint incorporato predefinito (**messaggi/eventi**) compatibile con Hub eventi. È possibile creare [endpoint personalizzati](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-endpoints#custom-endpoints) per indirizzare i messaggi collegando altri servizi nella sottoscrizione all'hub IoT. L'hub IoT supporta attualmente i servizi seguenti come endpoint personalizzati:

### <a name="built-in-endpoint"></a>Endpoint predefinito
È possibile usare l'[integrazione standard di Hub eventi e gli SDK](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-read-builtin) per ricevere i messaggi da dispositivo a cloud dall'endpoint predefinito (**messaggi/eventi**). Si noti che quando viene creata una route, i dati non vengono più trasmessi all'endpoint predefinito, a meno che non venga creata una route verso tale endpoint.

### <a name="azure-blob-storage"></a>Archiviazione BLOB di Azure
L'hub IoT supporta solo la scrittura dei dati in Archiviazione BLOB di Azure nel formato [Apache Avro](http://avro.apache.org/). L'hub IoT crea batch di messaggi e scrive i dati in un BLOB non appena il batch raggiunge una determinata dimensione oppure dopo che è trascorso un determinato intervallo di tempo.

Per impostazione predefinita, l'hub IoT usa la convenzione di denominazione di file seguente:
```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

È possibile usare qualsiasi convenzione di denominazione. È tuttavia necessario usare tutti i token elencati. L'hub IoT scriverà in un BLOB vuoto se non sono presenti dati da scrivere.

### <a name="service-bus-queues-and-service-bus-topics"></a>Code e argomenti del bus di servizio
Nelle code e negli argomenti del bus di servizio usati come endpoint dell'hub IoT non devono essere abilitati le **sessioni** e il **rilevamento duplicati**. Se una di queste opzioni è abilitata, l'endpoint risulta **non raggiungibile** nel portale di Azure.

### <a name="event-hubs"></a>Hub eventi
Oltre all'endpoint compatibile con Hub eventi predefinito, è anche possibile indirizzare i dati a endpoint personalizzati di tipo Hub eventi. 

Quando si usa il routing insieme a endpoint personalizzati, i messaggi vengono recapitati solo all'endpoint predefinito se non corrispondono ad alcuna regola. Per recapitare i messaggi all'endpoint predefinito e a endpoint personalizzati, aggiungere una route per l'invio dei messaggi all'endpoint Eventi.

## <a name="reading-data-that-has-been-routed"></a>Lettura dei dati indirizzati
È possibile configurare una route seguendo questa [esercitazione](https://docs.microsoft.com/azure/iot-hub/tutorial-routing).

Usare le esercitazioni seguenti per informazioni su come leggere messaggi da un endpoint.

* Lettura dall'[endpoint predefinito](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-node)
* Lettura dall'[archivio BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart)
* Lettura da [Hub eventi](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send)
* Lettura dalle [code del bus di servizio](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-get-started-with-queues)
* Lettura dagli [argomenti del bus di servizio](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions)

## <a name="fallback-route"></a>Route di fallback
La route di fallback invia tutti i messaggi che non soddisfano le condizioni di query su una delle route esistenti all'istanza predefinita di Hub eventi (**messaggi/eventi**), compatibile con [Hub eventi](https://docs.microsoft.com/azure/event-hubs/). Se il routing dei messaggi è attivato, è possibile abilitare la funzionalità di route di fallback. Si noti che quando viene creata una route, i dati non vengono più trasmessi all'endpoint predefinito, a meno che non venga creata una route verso tale endpoint. Se non esistono route verso l'endpoint predefinito ed è abilitata una route di fallback, solo i messaggi che non corrispondono ad alcuna condizione di query sulle route verranno inviati all'endpoint predefinito. Inoltre, se vengono eliminate tutte le route esistenti, è necessario abilitare la route di fallback per ricevere tutti i dati sull'endpoint predefinito. 

È possibile abilitare/disabilitare la route di fallback nel portale di Azure -> pannello Routing messaggi. È anche possibile usare Azure Resource Manager per [FallbackRouteProperties](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) per usare un endpoint personalizzato per la route di fallback.

## <a name="non-telemetry-events"></a>Eventi non di telemetria
Oltre alla telemetria del dispositivo, il routing dei messaggi consente anche l'invio degli eventi di modifica del dispositivo gemello e degli eventi del ciclo di vita del dispositivo. Ad esempio, se viene creata una route con origine dati impostata su **Eventi di modifica del dispositivo gemello**, l'hub IoT invia messaggi all'endpoint che contengono la modifica nel dispositivo gemello. In modo analogo, se viene creata una route con origine dati impostata su **Eventi relativi al ciclo di vita del dispositivo**, l'hub IoT invierà un messaggio che indica se il dispositivo è stato eliminato o creato. 
[L'hub IoT si integra anche con Griglia di eventi di Azure](iot-hub-event-grid.md) per pubblicare gli eventi dei dispositivi per supportare le integrazioni in tempo reale e l'automazione dei flussi di lavoro in base a questi eventi. Vedere le principali [differenze tra il routing dei messaggi e Griglia di eventi](iot-hub-event-grid-routing-comparison.md) per determinare la soluzione ottimale per il proprio scenario.

## <a name="testing-routes"></a>Test delle route
Quando si crea una nuova route o si modifica una route esistente, è consigliabile testare la query di route con un messaggio di esempio. È possibile testare singole route o testarle tutte contemporaneamente. Nessuno messaggio viene indirizzato agli endpoint durante il test. Per i test è possibile usare il portale di Azure, Azure Resource Manager, Azure PowerShell e l'interfaccia della riga di comando di Azure. I risultati consentono di scoprire se il messaggio di esempio corrisponde alla query, se non corrisponde oppure se non è possibile eseguire il test perché il messaggio di esempio o la sintassi della query non sono corretti. Per altre informazioni, vedere [Test Route](https://docs.microsoft.com/rest/api/iothub/iothubresource/testroute) (Testare una route) e [Test all routes](https://docs.microsoft.com/rest/api/iothub/iothubresource/testallroutes) (Testare tutte le route).

## <a name="latency"></a>Latenza
Quando si indirizzano i messaggi di telemetria da dispositivo a cloud tramite endpoint predefiniti, si verifica un lieve aumento della latenza end-to-end dopo la creazione della prima route.

Nella maggior parte dei casi l'aumento medio della latenza è inferiore a 500 ms. È possibile monitorare la latenza con la metrica dell'hub IoT **Routing: message latency for messages/events** (Routing: latenza messaggi per messaggi/eventi) o **d2c.endpoints.latency.builtIn.events**. La creazione o l'eliminazione di una route successiva alla prima non influisce sulla latenza end-to-end.

## <a name="monitoring-and-troubleshooting"></a>Monitoraggio e risoluzione dei problemi
L'hub IoT offre diverse metriche correlate al routing e agli endpoint per offrire una panoramica dell'integrità dell'hub e dei messaggi inviati. È possibile combinare le informazioni da più metriche per individuare la causa radice dei problemi. Ad esempio usare la metrica **Routing: telemetry messages dropped** (Routing: messaggi di telemetria eliminati) oppure **d2c.telemetry.egress.dropped** per identificare il numero di messaggi eliminati perché non corrispondenti alle query su qualsiasi route e con la route di fallback disabilitata. In [Metriche di Hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics) sono elencate tutte le metriche abilitate per impostazione predefinita per l'hub IoT.

È possibile usare i log di diagnostica delle **route** nelle [impostazioni di diagnostica](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health) di Monitoraggio di Azure per tenere traccia degli errori che si verificano durante la valutazione dell'integrità di una query e un endpoint di routing nella percezione dell'hub IoT, ad esempio quando un endpoint è inattivo. Questi log di diagnostica generati possono essere inviati a Log Analytics, Hub eventi o Archiviazione di Azure per l'elaborazione personalizzata.

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni su come creare route i messaggi, vedere l'esercitazione [Elaborare messaggi da dispositivo a cloud dell'hub IoT usando route](../iot-hub/tutorial-routing.md).
* Le [guide introduttive](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-node) descrivono come inviare messaggi da dispositivo a cloud da dispositivi simulati.
* Per informazioni sugli SDK che è possibile usare per inviare i messaggi da dispositivo a cloud, vedere [Azure IoT SDK](../iot-hub/iot-hub-devguide-sdks.md).
