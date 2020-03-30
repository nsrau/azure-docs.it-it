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
ms.openlocfilehash: d10744f2536cdf89115cdccd0bea6f1e5155774c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370458"
---
# <a name="use-iot-hub-message-routing-to-send-device-to-cloud-messages-to-different-endpoints"></a>Usare il routing dei messaggi dell'hub IoT per inviare messaggi da dispositivo a cloud a endpoint diversiUse IoT Hub message routing to send device-to-cloud messages to different endpoints

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Il routing dei messaggi consente di inviare messaggi dai dispositivi ai servizi cloud in modo automatizzato, scalabile e affidabile. Il routing dei messaggi può essere usato per: 

* **Inviare i messaggi di telemetria del dispositivo e gli eventi** in particolare gli eventi del ciclo di vita del dispositivo e gli eventi di modifica del dispositivo gemello all'endpoint predefinito e agli endpoint personalizzati. Informazioni sugli [endpoint di routing](#routing-endpoints).

* **Filtrare i dati prima del routing a vari endpoint** applicando query complesse. Il routing dei messaggi consente di eseguire query sulle proprietà dei messaggi e sul corpo del messaggio, nonché sui tag e sulle proprietà del dispositivo gemello. Altre informazioni sull'uso delle [query nel routing dei messaggi](iot-hub-devguide-routing-query-syntax.md).

Hub IoT richiede l'accesso in scrittura a questi endpoint di servizio affinché il routing dei messaggi funzioni correttamente. Se si configurano gli endpoint tramite il Portale di Azure, verranno aggiunte le autorizzazioni necessarie. Accertarsi di configurare i servizi per supportare la velocità effettiva prevista. Ad esempio, se si usa Hub eventi come endpoint personalizzato, è necessario configurare le unità di velocità effettiva per l'hub eventi in modo che possa gestire l'ingresso degli eventi che si prevede di inviare tramite il routing dei messaggi dell'hub IoT.For example, if you are using Event Hubs as a custom endpoint, you must configure the **throughput units** for that event hub so it can handle the ingress of events you plan to send via IoT Hub message routing. Analogamente, quando si usa una coda del bus di servizio come endpoint, è necessario configurare la **dimensione massima** per garantire che la coda possa contenere tutti i dati in ingresso, fino a quando non viene in uscita dai consumer. Durante la prima configurazione della soluzione IoT, potrebbe essere necessario monitorare gli endpoint aggiuntivi e quindi apportare le modifiche necessarie per il carico effettivo.

L'hub IoT definisce un [formato comune](iot-hub-devguide-messages-construct.md) per tutta la messaggistica da dispositivo a cloud per l'interoperabilità tra i protocolli. Se un messaggio corrisponde a più route che puntano allo stesso endpoint, l'hub IoT invia il messaggio a questo endpoint una sola volta. Pertanto, non è necessario configurare la deduplicazione nella coda o nell'argomento del bus di servizio. Nelle code partizionate, l'affinità della partizione garantisce l'ordinamento dei messaggi. Usare questa esercitazione per imparare a [configurare il routing dei messaggi](tutorial-routing.md).

## <a name="routing-endpoints"></a>Endpoint di routing

