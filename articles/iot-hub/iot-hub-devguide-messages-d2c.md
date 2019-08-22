---
title: Informazioni sul routing dei messaggi di hub IoT di Azure | Microsoft Docs
description: Guida per gli sviluppatori - Come usare il routing dei messaggi per inviare messaggi da dispositivo a cloud. Include informazioni sull'invio di dati di telemetria e non.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: asrastog
ms.openlocfilehash: 6ee9e334c10bd2d0f291b5fd1bb547ba3ba83ddb
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877179"
---
# <a name="use-iot-hub-message-routing-to-send-device-to-cloud-messages-to-different-endpoints"></a>Usare il routing dei messaggi dell'hub Internet per inviare messaggi da dispositivo a cloud a endpoint diversi

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Il routing dei messaggi consente di inviare messaggi dai dispositivi ai servizi cloud in modo automatizzato, scalabile e affidabile. Il routing dei messaggi può essere usato per: 

* **Inviare i messaggi di telemetria del dispositivo e gli eventi** in particolare gli eventi del ciclo di vita del dispositivo e gli eventi di modifica del dispositivo gemello all'endpoint predefinito e agli endpoint personalizzati. Informazioni sugli [endpoint di routing](#routing-endpoints).

* **Filtrare i dati prima del routing a vari endpoint** applicando query complesse. Il routing dei messaggi consente di eseguire query sulle proprietà dei messaggi e sul corpo del messaggio, nonché sui tag e sulle proprietà del dispositivo gemello. Altre informazioni sull'uso delle [query nel routing dei messaggi](iot-hub-devguide-routing-query-syntax.md).

Hub IoT richiede l'accesso in scrittura a questi endpoint di servizio affinché il routing dei messaggi funzioni correttamente. Se si configurano gli endpoint tramite il Portale di Azure, verranno aggiunte le autorizzazioni necessarie. Accertarsi di configurare i servizi per supportare la velocità effettiva prevista. Durante la prima configurazione della soluzione IoT, potrebbe essere necessario monitorare gli endpoint aggiuntivi e quindi apportare le modifiche necessarie per il carico effettivo.

L'hub IoT definisce un [formato comune](iot-hub-devguide-messages-construct.md) per tutta la messaggistica da dispositivo a cloud per l'interoperabilità tra i protocolli. Se un messaggio corrisponde a più route che puntano allo stesso endpoint, l'hub IoT invia il messaggio a questo endpoint una sola volta. Pertanto, non è necessario configurare la deduplicazione nella coda o nell'argomento del bus di servizio. Nelle code partizionate, l'affinità della partizione garantisce l'ordinamento dei messaggi. Usare questa esercitazione per imparare a [configurare il routing dei messaggi](tutorial-routing.md).

## <a name="routing-endpoints"></a>Endpoint di routing

