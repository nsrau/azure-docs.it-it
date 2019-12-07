---
title: Schemi dei log di diagnostica di servizi multimediali di Azure-Azure
description: Questo articolo illustra gli schemi dei log di diagnostica di servizi multimediali di Azure.
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
ms.date: 01/20/2019
ms.author: juliako
ms.openlocfilehash: 2f5fcf4d9106bf37bcc81388e48afe689f4ef4d6
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896050"
---
# <a name="diagnostic-logs-schemas"></a>Schemi dei log di diagnostica

[Monitoraggio di Azure](../../azure-monitor/overview.md) consente di monitorare le metriche e i log di diagnostica che consentono di comprendere le prestazioni delle applicazioni. È possibile monitorare i log di diagnostica di servizi multimediali e creare avvisi e notifiche per le metriche e i log raccolti. È possibile inviare log ad [archiviazione di Azure](https://azure.microsoft.com/services/storage/), trasmetterli a [Hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/)ed esportarli in [log Analytics](https://azure.microsoft.com/services/log-analytics/)o usare servizi di terze parti.

Per informazioni dettagliate, vedere [metriche di monitoraggio di Azure](../../azure-monitor/platform/data-platform.md) e [log di diagnostica di monitoraggio di Azure](../../azure-monitor/platform/resource-logs-overview.md).

Questo articolo descrive gli schemi dei log di diagnostica di servizi multimediali.

## <a name="top-level-diagnostic-logs-schema"></a>Schema dei log di diagnostica di primo livello

Per una descrizione dettagliata dello schema dei log di diagnostica di primo livello, vedere [servizi, schemi e categorie supportati per i log di diagnostica di Azure](../../azure-monitor/platform/tutorial-dashboards.md).

## <a name="key-delivery-log-schema"></a>Schema del log di distribuzione delle chiavi

### <a name="properties"></a>properties

Queste proprietà sono specifiche dello schema del log di distribuzione delle chiavi.

|name|Description|
|---|---|
|keyId|ID della chiave richiesta.|
|keyType|Può essere uno dei valori seguenti: "Clear" (nessuna crittografia), "FairPlay", "PlayReady" o "Widevine".|
|policyName|Nome Azure Resource Manager del criterio.|
|tokenType|Tipo di token.|
|statusMessage|Messaggio di stato.|

### <a name="examples"></a>esempi

Proprietà dello schema delle richieste di recapito delle chiavi.

```json
{
    "time": "2019-01-11T17:59:10.4908614Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Succeeded",
    "resultSignature": "OK",
    "durationMs": 315,
    "identity": {
        "authorization": {
            "issuer": "http://testacs",
            "audience": "urn:test"
        },
        "claims": {
            "urn:microsoft:azure:mediaservices:contentkeyidentifier": "3321e646-78d0-4896-84ec-c7b98eddfca5",
            "iss": "http://testacs",
            "aud": "urn:test",
            "exp": "1547233138"
        }
    },
    "level": "Informational",
    "location": "uswestcentral",
    "properties": {
        "requestId": "b0243468-d8e5-4edf-a48b-d408e1661050",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "JWT",
        "statusMessage": "OK"
    }
} 
```

```json
 {
    "time": "2019-01-11T17:59:33.4676382Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Failed",
    "resultSignature": "Unauthorized",
    "durationMs": 2,
    "level": "Error",
    "location": "uswestcentral",
    "properties": {
        "requestId": "875af030-b77c-416b-b7e1-58f23ebec182",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "None",
        "statusMessage": "No token present in authorization header or URL."
    }
} 
```

## <a name="next-steps"></a>Passaggi successivi

[Monitorare le metriche di servizi multimediali e i log di diagnostica](media-services-metrics-diagnostic-logs.md)
