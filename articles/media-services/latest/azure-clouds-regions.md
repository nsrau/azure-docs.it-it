---
title: Cloud e aree in cui i Servizi multimediali v3 di Azure sono disponibili| Microsoft Docs
description: Questo articolo illustra i cloud e le aree di Azure in cui i Servizi multimediali v3 di Azure sono attualmente disponibili.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/07/2019
ms.author: juliako
ms.openlocfilehash: 7b2691f543cf38a56eefb1e8521169aeccbf3221
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65409292"
---
# <a name="clouds-and-regions-in-which-azure-media-services-v3-exists"></a>Cloud e aree in cui sono presenti i Aervizi multimediali v3 di Azure

I Servizi multimediali v3 di Azure sono disponibili tramite il manifesto di Azure Resource Manager in Azure globale, Azure per enti pubblici, Azure Germania, Azure Cina 21Vianet. Tuttavia, non tutte le funzionalità di servizi multimediali sono disponibili in tutti i cloud di Azure. Questo documento descrive le disponibilità dei componenti principali dei Servizi multimediali v3.

## <a name="feature-availability-in-azure-clouds"></a>Funzionalità disponibili nei cloud di Azure

| Funzionalità|Aree a livello globale di Azure | Azure Government|Azure Germania|21Vianet per Azure Cina|
| --- | --- | --- | --- | --- |
| [Azure EventGrid](reacting-to-media-services-events.md) | Disponibile | Non disponibile | Non disponibile | Non disponibile |
| [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) |  Disponibile | Non disponibile | Non disponibile | Non disponibile |
| [AudioAnalyzerPreset](analyzing-video-audio-files-concept.md) |  Disponibile | Non disponibile | Non disponibile | Non disponibile |
| [StandardEncoderPreset](encoding-concept.md) | Disponibile | Disponibile | Disponibile | Disponibile |
| [LiveEvents](live-streaming-overview.md) | Disponibile | Disponibile | Disponibile | Disponibile |
| [StreamingEndpoints](streaming-endpoint-concept.md) | Disponibile | Disponibile | Disponibile | Disponibile |

## <a name="regionsgeographieslocations"></a>Aree/aree geografiche/località

[Aree in cui viene distribuito il servizio di servizi multimediali di Azure](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)

### <a name="region-code-name"></a>Nome del codice dell'area 

Quando è necessario fornire il parametro dell'**ubicazione**, è necessario fornire il codice dell'area come il valore della **posizione**. Per ottenere il nome del codice dell'area in cui si trova l'account e a cui deve essere indirizzata la chiamata, è possibile eseguire la riga seguente nell'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)

```bash
az account list-locations
```

Dopo avere eseguito la riga precedente, viene visualizzato un elenco di tutte le aree di Azure. Passare all'area di Azure con il *displayName* desiderato e usare il valore *name* per il parametro **location**.

Ad esempio, per l'area di Azure Stati Uniti occidentali 2 (indicata di seguito), usare "westus2" per il parametro **location**.

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/00000000-23da-4fce-b59c-f6fb9513eeeb/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="endpoints"></a>Endpoint  

Gli endpoint seguenti sono importanti per la connessione agli account di Servizi multimediali da diversi cloud nazionali di Azure.

### <a name="global-azure"></a>Azure globale

|Endpoint ||
| --- | --- | 
| Azure Resource Manager |  `https://management.azure.com/` |
| Authentication | `https://login.microsoftonline.com/` | 
| Destinatari dei token | `https://management.core.windows.net/` |

### <a name="azure-government"></a>Azure Government

|Endpoint||
| --- | --- | 
| Azure Resource Manager |  `https://management.usgovcloudapi.net/` |
| Authentication | `https://login.microsoftonline.us/` | 
| Destinatari dei token | `https://management.core.usgovcloudapi.net/` |

### <a name="azure-germany"></a>Azure Germania

| Endpoint ||
| --- | --- |  
| Azure Resource Manager | `https://management.cloudapi.de/` |
| Authentication | `https://login.microsoftonline.de/` |
| Destinatari dei token | `https://management.core.cloudapi.de/`|

### <a name="azure-china-21vianet"></a>21Vianet per Azure Cina

|Endpoint||
| --- | --- | 
| Azure Resource Manager | `https://management.chinacloudapi.cn/` |
| Authentication | `https://login.chinacloudapi.cn/` |
| Destinatari dei token |  `https://management.core.chinacloudapi.cn/` |

## <a name="see-also"></a>Vedere anche

* [Aree di Azure](https://azure.microsoft.com/global-infrastructure/regions/)
* [Aree geografiche di Azure](https://azure.microsoft.com/global-infrastructure/geographies/)
* [Località di Azure](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="next-steps"></a>Passaggi successivi

[Panoramica di Servizi multimediali v3](media-services-overview.md)
