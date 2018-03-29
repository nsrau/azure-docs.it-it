---
title: Sottoscrizione di Griglia di eventi di Azure con modello
description: Creare una sottoscrizione di Griglia di eventi con un modello di Resource Manager.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/30/2018
ms.author: tomfitz
ms.openlocfilehash: ee0b2c228ae4ea53c0ee9794529aa190334ceed9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
# <a name="use-resource-manager-template-for-event-grid-subscription"></a>Usare un modello di Resource Manager per la sottoscrizione di Griglia di eventi

Questo articolo illustra come usare un modello di Azure Resource Manager per creare sottoscrizioni di Griglia di eventi. Il formato da usare varia in base al fatto che si voglia attivare un sottoscrizione per eventi di un gruppo di risorse o per eventi di un particolare tipo di risorsa. Entrambi i formati sono illustrati in questo articolo.

## <a name="subscribe-to-resource-group-events"></a>Sottoscrivere eventi di un gruppo di risorse

Per la sottoscrizione di eventi di un gruppo di risorse, usare `Microsoft.EventGrid/eventSubscriptions` per il tipo di risorsa. Per il tipo di punto di evento, usare `WebHook` o `EventHub`.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "resources": [
        {
            "type": "Microsoft.EventGrid/eventSubscriptions",
            "name": "mySubscription",
            "apiVersion": "2018-01-01",
            "properties": {
                "destination": {
                    "endpointType": "WebHook",
                    "properties": {
                        "endpointUrl": "https://requestb.in/ynboxiyn"
                    }
                },
                "filter": {
                    "subjectBeginsWith": "",
                    "subjectEndsWith": "",
                    "isSubjectCaseSensitive": false,
                    "includedEventTypes": ["All"]
                }
            }
        }
    ]
}
```

Quando si distribuisce il modello in un gruppo di risorse, sottoscrivere gli eventi per tale gruppo di risorse.

## <a name="subscribe-to-resource-events"></a>Sottoscrivere eventi di risorse

Per la sottoscrizione di eventi di risorse, è necessario associare la sottoscrizione alla risorsa corretta includendo il tipo e il nome della risorsa nella definizione della sottoscrizione. Per il tipo di risorsa, usare `<provider-namespace>/<resource-type>/providers/eventSubscriptions`. Per il nome, usare `<resource-name>/Microsoft.EventGrid/<subscription-name>`. Per il tipo di punto di evento, usare `WebHook` o `EventHub`.

Nell'esempio seguente viene illustrato come sottoscrivere gli eventi di Archiviazione Blob.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageName": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts/providers/eventSubscriptions",
            "name": "[concat(parameters('storageName'), '/Microsoft.EventGrid/myStorageSubscription')]",
            "apiVersion": "2018-01-01",
            "properties": {
                "destination": {
                    "endpointType": "WebHook",
                    "properties": {
                        "endpointUrl": "https://requestb.in/ynboxiyn"
                    }
                },
                "filter": {
                    "subjectBeginsWith": "",
                    "subjectEndsWith": "",
                    "isSubjectCaseSensitive": false
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi, vedere [Informazioni su Griglia di eventi](overview.md).
* Per un'introduzione a Resource Manager, vedere [Panoramica di Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
* Per iniziare a usare Griglia di eventi, vedere [Creare e instradare eventi personalizzati con Griglia di eventi di Azure](custom-event-quickstart.md).