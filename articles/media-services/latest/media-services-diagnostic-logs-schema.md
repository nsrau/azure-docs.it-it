---
title: Servizi multimediali di Azure diagnostica registra schemi - Azure
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
ms.openlocfilehash: 225bc4368d2a87fa5fef3f74624fd10f42dc7c97
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57556363"
---
# <a name="diagnostic-logs-schemas"></a>Schemi dei log di diagnostica

[Monitoraggio di Azure](../../azure-monitor/overview.md) consente di monitorare le metriche e log di diagnostica che consentono di comprendere prestazioni delle applicazioni. È possibile monitorare i log di diagnostica di servizi multimediali e creare avvisi e notifiche per i log e delle metriche raccolte. È possibile inviare log ad [archiviazione di Azure](https://azure.microsoft.com/services/storage/), trasmetterli agli [hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/)e di esportarli per [Log Analitica](https://azure.microsoft.com/services/log-analytics/), o usare servizi di terze parti 3rd.

Per informazioni dettagliate, vedere [metriche di monitoraggio di Azure](../../azure-monitor/platform/data-collection.md) e [log di diagnostica di monitoraggio di Azure](../../azure-monitor/platform/diagnostic-logs-overview.md).

Questo articolo descrive gli schemi dei log di diagnostica di servizi multimediali.

## <a name="top-level-diagnostic-logs-schema"></a>Schema dei log di diagnostica di primo livello

Per una descrizione dettagliata dello schema di primo livello dei log di diagnostica, vedere [supportati servizi, schemi e categorie per i log di diagnostica di Azure](../../azure-monitor/platform/tutorial-dashboards.md).

## <a name="key-delivery-log-schema"></a>Schema del log di distribuzione delle chiavi

### <a name="properties"></a>Properties

Queste proprietà sono specifiche per lo schema di log di distribuzione delle chiavi.

|NOME|DESCRIZIONE|
|---|---|
|keyId|ID della chiave richiesta.|
|keyType|Può avere uno dei valori seguenti: "Clear" (Nessuna crittografia), "FairPlay", "PlayReady" o "Widevine".|
|policyName|Nome dei criteri di Azure Resource Manager.|
|tokenType|Tipo di token.|
|statusMessage|Il messaggio di stato.|

### <a name="examples"></a>Esempi

Proprietà dello schema di richieste di distribuzione delle chiavi.

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
