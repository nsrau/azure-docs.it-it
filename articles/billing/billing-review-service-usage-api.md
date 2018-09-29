---
title: Esaminare l'utilizzo delle risorse di Azure con l'API REST | Microsoft Docs
description: Informazioni su come usare le API REST di Azure per esaminare l'uso delle risorse di servizio di Azure.
services: billing
documentationcenter: na
author: lleonard-msft
manager: MBaldwin
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2018
ms.author: erikre
ms.openlocfilehash: bddb72f2b76d6e652dd26f2e383b7d06fded881b
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2018
ms.locfileid: "47395458"
---
# <a name="review-azure-resource-usage-using-the-rest-api"></a>Esaminare l'utilizzo di risorse di Azure usando l'API REST

Le API di Gestione costi di Azure aiutano a esaminare e gestire il consumo delle risorse di Azure.

Questo articolo descrive come creare un report giornaliero in grado di generare un documento di valori delimitati da virgole, con le informazioni relative all'utilizzo orario e come usare successivamente i filtri per personalizzare il report, in modo che sia possibile eseguire una query sull'utilizzo di macchine virtuali, database e risorse contrassegnate in un gruppo di risorse di Azure.

>[!NOTE]
> L'API di Gestione costi è attualmente in modalità Private Preview.

## <a name="create-a-basic-cost-management-report"></a>Creare un report di gestione dei costi di base

Usare l'operazione `reports` nell'API Gestione costi per definire la modalità di generazione dei report di costi e il percorso in cui verrà pubblicato il report.

```http
https://management.azure.com/subscriptions/{subscriptionGuid}/providers/Microsoft.CostManagement/reports/{reportName}?api-version=2018-09-01-preview
Content-Type: application/json   
Authorization: Bearer
```

Il parametro `{subscriptionGuid}` è obbligatorio e deve contenere un ID di sottoscrizione che può essere letto utilizzando le credenziali fornite nel token API. Il `{reportName}`

Gli argomenti seguenti sono obbligatori: 

|Intestazione della richiesta|DESCRIZIONE|  
|--------------------|-----------------|  
|*Content-Type:*| Richiesto. Impostare su `application/json`. |  
|*Authorization:*| Richiesto. Impostare un valore valido per il token API `Bearer`. |

Configurare i parametri del report nel corpo della richiesta HTTP. Nell'esempio seguente, il report è impostato per generarsi ogni giorno quando attivo; si tratta di un file CSV scritto in un contenitore blob di Archiviazione di Microsoft Azure e contiene informazioni sui costi su base oraria per tutte le risorse nel gruppo di risorse `westus`.

```json
{
    "properties": {
        "schedule": {
            "status": "Inactive",
            "recurrence": "Daily",
            "recurrencePeriod": {
                "from": "2018-08-21",
                "to": "2019-10-31"
            }
        },
        "deliveryInfo": {
            "destination": {
                "resourceId": "/subscriptions/{subscriptionGuid}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}",
                "container": "MyReportContainer",
                "rootFolderPath": "MyScheduleTest"
            }
        },
        "format": "Csv",
        "definition": {
            "type": "Usage",
            "timeframe": "MonthToDate",
            "dataSet": {
                "granularity": "Hourly",
                "filter": {
                    "dimensions": {
                        "name": "ResourceLocation",
                        "operator": "In",
                        "values": [
                            "westus"
                        ]
                    }
                }
            }
        }
    }
}
```

Il valore di 

## <a name="filtering-reports"></a>Filtraggio di report

Le sezioni `filter` e `dimensions` del corpo della richiesta durante la creazione di un report consentono di concentrarsi sui costi per tipo di risorsa specifico. Il corpo della richiesta precedente mostra come filtrare le risorse in base all’area. 

### <a name="get-all-compute-usage"></a>Ottenere tutti gli utilizzi di calcolo

Usare la dimensione `ResourceType` per creare report dei costi delle macchine virtuali di Azure nella sottoscrizione di tutte le aree.

```json
"filter": {
    "dimensions": {
        "name": "ResourceType",
        "operator": "In",
        "values": [
                "Microsoft.ClassicCompute/virtualMachines", 
                "Microsoft.Compute/virtualMachines"
        ] 
    }
}
```

### <a name="get-all-database-usage"></a>Ottenere l'uso di tutti i database

Usare la dimensione `ResourceType` per creare report dei costi del database SQL di Azure nella sottoscrizione di tutte le aree.

```json
"filter": {
    "dimensions": {
        "name": "ResourceType",
        "operator": "In",
        "values": [
                "Microsoft.Sql/servers"
        ] 
    }
}
```

### <a name="report-on-specific-instances"></a>Creare report su istanze specifiche

La dimensione `Resource` consente di creare report sui costi per risorse specifiche.

```json
"filter": {
    "dimensions": {
        "name": "Resource",
        "operator": "In",
        "values": [
            "subscriptions/{subscriptionGuid}/resourceGroups/{resourceGroup}/providers/Microsoft.ClassicCompute/virtualMachines/{ResourceName}"
        ]
    }
}
```

### <a name="changing-timeframes"></a>Modifica intervalli di tempo

Impostare la definizione `timeframe` a `Custom` per impostare un intervallo di tempo di fuori delle opzioni predefinite di settimana e mese.

```json
"timeframe": "Custom",
"timePeriod": {
    "from": "2017-12-31T00:00:00.000Z",
    "to": "2018-12-30T00:00:00.000Z"
}
```

## <a name="next-steps"></a>Passaggi successivi
- [Introduzione all'API REST di Azure](https://docs.microsoft.com/rest/api/azure/)   
