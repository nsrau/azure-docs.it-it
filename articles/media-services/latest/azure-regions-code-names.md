---
title: Cloud e aree in cui è disponibile servizi multimediali di Azure V3
description: Questo articolo illustra gli URL usati per gli endpoint e il codice per le aree.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: reference
ms.date: 10/28/2020
ms.author: inhenkel
ms.openlocfilehash: c17eeda91af46c4159f7c4b1747416298f24c703
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93319646"
---
# <a name="regional-code-names-and-endpoints"></a>Endpoint e nomi di codice regionali

### <a name="region-code-name"></a>Nome del codice dell'area

Quando il parametro **location** viene usato in un comando o in una richiesta, è necessario specificare il nome del codice Region come valore **location** . Per ottenere il nome in codice dell'area in cui si trova l'account e che la chiamata deve essere indirizzata a, è possibile eseguire la riga seguente nell'interfaccia della riga di comando di Azure.

```azurecli-interactive
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

| Servizio | Endpoint |
| ------- | -------- |
| Azure Resource Manager |  `https://management.azure.com/` |
| Authentication | `https://login.microsoftonline.com/` |
| Destinatari dei token | `https://management.core.windows.net/` |

### <a name="azure-government"></a>Azure Government

| Servizio | Endpoint |
| ------- | -------- |
| Azure Resource Manager |  `https://management.usgovcloudapi.net/` |
| Authentication | `https://login.microsoftonline.us/` |
| Destinatari dei token | `https://management.core.usgovcloudapi.net/` |

[!INCLUDE [Widevine is not available in the GovCloud region.](./includes/widevine-not-available-govcloud.md)]

### <a name="azure-germany"></a>Azure Germania

> [!NOTE]
> Gli endpoint di Azure Germania si applicano solo ai cloud sovrani in Germania.

| Servizio | Endpoint |
| ------- | -------- |
| Azure Resource Manager | `https://management.cloudapi.de/` |
| Authentication | `https://login.microsoftonline.de/` |
| Destinatari dei token | `https://management.core.cloudapi.de/`|

### <a name="azure-china-21vianet"></a>Azure Cina 21Vianet

| Servizio | Endpoint |
| ------- | -------- |
| Azure Resource Manager | `https://management.chinacloudapi.cn/` |
| Authentication | `https://login.chinacloudapi.cn/` |
| Destinatari dei token |  `https://management.core.chinacloudapi.cn/` |

## <a name="see-also"></a>Vedere anche

* [Aree di Azure](https://azure.microsoft.com/global-infrastructure/regions/)
* [Endpoint e nomi di codice regionali](azure-regions-code-names.md)
* [Aree geografiche di Azure](https://azure.microsoft.com/global-infrastructure/geographies/)
* [Località di Azure](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="next-steps"></a>Passaggi successivi

[Panoramica di Servizi multimediali v3](media-services-overview.md)