Un hub IoT ha un endpoint incorporato predefinito (**messaggi/eventi**) compatibile con Hub eventi. È possibile creare [endpoint personalizzati](iot-hub-devguide-endpoints.md#custom-endpoints) per indirizzare i messaggi collegando altri servizi nella sottoscrizione all'hub IoT. 

Ogni messaggio viene instradato a tutti gli endpoint le cui query di routing corrispondono. In altre parole, un messaggio può essere instradato a più endpoint.

L'hub IoT supporta attualmente i servizi seguenti come endpoint personalizzati:

### <a name="built-in-endpoint"></a>Endpoint predefinito

È possibile usare l'[integrazione standard di Hub eventi e gli SDK](iot-hub-devguide-messages-read-builtin.md) per ricevere i messaggi da dispositivo a cloud dall'endpoint predefinito (**messaggi/eventi**). Una volta creata un'route, i dati smette di fluire verso l'endpoint predefinito, a meno che non venga creata un'route per tale endpoint.

### <a name="azure-storage"></a>Archiviazione di Azure

Esistono due servizi di archiviazione Che l'hub IoT può instradare i messaggi, ad esempio gli account [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) e Azure Data Lake Storage [Gen2](../storage/blobs/data-lake-storage-introduction.md) (ADLS Gen2). Gli account di archiviazione di Azure Data Lake sono account di archiviazione [gerarchici](../storage/blobs/data-lake-storage-namespace.md)abilitati per lo spazio dei nomi basati sull'archiviazione BLOB. Entrambi usano BLOB per l'archiviazione.

L'hub IoT supporta la scrittura di dati in Archiviazione di Azure nel formato [Apache Avro](https://avro.apache.org/) e in formato JSON. Il valore predefinito è AVRO. Il formato di codifica può essere impostato solo quando è configurato l'endpoint di archiviazione BLOB. Impossibile modificare il formato per un endpoint esistente. Quando si usa la codifica JSON, è necessario impostare contentType su **application/json** e contentEncoding su **UTF-8** nelle proprietà del sistema di [messaggi.](iot-hub-devguide-routing-query-syntax.md#system-properties) Entrambi questi valori non fanno distinzione tra maiuscole e minuscole. Se la codifica del contenuto non è impostata, l'hub IoT scriverà i messaggi in formato codificato in base 64. È possibile selezionare il formato di codifica usando l'API REST Create or Update dell'hub IoT, in particolare [RoutingStorageContainerProperties](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate#routingstoragecontainerproperties), il portale di Azure, [l'interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/iot/hub/routing-endpoint?view=azure-cli-latest)di Azure o Azure [PowerShell.](https://docs.microsoft.com/powershell/module/az.iothub/add-aziothubroutingendpoint?view=azps-1.3.0) Il diagramma seguente mostra come selezionare il formato di codifica nel portale di Azure.The following diagram shows how to select the encoding format in the Azure portal.

![Codifica dell'endpoint dell'archiviazione BLOBBLOB storage endpoint encoding](./media/iot-hub-devguide-messages-d2c/blobencoding.png)

L'hub IoT crea un batch di messaggi e scrive i dati nell'archivio ogni volta che il batch raggiunge una determinata dimensione o è trascorso un determinato periodo di tempo. Per impostazione predefinita, l'hub IoT usa la convenzione di denominazione di file seguente: 

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

È possibile usare qualsiasi convenzione di denominazione. È tuttavia necessario usare tutti i token elencati. L'hub IoT scriverà in un BLOB vuoto se non sono presenti dati da scrivere.

È consigliabile elencare i BLOB o i file e quindi scorrerli sopra, per garantire che tutti i BLOB o i file vengano letti senza fare alcun sussing di partizione. L'intervallo di partizione potrebbe potenzialmente cambiare durante un [failover avviato da Microsoft](iot-hub-ha-dr.md#microsoft-initiated-failover)o un [failover manuale](iot-hub-ha-dr.md#manual-failover) dell'hub IoT. È possibile usare [l'API Elenca BLOB](https://docs.microsoft.com/rest/api/storageservices/list-blobs) per enumerare l'elenco di BLOB o l'API [ADLS Gen2](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/list) di elenco per l'elenco dei file. Vedere l'esempio seguente come guida.

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

> [!NOTE]
> Se l'account di archiviazione include configurazioni del firewall che limitano la connettività dell'hub IoT, è consigliabile usare un'eccezione di [prima parte attendibile di Microsoft](./virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing) (disponibile in determinate aree per hub IoT con identità del servizio gestito).

Per creare un account di archiviazione compatibile con Azure Data Lake Gen2, creare un nuovo account di archiviazione V2 e selezionare abilitato nel campo *Spazio dei nomi gerarchico* della scheda **Avanzate,** come illustrato nell'immagine seguente:To create an Azure Data Lake Gen2-compatible storage account, create a new V2 storage account and select *enabled on* the Hierarchical namespace field on the Advanced tab as shown in the following image:

![Selezionare l'archiviazione di Azure Date Lake Gen2Select Azure Date Lake Gen2 storage](./media/iot-hub-devguide-messages-d2c/selectadls2storage.png)


### <a name="service-bus-queues-and-service-bus-topics"></a>Code e argomenti del bus di servizio

Nelle code e negli argomenti del bus di servizio usati come endpoint dell'hub IoT non devono essere abilitati le **sessioni** e il **rilevamento duplicati**. Se una di queste opzioni è abilitata, l'endpoint risulta **non raggiungibile** nel portale di Azure.

> [!NOTE]
> Se la risorsa bus di servizio include configurazioni del firewall che limitano la connettività dell'hub IoT, è consigliabile usare un'eccezione di [prima parte attendibile di Microsoft](./virtual-network-support.md#egress-connectivity-to-service-bus-endpoints-for-routing) (disponibile in determinate aree per hub IoT con identità del servizio gestito).


### <a name="event-hubs"></a>Hub eventi

Oltre all'endpoint compatibile con Hub eventi predefinito, è anche possibile indirizzare i dati a endpoint personalizzati di tipo Hub eventi. 

> [!NOTE]
> Se la risorsa hub eventi include configurazioni del firewall che limitano la connettività dell'hub IoT, è consigliabile usare un'eccezione di [prima parte attendibile microsoft](./virtual-network-support.md#egress-connectivity-to-event-hubs-endpoints-for-routing) (disponibile in aree selezionate per hub IoT con identità del servizio gestito).


## <a name="reading-data-that-has-been-routed"></a>Lettura dei dati indirizzati

È possibile configurare una route seguendo questa [esercitazione](tutorial-routing.md).

Usare le esercitazioni seguenti per informazioni su come leggere messaggi da un endpoint.

* Lettura dall'[endpoint predefinito](quickstart-send-telemetry-node.md)

* Lettura dall'[archivio BLOB](../storage/blobs/storage-blob-event-quickstart.md)

* Lettura da [Hub eventi](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

* Lettura dalle [code del bus di servizio](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)

* Lettura dagli [argomenti del bus di servizio](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions)


## <a name="fallback-route"></a>Route di fallback

La route di fallback invia tutti i messaggi che non soddisfano le condizioni di query su una delle route esistenti all'istanza predefinita di Hub eventi (**messaggi/eventi**), compatibile con [Hub eventi](/azure/event-hubs/). Se il routing dei messaggi è attivato, è possibile abilitare la funzionalità di route di fallback. Una volta creata una route, i dati smette di fluire verso l'endpoint predefinito, a meno che non venga creata una route per tale endpoint. Se non esistono route verso l'endpoint predefinito ed è abilitata una route di fallback, solo i messaggi che non corrispondono ad alcuna condizione di query sulle route verranno inviati all'endpoint predefinito. Inoltre, se vengono eliminate tutte le route esistenti, è necessario abilitare la route di fallback per ricevere tutti i dati sull'endpoint predefinito.

È possibile abilitare/disabilitare la route di fallback nel pannello Distribuzione messaggi >portale di Azure.You can enable/disable the fallback route in the Azure portal->Message Routing blade. È anche possibile usare Azure Resource Manager per [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) per usare un endpoint personalizzato per la route di fallback.

## <a name="non-telemetry-events"></a>Eventi non di telemetria

Oltre alla telemetria del dispositivo, il routing dei messaggi consente anche l'invio di eventi di modifica gemelli del dispositivo, eventi del ciclo di vita del dispositivo ed eventi di modifica gemelli digitali (nell'anteprima pubblica). Ad esempio, se viene creata una route con origine dati impostata su **Eventi di modifica del dispositivo gemello**, l'hub IoT invia messaggi all'endpoint che contengono la modifica nel dispositivo gemello. Analogamente, se viene creata una route con l'origine dati impostata sugli **eventi del ciclo**di vita del dispositivo , l'hub IoT invia un messaggio che indica se il dispositivo è stato eliminato o creato. Infine, come parte [dell'anteprima pubblica Plug and Play IoT](../iot-pnp/overview-iot-plug-and-play.md), uno sviluppatore può creare route con origine dati impostata su eventi di modifica **gemelli digitali** e l'hub IoT invia messaggi ogni volta che una [proprietà](../iot-pnp/iot-plug-and-play-glossary.md) digitale gemella viene impostata o modificata, viene sostituito un [gemello digitale](../iot-pnp/iot-plug-and-play-glossary.md) o quando si verifica un evento di modifica per il dispositivo gemello sottostante.

[L'hub IoT si integra anche con Griglia di](iot-hub-event-grid.md) eventi di Azure per pubblicare eventi del dispositivo per supportare le integrazioni in tempo reale e l'automazione dei flussi di lavoro basati su questi eventi. Vedere le principali [differenze tra il routing dei messaggi e Griglia di eventi](iot-hub-event-grid-routing-comparison.md) per determinare la soluzione ottimale per il proprio scenario.

## <a name="testing-routes"></a>Test delle route

Quando si crea una nuova route o si modifica una route esistente, è consigliabile testare la query di route con un messaggio di esempio. È possibile testare singole route o testarle tutte contemporaneamente. Nessuno messaggio viene indirizzato agli endpoint durante il test. Il portale di Azure, Azure Resource Manager, Azure PowerShell e l'interfaccia della riga di comando di Azure possono essere usati per il test. I risultati consentono di identificare se il messaggio di esempio corrisponde alla query, se il messaggio non corrisponde alla query o se non è stato possibile eseguire il test perché il messaggio di esempio o la sintassi della query non sono corretti. Per altre informazioni, vedere [Test Route](/rest/api/iothub/iothubresource/testroute) (Testare una route) e [Test all routes](/rest/api/iothub/iothubresource/testallroutes) (Testare tutte le route).

## <a name="ordering-guarantees-with-at-least-once-delivery"></a>Garanzia di ordinazione con almeno una consegna

Il routing dei messaggi dell'hub IoT garantisce l'ordine e almeno una volta il recapito dei messaggi agli endpoint. Ciò significa che possono essere presenti messaggi duplicati e una serie di messaggi possono essere ritrasmessi in onore dell'ordine dei messaggi originale. Ad esempio, se l'ordine dei messaggi originale è [1,2,3,4], è possibile ricevere una sequenza di messaggi come [1,2,1,2,3,1,2,3,4]. La garanzia di ordinazione è che se si riceve un messaggio [1], sarebbe sempre seguito da [2,3,4].

Per la gestione dei duplicati dei messaggi, è consigliabile impostare un identificatore univoco nelle proprietà dell'applicazione del messaggio nel punto di origine, che in genere è un dispositivo o un modulo. Il servizio che utilizza i messaggi può gestire i messaggi duplicati usando questo identificatore.

## <a name="latency"></a>Latenza

Quando si indirizzano i messaggi di telemetria da dispositivo a cloud tramite endpoint predefiniti, si verifica un lieve aumento della latenza end-to-end dopo la creazione della prima route.

Nella maggior parte dei casi, l'aumento medio della latenza è inferiore a 500 ms. È possibile monitorare la latenza con la metrica dell'hub IoT **Routing: message latency for messages/events** (Routing: latenza messaggi per messaggi/eventi) o **d2c.endpoints.latency.builtIn.events**. La creazione o l'eliminazione di una route successiva alla prima non influisce sulla latenza end-to-end.

## <a name="monitoring-and-troubleshooting"></a>Monitoraggio e risoluzione dei problemi

L'hub IoT offre diverse metriche relative al routing e agli endpoint per fornire una panoramica dell'integrità dell'hub e dei messaggi inviati. È possibile combinare le informazioni da più metriche per individuare la causa radice dei problemi. Ad esempio, usare la metrica Routing: messaggi di **telemetria interrotti** o **d2c.telemetry.egress.dropped** per identificare il numero di messaggi che sono stati eliminati quando non corrispondevano alle query su nessuna delle route e la route di fallback è stata disabilitata. In [Metriche di Hub IoT](iot-hub-metrics.md) sono elencate tutte le metriche abilitate per impostazione predefinita per l'hub IoT.

È possibile usare l'API REST [Get Endpoint Health](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) per ottenere lo stato di [integrità](iot-hub-devguide-endpoints.md#custom-endpoints) degli endpoint. È consigliabile usare le [metriche dell'hub IoT](iot-hub-metrics.md) correlate alla latenza dei messaggi di routing per identificare ed eseguire il debug degli errori quando l'integrità dell'endpoint è inattiva o non integra. Ad esempio, per gli hub eventi di tipo endpoint, è possibile monitorare **d2c.endpoints.latency.eventHubs**. Lo stato di un endpoint non integro verrà aggiornato a integro quando l'hub IoT ha stabilito uno stato di integrità finale coerente.

Usando i log di diagnostica delle **route** nelle [impostazioni di diagnostica](../iot-hub/iot-hub-monitor-resource-health.md)di Monitoraggio di Azure, è possibile tenere traccia degli errori che si verificano durante la valutazione di una query di routing e dell'integrità dell'endpoint, come percepito dall'hub IoT, ad esempio quando un endpoint è morto. Questi log di diagnostica possono essere inviati ai log di Monitoraggio di Azure, agli hub eventi o ad Archiviazione di Azure per l'elaborazione personalizzata.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come creare route di messaggi, vedere [Elaborare messaggi da dispositivo a cloud dell'hub IoT usando route](tutorial-routing.md).

* [Come inviare messaggi da dispositivo a cloud](quickstart-send-telemetry-node.md)

* Per informazioni sugli SDK che è possibile usare per inviare i messaggi da dispositivo a cloud, vedere [Azure IoT SDK](iot-hub-devguide-sdks.md).
