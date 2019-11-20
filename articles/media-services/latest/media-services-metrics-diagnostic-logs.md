---
title: Monitorare le metriche e i log di diagnostica tramite monitoraggio di Azure
titleSuffix: Azure Media Services
description: Informazioni su come monitorare le metriche e i log di diagnostica di servizi multimediali di Azure tramite monitoraggio di Azure.
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
ms.openlocfilehash: 38fcda40a208da3f3f5aef5b13778ca38092d8f8
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186107"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs-via-azure-monitor"></a>Monitorare le metriche di servizi multimediali e i log di diagnostica tramite monitoraggio di Azure

[Monitoraggio di Azure](../../azure-monitor/overview.md) consente di monitorare le metriche e i log di diagnostica che consentono di comprendere le prestazioni delle app. Tutti i dati raccolti da monitoraggio di Azure si integrano in uno dei due tipi fondamentali: metriche e log. È possibile monitorare i log di diagnostica di servizi multimediali e creare avvisi e notifiche per le metriche e i log raccolti. È possibile visualizzare e analizzare i dati di metrica usando [Esplora metriche](../../azure-monitor/platform/metrics-getting-started.md). È possibile inviare log ad [archiviazione di Azure](https://azure.microsoft.com/services/storage/), trasmetterli a [Hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/), esportarli in [log Analytics](https://azure.microsoft.com/services/log-analytics/)o usare servizi di terze parti.

Per una panoramica dettagliata, vedere [metriche di monitoraggio di Azure](../../azure-monitor/platform/data-platform.md) e [log di diagnostica di monitoraggio di Azure](../../azure-monitor/platform/resource-logs-overview.md).

In questo argomento vengono illustrate le [metriche](#media-services-metrics) e i [log di diagnostica](#media-services-diagnostic-logs)di servizi multimediali supportati.

## <a name="media-services-metrics"></a>Metriche di servizi multimediali

Le metriche vengono raccolte a intervalli regolari indipendentemente dal fatto che il valore venga modificato. Sono utili per la generazione di avvisi perché possono essere campionate di frequente e perché un avviso può essere generato rapidamente con una logica relativamente semplice. Per informazioni su come creare avvisi delle metriche, vedere [creare, visualizzare e gestire gli avvisi delle metriche con monitoraggio di Azure](../../azure-monitor/platform/alerts-metric.md).

Servizi multimediali supporta le metriche di monitoraggio per le risorse seguenti:

* Account
* Endpoint di streaming

### <a name="account"></a>Account

È possibile monitorare le metriche dell'account seguenti.

|Nome metrica|Nome visualizzato|DESCRIZIONE|
|---|---|---|
|AssetCount|Conteggio asset|Asset nell'account.|
|AssetQuota|Quota asset|Quota di asset nell'account.|
|AssetQuotaUsedPercentage|Percentuale di utilizzo della quota di asset|Percentuale della quota di asset già in uso.|
|ContentKeyPolicyCount|Conteggio dei criteri della chiave simmetrica|Criteri chiave simmetrica nell'account.|
|ContentKeyPolicyQuota|Quota dei criteri della chiave simmetrica|Quota dei criteri chiave simmetrica nell'account.|
|ContentKeyPolicyQuotaUsedPercentage|Percentuale di utilizzo della quota dei criteri della chiave simmetrica|Percentuale della quota dei criteri della chiave simmetrica già utilizzata.|
|StreamingPolicyCount|Conteggio dei criteri di flusso|Criteri di streaming nell'account.|
|StreamingPolicyQuota|Quota dei criteri di streaming|Quota dei criteri di streaming nell'account.|
|StreamingPolicyQuotaUsedPercentage|Percentuale di utilizzo della quota dei criteri di flusso|Percentuale della quota dei criteri di flusso già utilizzata.|

È inoltre consigliabile esaminare [le quote e le limitazioni dell'account](limits-quotas-constraints.md).

### <a name="streaming-endpoint"></a>Endpoint di streaming

Sono supportate le metriche seguenti per gli [endpoint di streaming](https://docs.microsoft.com/rest/api/media/streamingendpoints) di servizi multimediali:

|Nome metrica|Nome visualizzato|DESCRIZIONE|
|---|---|---|
|Requests|Requests|Specifica il numero totale di richieste HTTP gestite dall'endpoint di streaming.|
|Dati in uscita|Dati in uscita|Numero totale di byte in uscita. Ad esempio, byte trasmessi dall'endpoint di streaming.|
|SuccessE2ELatency|Latenza end-to-end riuscita|Durata del periodo di tempo durante il quale l'endpoint di streaming ha ricevuto la richiesta al momento dell'invio dell'ultimo byte della risposta.|

### <a name="why-would-i-want-to-use-metrics"></a>Perché si vuole usare la metrica?

Di seguito sono riportati alcuni esempi di come le metriche di monitoraggio di servizi multimediali possano aiutarti a comprendere le prestazioni delle tue app. Di seguito sono riportate alcune domande che possono essere risolte con le metriche di servizi multimediali:

* Ricerca per categorie monitorare l'endpoint di streaming standard per capire quando sono stati superati i limiti?
* Ricerca per categorie sa se si dispone di un numero sufficiente di unità di scala di endpoint di streaming Premium?
* Come è possibile impostare un avviso per stabilire quando aumentare la scalabilità verticale degli endpoint di streaming?
* Ricerca per categorie impostare un avviso per stabilire quando è stato raggiunto il numero massimo di uscite configurato per l'account?
* Come è possibile visualizzare la suddivisione delle richieste in errore e la causa dell'errore?
* Come è possibile vedere il numero di richieste HLS o DASH da estrarre dal pacchetto?
* Ricerca per categorie impostare un avviso per stabilire quando è stato raggiunto il valore soglia di numero di richieste non riuscite?

### <a name="example"></a>Esempio

Vedere [come monitorare le metriche di servizi multimediali](media-services-metrics-howto.md).

## <a name="media-services-diagnostic-logs"></a>Log di diagnostica di servizi multimediali

I log di diagnostica forniscono dati avanzati e frequenti sul funzionamento di una risorsa di Azure. Per ulteriori informazioni, vedere [come raccogliere e utilizzare i dati di log dalle risorse di Azure](../../azure-monitor/platform/resource-logs-overview.md).

Servizi multimediali supporta i log di diagnostica seguenti:

* Distribuzione delle chiavi

### <a name="key-delivery"></a>Distribuzione delle chiavi

|Nome|DESCRIZIONE|
|---|---|
|Richiesta del servizio di distribuzione delle chiavi|Log che mostrano le informazioni della richiesta del servizio di distribuzione delle chiavi. Per ulteriori informazioni, vedere [schemi](media-services-diagnostic-logs-schema.md).|

### <a name="why-would-i-want-to-use-diagnostics-logs"></a>Perché si vogliono usare I log di diagnostica?

Di seguito sono riportate alcune informazioni che è possibile esaminare con i log di diagnostica per il recapito

* Vedere il numero di licenze recapitate dal tipo di DRM.
* Vedere il numero di licenze recapitate dai criteri.
* Vedere errori per DRM o tipo di criteri.
* Vedere il numero di richieste di licenza non autorizzate dai client.

### <a name="example"></a>Esempio

Vedere [come monitorare i log di diagnostica del servizio multimediale](media-services-diagnostic-logs-howto.md).

## <a name="next-steps"></a>Passaggi successivi

* [Come raccogliere e utilizzare i dati di log dalle risorse di Azure](../../azure-monitor/platform/resource-logs-overview.md)
* [Creare, visualizzare e gestire gli avvisi delle metriche con Monitoraggio di Azure](../../azure-monitor/platform/alerts-metric.md)
* [Come monitorare le metriche di servizi multimediali](media-services-metrics-howto.md)
* [Come monitorare i log di diagnostica del servizio multimediale](media-services-diagnostic-logs-howto.md)