Un hub IoT ha un endpoint incorporato predefinito (**messaggi/eventi**) compatibile con Hub eventi. È possibile creare [endpoint personalizzati](iot-hub-devguide-endpoints.md#custom-endpoints) per indirizzare i messaggi collegando altri servizi nella sottoscrizione all'hub IoT. L'hub IoT supporta attualmente i servizi seguenti come endpoint personalizzati:

### <a name="built-in-endpoint"></a>Endpoint predefinito

È possibile usare l'[integrazione standard di Hub eventi e gli SDK](iot-hub-devguide-messages-read-builtin.md) per ricevere i messaggi da dispositivo a cloud dall'endpoint predefinito (**messaggi/eventi**). Una volta creata una route, i dati vengono interrotti verso l'endpoint incorporato, a meno che non venga creata una route per tale endpoint.

### <a name="azure-blob-storage"></a>Archivio BLOB di Azure

L'hub Internet delle cose supporta la scrittura di dati nell'archivio BLOB di Azure in formato [Apache avro](https://avro.apache.org/) e in formato JSON. La funzionalità di codifica del formato JSON è disponibile a livello generale in tutte le aree in cui è disponibile l'hub Internet. Il valore predefinito è AVRO. Il formato di codifica può essere impostato solo quando viene configurato l'endpoint di archiviazione BLOB. Non è possibile modificare il formato per un endpoint esistente. Quando si usa la codifica JSON, è necessario impostare contentType su **Application/JSON** e ContentEncoding su **UTF-8** nelle [proprietà del sistema](iot-hub-devguide-routing-query-syntax.md#system-properties)del messaggio. Entrambi i valori non fanno distinzione tra maiuscole e minuscole. Se la codifica del contenuto non è impostata, l'hub Internet scriverà i messaggi nel formato con codifica base 64. È possibile selezionare il formato di codifica usando l'API REST di creazione o aggiornamento dell'hub Internet, in particolare [RoutingStorageContainerProperties](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate#routingstoragecontainerproperties), il portale di Azure, l'interfaccia della riga di comando di [Azure](https://docs.microsoft.com/cli/azure/iot/hub/routing-endpoint?view=azure-cli-latest)o [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.iothub/add-aziothubroutingendpoint?view=azps-1.3.0). Il diagramma seguente illustra come selezionare il formato di codifica nel portale di Azure.

![Codifica dell'endpoint di archiviazione BLOB](./media/iot-hub-devguide-messages-d2c/blobencoding.png)

L'hub Internet delle cose supporta anche il routing dei messaggi agli account ADLS Gen2, ovvero account di archiviazione gerarchici abilitati per gli [spazi dei nomi](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace)basati sull'archiviazione BLOB. Questa funzionalità è disponibile in anteprima pubblica per i nuovi account ADLS Gen2 negli Stati Uniti occidentali 2 e negli Stati Uniti centro-occidentali. Questa funzionalità verrà implementata a breve da tutte le aree cloud.

L'hub IoT crea batch di messaggi e scrive i dati in un BLOB non appena il batch raggiunge una determinata dimensione oppure dopo che è trascorso un determinato intervallo di tempo. Per impostazione predefinita, l'hub IoT usa la convenzione di denominazione di file seguente:

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

È possibile usare qualsiasi convenzione di denominazione. È tuttavia necessario usare tutti i token elencati. L'hub IoT scriverà in un BLOB vuoto se non sono presenti dati da scrivere.

Quando si esegue il routing all'archivio BLOB è consigliabile integrare i BLOB e quindi eseguirne l'iterazione per garantire che tutti i contenitori vengano letti senza fare ipotesi sulla partizione. L'intervallo di partizione potrebbe potenzialmente cambiare durante un [failover avviato da Microsoft](iot-hub-ha-dr.md#microsoft-initiated-failover)o un [failover manuale](iot-hub-ha-dr.md#manual-failover) dell'hub IoT. È possibile usare l' [API List Blobs](https://docs.microsoft.com/rest/api/storageservices/list-blobs) per enumerare l'elenco di BLOB. Per informazioni, vedere l'esempio seguente.

   ```csharp
        public void ListBlobsInContainer(string containerName, string iothub)
        {
            var storageAccount = CloudStorageAccount.Parse(this.blobConnectionString);
            var cloudBlobContainer = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
            if (cloudBlobContainer.Exists())
            {
                var results = cloudBlobContainer.ListBlobs(prefix: $"{iothub}/");
                foreach (IListBlobItem item in results)
                {
                    Console.WriteLine(item.Uri);
                }
            }
        }
   ```

### <a name="service-bus-queues-and-service-bus-topics"></a>Code e argomenti del bus di servizio

Nelle code e negli argomenti del bus di servizio usati come endpoint dell'hub IoT non devono essere abilitati le **sessioni** e il **rilevamento duplicati**. Se una di queste opzioni è abilitata, l'endpoint risulta **non raggiungibile** nel portale di Azure.

### <a name="event-hubs"></a>Hub eventi

Oltre all'endpoint compatibile con Hub eventi predefinito, è anche possibile indirizzare i dati a endpoint personalizzati di tipo Hub eventi. 

## <a name="reading-data-that-has-been-routed"></a>Lettura dei dati indirizzati

È possibile configurare una route seguendo questa [esercitazione](tutorial-routing.md).

Usare le esercitazioni seguenti per informazioni su come leggere messaggi da un endpoint.

* Lettura dall'[endpoint predefinito](quickstart-send-telemetry-node.md)

* Lettura dall'[archivio BLOB](../storage/blobs/storage-blob-event-quickstart.md)

* Lettura da [Hub eventi](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

* Lettura dalle [code del bus di servizio](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)

* Lettura dagli [argomenti del bus di servizio](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions)

## <a name="fallback-route"></a>Route di fallback

La route di fallback invia tutti i messaggi che non soddisfano le condizioni di query su una delle route esistenti all'istanza predefinita di Hub eventi (**messaggi/eventi**), compatibile con [Hub eventi](/azure/event-hubs/). Se il routing dei messaggi è attivato, è possibile abilitare la funzionalità di route di fallback. Una volta creata una route, i dati vengono interrotti verso l'endpoint incorporato, a meno che non venga creata una route per tale endpoint. Se non esistono route verso l'endpoint predefinito ed è abilitata una route di fallback, solo i messaggi che non corrispondono ad alcuna condizione di query sulle route verranno inviati all'endpoint predefinito. Inoltre, se vengono eliminate tutte le route esistenti, è necessario abilitare la route di fallback per ricevere tutti i dati sull'endpoint predefinito.

È possibile abilitare o disabilitare la route di fallback nel pannello routing dei messaggi portale di Azure >. È anche possibile usare Azure Resource Manager per [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) per usare un endpoint personalizzato per la route di fallback.

## <a name="non-telemetry-events"></a>Eventi non di telemetria

Oltre ai dati di telemetria del dispositivo, il routing dei messaggi consente anche l'invio di eventi di modifica del dispositivo gemello, eventi del ciclo di vita del dispositivo e eventi di modifica del gemello digitale (in anteprima Ad esempio, se viene creata una route con origine dati impostata su **Eventi di modifica del dispositivo gemello**, l'hub IoT invia messaggi all'endpoint che contengono la modifica nel dispositivo gemello. Analogamente, se viene creata una route con l'origine dati impostata su **eventi del ciclo**di vita del dispositivo, l'hub Internet invia un messaggio che indica se il dispositivo è stato eliminato o creato. Infine, nell'ambito del [plug and Play di anteprima pubblica](../iot-pnp/overview-iot-plug-and-play.md), uno sviluppatore può creare route con l'origine dati impostata su **eventi di modifica digitale gemello** e l'hub Internet invia messaggi ogni volta che viene impostata o modificata una [proprietà](../iot-pnp/iot-plug-and-play-glossary.md) del dispositivo gemello digitale, un dispositivo [gemello digitale ](../iot-pnp/iot-plug-and-play-glossary.md)viene sostituito o quando si verifica un evento di modifica per il dispositivo gemello sottostante.

L'hub Internet si [integra anche con griglia di eventi di Azure](iot-hub-event-grid.md) per pubblicare gli eventi del dispositivo per supportare integrazioni in tempo reale e automazione dei flussi di lavoro in base a questi eventi. Vedere le principali [differenze tra il routing dei messaggi e Griglia di eventi](iot-hub-event-grid-routing-comparison.md) per determinare la soluzione ottimale per il proprio scenario.

## <a name="testing-routes"></a>Test delle route

Quando si crea una nuova route o si modifica una route esistente, è consigliabile testare la query di route con un messaggio di esempio. È possibile testare singole route o testarle tutte contemporaneamente. Nessuno messaggio viene indirizzato agli endpoint durante il test. Per i test è possibile usare portale di Azure, Azure Resource Manager, Azure PowerShell e l'interfaccia della riga di comando di Azure. I risultati consentono di identificare se il messaggio di esempio corrisponde alla query, il messaggio non corrisponde alla query o se il test non è stato eseguito perché la sintassi del messaggio o della query di esempio non è corretta. Per altre informazioni, vedere [Test Route](/rest/api/iothub/iothubresource/testroute) (Testare una route) e [Test all routes](/rest/api/iothub/iothubresource/testallroutes) (Testare tutte le route).

## <a name="latency"></a>Latency

Quando si indirizzano i messaggi di telemetria da dispositivo a cloud tramite endpoint predefiniti, si verifica un lieve aumento della latenza end-to-end dopo la creazione della prima route.

Nella maggior parte dei casi, l'aumento medio della latenza è inferiore a 500 ms. È possibile monitorare la latenza con la metrica dell'hub IoT **Routing: message latency for messages/events** (Routing: latenza messaggi per messaggi/eventi) o **d2c.endpoints.latency.builtIn.events**. La creazione o l'eliminazione di una route successiva alla prima non influisce sulla latenza end-to-end.

## <a name="monitoring-and-troubleshooting"></a>Monitoraggio e risoluzione dei problemi

L'hub Internet delle cose offre diverse metriche relative al routing e agli endpoint per offrire una panoramica dell'integrità dell'hub e dei messaggi inviati. È possibile combinare le informazioni da più metriche per individuare la causa radice dei problemi. Usare, ad esempio, **routing metrica: messaggi** di telemetria eliminati o **D2C. telemetria. in uscita. Dropped** per identificare il numero di messaggi eliminati quando non corrispondono a query su una delle route e la route di fallback è stata disabilitata. In [Metriche di Hub IoT](iot-hub-metrics.md) sono elencate tutte le metriche abilitate per impostazione predefinita per l'hub IoT.

È possibile usare l'API REST per [ottenere l'integrità dell'endpoint](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) per ottenere [lo stato di integrità](iot-hub-devguide-endpoints.md#custom-endpoints) degli endpoint. Si consiglia di usare le [metriche dell'hub](iot-hub-metrics.md) delle cose correlate alla latenza dei messaggi di routing per identificare ed eseguire il debug degli errori quando l'integrità dell'endpoint è inattiva o non integro. Per il tipo di endpoint Hub eventi, ad esempio, è possibile monitorare **D2C. Endpoints. latence. eventHubs**. Lo stato di un endpoint non integro verrà aggiornato a integro quando lo stato di integrità dell'hub Internet è stabile.

Usando i log di diagnostica delle **Route** nelle [impostazioni di diagnostica](../iot-hub/iot-hub-monitor-resource-health.md)di monitoraggio di Azure, è possibile rilevare gli errori che si verificano durante la valutazione di una query di routing e l'integrità degli endpoint, ad esempio quando un endpoint è inattivo. Questi log di diagnostica possono essere inviati a log di monitoraggio di Azure, Hub eventi o archiviazione di Azure per l'elaborazione personalizzata.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come creare route di messaggi, vedere [Elaborare messaggi da dispositivo a cloud dell'hub IoT usando route](tutorial-routing.md).

* [Come inviare messaggi da dispositivo a cloud](quickstart-send-telemetry-node.md)

* Per informazioni sugli SDK che è possibile usare per inviare i messaggi da dispositivo a cloud, vedere [Azure IoT SDK](iot-hub-devguide-sdks.md).
