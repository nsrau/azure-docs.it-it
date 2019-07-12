---
title: Monitorare le metriche di servizi multimediali di Azure e log di diagnostica tramite Monitoraggio di Azure | Microsoft Docs
description: Questo articolo offre una panoramica su come monitorare le metriche di servizi multimediali di Azure e log di diagnostica tramite Monitoraggio di Azure.
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
ms.openlocfilehash: 6d26cd809d78bf05f66c9fa03be5063ca4d2d5e4
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67806008"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs"></a>Monitorare le metriche di servizi multimediali e i log di diagnostica

[Monitoraggio di Azure](../../azure-monitor/overview.md) consente di monitorare le metriche e log di diagnostica che consentono di comprendere prestazioni delle applicazioni. Tutti i dati raccolti da monitoraggio di Azure rientra in uno dei due tipi fondamentali, metriche e log. È possibile monitorare i log di diagnostica di servizi multimediali e creare avvisi e notifiche per i log e delle metriche raccolte. È possibile visualizzare e analizzare i dati delle metriche usando [Esplora metriche](../../azure-monitor/platform/metrics-getting-started.md). È possibile inviare log ad [archiviazione di Azure](https://azure.microsoft.com/services/storage/), trasmetterli agli [hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/)e di esportarli per [Log Analitica](https://azure.microsoft.com/services/log-analytics/), o usare servizi di terze parti 3rd.

Per una panoramica dettagliata, vedere [metriche di monitoraggio di Azure](../../azure-monitor/platform/data-platform.md) e [log di diagnostica di monitoraggio di Azure](../../azure-monitor/platform/diagnostic-logs-overview.md).

In questo argomento viene supportata [metriche di servizi multimediali](#media-services-metrics) e [log di diagnostica di servizi multimediali](#media-services-diagnostic-logs).

## <a name="media-services-metrics"></a>Metriche di servizi multimediali

Le metriche vengono raccolte a intervalli regolari indipendentemente dal fatto che il valore venga modificato. Sono utili per la generazione di avvisi perché possono essere campionate di frequente e perché un avviso può essere generato rapidamente con una logica relativamente semplice. Per informazioni su come creare avvisi delle metriche, vedere [crea, visualizzare e gestire gli avvisi delle metriche con monitoraggio di Azure](../../azure-monitor/platform/alerts-metric.md).

Servizi multimediali supporta le metriche di monitoraggio per le risorse seguenti:

* Account
* Endpoint di streaming
 
### <a name="account"></a>Account

È possibile monitorare le metriche seguenti di account. 

|Nome metrica|`Display name`|DESCRIZIONE|
|---|---|---|
|AssetCount|Numero di asset|Asset nell'account.|
|AssetQuota|Quota di asset|Quota di asset nell'account.|
|AssetQuotaUsedPercentage|Percentuale della quota usata Asset|La percentuale della quota Asset già in uso.|
|ContentKeyPolicyCount|Conteggio dei criteri di chiave del contenuto|Criteri di chiave contenuti nell'account.|
|ContentKeyPolicyQuota|Quota di criteri di chiave del contenuto|Quota di criteri chiave contenuto nell'account.|
|ContentKeyPolicyQuotaUsedPercentage|Quota di criteri di chiave del contenuto usati percentuale|Percentuale dell'obiettivo di criteri di chiave simmetrica già in uso.|
|StreamingPolicyCount|Numero di criteri di streaming|Criteri di streaming nell'account.|
|StreamingPolicyQuota|Lo streaming di quota dei criteri|Quota di criteri streaming nell'account.|
|StreamingPolicyQuotaUsedPercentage|Percentuale di utilizzato streaming quota dei criteri|La percentuale della quota dei criteri di Streaming già in uso.|
 
È consigliabile anche leggere [le quote e limitazioni dell'account](limits-quotas-constraints.md).

### <a name="streaming-endpoint"></a>Endpoint di streaming

I seguenti servizi multimediali [gli endpoint di Streaming](https://docs.microsoft.com/rest/api/media/streamingendpoints) sono supportate le metriche:

|Nome metrica|`Display name`|Descrizione|
|---|---|---|
|Requests|Requests|Fornisce il numero totale di richieste HTTP servite dall'Endpoint di Streaming.|
|Egress|Egress|Numero totale di byte in uscita. Ad esempio, i byte trasmessi dall'Endpoint di Streaming.|
|SuccessE2ELatency|Latenza end to end esito positivo|Durata di tempo da quando l'Endpoint di Streaming ha ricevuto la richiesta quando è stato inviato l'ultimo byte della risposta.|

### <a name="why-would-i-want-to-use-metrics"></a>Motivo per cui vuole usare le metriche? 

Ecco alcuni esempi del modo in cui il monitoraggio delle metriche di servizi multimediali aiuta a comprendere prestazioni delle applicazioni. Alcune delle domande che possono essere risolti con le metriche di servizi multimediali sono:

* Come si monitora l'Endpoint di Streaming Standard per sapere quando è stato superato i limiti?
* Come si capisce se si dispone di sufficienti unità di scala di Endpoint di Streaming Premium? 
* Come è possibile impostare un avviso di sapere quando aumenta l'endpoint di Streaming?
* Come impostare un avviso per sapere quando è stato raggiunto il numero massimo in uscita configurato per l'account?
* Come è possibile visualizzare la suddivisione di richieste non riuscite e la causa dell'errore?
* Come è possibile vedere quante richieste DASH o HLS vengono rimosse dal packager?
* Come impostare un avviso per sapere quando è stato raggiunto il valore di soglia del numero di richieste non riuscite? 

### <a name="example"></a>Esempio

Vedere [come monitorare le metriche di servizi multimediali](media-services-metrics-howto.md)

## <a name="media-services-diagnostic-logs"></a>Log di diagnostica di servizi multimediali

I log di diagnostica forniscono dati completi e frequenti sul funzionamento di una risorsa di Azure. Per altre informazioni, vedere [come raccogliere e utilizzare dati di log dalle risorse di Azure](../../azure-monitor/platform/diagnostic-logs-overview.md).

Servizi multimediali supporta i log di diagnostica seguenti:

* Distribuzione delle chiavi

### <a name="key-delivery"></a>Distribuzione delle chiavi

|Name|Descrizione|
|---|---|
|Richiesta di servizio di distribuzione delle chiavi|Log che mostrano le informazioni sulla richiesta di servizio di distribuzione delle chiavi. Per altre informazioni, vedere [schemi](media-services-diagnostic-logs-schema.md).|

### <a name="why-would-i-want-to-use-diagnostics-logs"></a>Motivo per cui vuole usare i log di diagnostica? 

Alcuni aspetti che è possibile esaminare i log di diagnostica di distribuzione delle chiavi sono:

* Visualizzare il numero di licenze recapitati dal tipo DRM
* Visualizzare il numero di licenze recapitati da criteri 
* Vedere gli errori dal tipo di criteri o DRM
* Visualizzare il numero di richieste di licenze non autorizzato dal client

### <a name="example"></a>Esempio

Vedere [come monitorare i log di diagnostica di servizi multimediali](media-services-diagnostic-logs-howto.md)

## <a name="next-steps"></a>Passaggi successivi 

* [Come raccogliere e utilizzare dati di log dalle risorse di Azure](../../azure-monitor/platform/diagnostic-logs-overview.md)
* [Creare, visualizzare e gestire gli avvisi delle metriche con monitoraggio di Azure](../../azure-monitor/platform/alerts-metric.md)
* [Come monitorare le metriche di servizi multimediali](media-services-metrics-howto.md)
* [Come monitorare i log di diagnostica di servizi multimediali](media-services-diagnostic-logs-howto.md)
