---
title: Monitorare le metriche di servizi multimediali e i log di diagnostica tramite Monitoraggio di Azure | Microsoft Docs
description: Questo articolo offre una panoramica su come monitorare le metriche di servizi multimediali e i log di diagnostica tramite Monitoraggio di Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2019
ms.author: juliako
ms.openlocfilehash: bbf43ecb07947fad8cc1ee064d2038e4a21d4444
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65964760"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs"></a>Monitorare le metriche di servizi multimediali e i log di diagnostica

[Monitoraggio di Azure](../../azure-monitor/overview.md) consente di monitorare le metriche e log di diagnostica che consentono di comprendere prestazioni delle applicazioni. Tutti i dati raccolti da monitoraggio di Azure rientra in uno dei due tipi fondamentali, metriche e log. È possibile monitorare i log di diagnostica di servizi multimediali e creare avvisi e notifiche per i log e delle metriche raccolte. È possibile visualizzare e analizzare i dati delle metriche usando [Esplora metriche](../../azure-monitor/platform/metrics-getting-started.md). È possibile inviare log ad [archiviazione di Azure](https://azure.microsoft.com/services/storage/), trasmetterli agli [hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/)e di esportarli per [Log Analitica](https://azure.microsoft.com/services/log-analytics/), o usare servizi di terze parti 3rd.

Per una panoramica dettagliata, vedere [metriche di monitoraggio di Azure](../../azure-monitor/platform/data-platform.md) e [log di diagnostica di monitoraggio di Azure](../../azure-monitor/platform/diagnostic-logs-overview.md).

Questo argomento illustra attualmente disponibili [metriche di servizi multimediali](#media-services-metrics) e [log di diagnostica di servizi multimediali](#media-services-diagnostic-logs).

## <a name="media-services-metrics"></a>Metriche di servizi multimediali

Le metriche vengono raccolte a intervalli regolari indipendentemente dal fatto che il valore venga modificato. Sono utili per la generazione di avvisi perché possono essere campionate di frequente e perché un avviso può essere generato rapidamente con una logica relativamente semplice.

Attualmente, i seguenti servizi multimediali [gli endpoint di Streaming](https://docs.microsoft.com/rest/api/media/streamingendpoints) metriche vengono generate da Azure:

|Metrica|`Display name`|Descrizione|
|---|---|---|
|Requests|Requests|Fornisce i dettagli per n. totale di richieste elaborate dall'Endpoint di Streaming.|
|Dati in uscita|Dati in uscita|Numero totale di byte in uscita. Ad esempio, i byte trasmessi dall'Endpoint di Streaming.|
|SuccessE2ELatency|Latenza end to end esito positivo| Fornisce informazioni sulla latenza end-to-end di richieste eseguite correttamente.|

Ad esempio, per ottenere le metriche "dei servizi esterni" con CLI, eseguire questo `az monitor metrics` riga di comando:

```cli
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

Per informazioni su come creare avvisi delle metriche, vedere [crea, visualizzare e gestire gli avvisi delle metriche con monitoraggio di Azure](../../azure-monitor/platform/alerts-metric.md).

## <a name="media-services-diagnostic-logs"></a>Log di diagnostica di servizi multimediali

Attualmente, è possibile ottenere i log di diagnostica seguenti:

|NOME|Descrizione|
|---|---|
|Richiesta di servizio di distribuzione delle chiavi|Log che mostrano le informazioni sulla richiesta di servizio di distribuzione delle chiavi. Per altre informazioni, vedere [schemi](media-services-diagnostic-logs-schema.md).|

Per abilitare la memorizzazione dei log di diagnostica in un Account di archiviazione, è necessario eseguire il comando seguente `az monitor diagnostic-settings` riga di comando: 

```cli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <name or ID of storage account> \
    --resource <target resource object ID> \
    --resource-group <storage account resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

Ad esempio:

```cli
az monitor diagnostic-settings create --name amsv3diagnostic \
    --storage-account storageaccountforamsv3  \
    --resource "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/amsv3ResourceGroup/providers/Microsoft.Media/mediaservices/amsv3account" \
    --resource-group "amsv3ResourceGroup" \
    --logs '[{"category": "KeyDeliveryRequests",  "enabled": true, "retentionPolicy": {"days": 3, "enabled": true }}]'
```

## <a name="next-steps"></a>Passaggi successivi 

[Come raccogliere e utilizzare dati di log dalle risorse di Azure](../../azure-monitor/platform/diagnostic-logs-overview.md).
