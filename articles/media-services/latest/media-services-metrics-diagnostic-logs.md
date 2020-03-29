---
title: Metriche di Servizi multimediali e log di diagnostica con Monitoraggio di AzureMedia Services metrics and diagnostic logs with Azure Monitor
titleSuffix: Azure Media Services
description: Informazioni su come monitorare le metriche di Servizi multimediali di Azure tramite Monitoraggio di Azure.Learn how to monitor Azure Media Services metrics and diagnostic logs via Azure Monitor.
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
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: f075362f976e6abb26c9781c4b0cdeb7912c0862
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514035"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs-via-azure-monitor"></a>Monitor Media Services metrics and diagnostic logs via Azure Monitor

[Monitoraggio di Azure](../../azure-monitor/overview.md) consente di monitorare le metriche e i log di diagnostica che consentono di comprendere le prestazioni delle app. Tutti i dati raccolti da Monitoraggio di Azure si adattano a uno dei due tipi fondamentali: metriche e log. È possibile monitorare i log di diagnostica di Servizi multimediali e creare avvisi e notifiche per le metriche e i log raccolti. È possibile visualizzare e analizzare i dati delle metriche utilizzando [Esplora metriche.](../../azure-monitor/platform/metrics-getting-started.md) È possibile inviare log ad [Archiviazione di Azure,](https://azure.microsoft.com/services/storage/)trasmetterli in streaming agli hub eventi di [Azure,](https://azure.microsoft.com/services/event-hubs/)esportarli in [Log Analytics](https://azure.microsoft.com/services/log-analytics/)o usare servizi di terze parti.

Per una panoramica dettagliata, vedere [Metriche di monitoraggio](../../azure-monitor/platform/data-platform.md) di Azure e Log di [diagnostica di Monitoraggio di Azure.For](../../azure-monitor/platform/platform-logs-overview.md)a detailed overview, see Azure Monitor Metrics and Azure Monitor Diagnostic logs .

In questo argomento vengono illustrati i log di diagnostica [di Media Services Metrics](#media-services-metrics) and Media [Services](#media-services-diagnostic-logs)supportate.

## <a name="media-services-metrics"></a>Metriche di Servizi multimediali

Le metriche vengono raccolte a intervalli regolari indipendentemente dal fatto che il valore venga modificato. Sono utili per la generazione di avvisi perché possono essere campionate di frequente e perché un avviso può essere generato rapidamente con una logica relativamente semplice. Per informazioni su come creare avvisi di metrica, vedere [Creare, visualizzare e gestire avvisi di metrica tramite Monitoraggio di Azure.](../../azure-monitor/platform/alerts-metric.md)

Servizi multimediali supporta il monitoraggio delle metriche per le risorse seguenti:Media Services supports monitoring metrics for the following resources:

* Account
* Endpoint di streaming

### <a name="account"></a>Account

È possibile monitorare le seguenti metriche dell'account.

|Nome metrica|Nome visualizzato|Descrizione|
|---|---|---|
|AssetCount (conteggio degli elementi)|Conteggio risorse|Risorse nel tuo account.|
|AssetQuota|Quota di attività|Quota di asset nel tuo account.|
|AssetQuotaUsedPercentual|Quota di attività utilizzata percentuale|Percentuale della quota di Asset già utilizzata.|
|ContentKeyPolicyCount|Conteggio criteri chiave simmetrica|Norme sulla chiave dei contenuti nel tuo account.|
|ContentKeyPolicyQuota|Quota dei criteri della chiave simmetrica|Quota Dei criteri chiave del contenuto nel tuo account.|
|ContentKeyPolicyQuotaUsedPercentage|Percentuale di quota utilizzata per la chiave di contenuto|Percentuale della quota dei criteri chiave simmetrica già utilizzata.|
|StreamingPolicyCount|Conteggio criteri di streaming|Criteri di streaming nel tuo account.|
|StreamingPolicyQuota|Quota di politica di streaming|Quota criteri di streaming nel tuo account.|
|StreamingPolicyQuotaUsedPercentage|Percentuale di quota utilizzata per i criteri di streaming|Percentuale della quota dei criteri di streaming già utilizzata.|

È inoltre necessario esaminare [le quote e le limitazioni dell'account](limits-quotas-constraints.md).

### <a name="streaming-endpoint"></a>Endpoint di streaming

Sono supportate le metriche seguenti per gli endpoint di streaming di Servizi multimediali:The following Media Services [Streaming Endpoints](https://docs.microsoft.com/rest/api/media/streamingendpoints) metrics are supported:

|Nome metrica|Nome visualizzato|Descrizione|
|---|---|---|
|Requests|Requests|Fornisce il numero totale di richieste HTTP servite dall'endpoint di streaming.|
|Egress|Egress|Numero totale di byte in uscita. Ad esempio, i byte trasmessi dall'endpoint di streaming.|
|SuccessE2ELatency|Latenza end-to-end riuscita|Durata del tempo che deve passare dal momento in cui l'endpoint di streaming ha ricevuto la richiesta fino all'ultimo byte della risposta inviata.|

### <a name="why-would-i-want-to-use-metrics"></a>Perché dovrei voler usare le metriche?

Ecco alcuni esempi di come il monitoraggio delle metriche di Servizi multimediali può aiutarti a comprendere le prestazioni delle tue app. Alcune domande che possono essere affrontate con le metriche di Servizi multimediali sono:Some questions that can be addressed with Media Services metrics are:

* Come faccio a monitorare il mio endpoint di streaming standard per sapere quando ho superato i limiti?
* Come faccio a sapere se ho un numero sufficiente di unità di scala degli endpoint Premium Streaming?
* Come è possibile impostare un avviso per sapere quando aumentare la scalabilità degli endpoint di streaming?
* Come si imposta un avviso per sapere quando è stata raggiunta l'uscita massima configurata per l'account?
* Come è possibile visualizzare l'errore di suddivisione delle richieste e che causa l'errore?
* Come posso vedere quante richieste HLS o DASH vengono estratte dal packager?
* Come si imposta un avviso per sapere quando è stato raggiunto il valore di soglia di - di richieste non riuscite?

### <a name="example"></a>Esempio

Vedere [Come monitorare](media-services-metrics-howto.md)le metriche di Servizi multimediali .

## <a name="media-services-diagnostic-logs"></a>Registri diagnostici di Servizi multimediali

I log di diagnostica forniscono dati dettagliati e frequenti sul funzionamento di una risorsa di Azure.Diagnostic logs provide rich and frequent data about the operation of an Azure resource. Per altre informazioni, vedere Come raccogliere e usare i dati di log dalle risorse di Azure.For more information, see [How to collect and consume log data from your Azure resources.](../../azure-monitor/platform/platform-logs-overview.md)

Servizi multimediali supporta i registri di diagnostica seguenti:Media Services supports the following diagnostic logs:

* Consegna delle chiavi

### <a name="key-delivery"></a>Consegna delle chiavi

|Nome|Descrizione|
|---|---|
|Richiesta del servizio di consegna delle chiavi|Registri che mostrano le informazioni sulla richiesta del servizio di distribuzione delle chiavi. Per ulteriori informazioni, vedere [schemi](media-services-diagnostic-logs-schema.md).|

### <a name="why-would-i-want-to-use-diagnostics-logs"></a>Perché dovrei usare i log di diagnostica?

Alcuni aspetti che è possibile esaminare con i log di diagnostica di recapito delle chiavi sono:Some things that you can examine with key delivery diagnostic logs are:

* Vedere il numero di licenze fornite dal tipo DRM.
* Vedere il numero di licenze fornite dai criteri.
* Visualizzare gli errori per DRM o tipo di criterio.
* Visualizzare il numero di richieste di licenza non autorizzate dai client.

### <a name="example"></a>Esempio

Vedere [Come monitorare i log di diagnostica del servizio multimediale](media-services-diagnostic-logs-howto.md).

## <a name="next-steps"></a>Passaggi successivi

* [Come raccogliere e usare i dati di log dalle risorse di AzureHow to collect and consume log data from your Azure resources](../../azure-monitor/platform/platform-logs-overview.md)
* [Creare, visualizzare e gestire gli avvisi delle metriche tramite Monitoraggio di AzureCreate, view, and manage metric alerts using Azure Monitor](../../azure-monitor/platform/alerts-metric.md)
* [Come monitorare le metriche di Servizi multimediali](media-services-metrics-howto.md)
* [Come monitorare i log di diagnostica del servizio multimedialeHow to monitor Media Service diagnostic logs](media-services-diagnostic-logs-howto.md)
