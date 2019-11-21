---
title: Recuperare le metriche con l'API REST
titleSuffix: Azure Load Balancer
description: In this article, get started using the Azure REST APIs to collect health and usage metrics for Azure Load Balancer.
services: sql-database
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.custom: REST, seodec18
ms.topic: article
ms.date: 11/19/2019
ms.author: allensu
ms.openlocfilehash: 760ec8a945ab88b63dde2de75f5354818facf4f2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225248"
---
# <a name="get-load-balancer-usage-metrics-using-the-rest-api"></a>Get Load Balancer usage metrics using the REST API

Collect the number of bytes processed by a [Standard Load Balancer](/azure/load-balancer/load-balancer-standard-overview) for an interval of time using the [Azure REST API](/rest/api/azure/).

La documentazione di riferimento completa e altri esempi relativi all'API REST sono disponibili nelle [informazioni di riferimento REST di Monitoraggio di Azure](/rest/api/monitor). 

## <a name="build-the-request"></a>Compilare la richiesta

Usare la richiesta GET seguente per raccogliere la [metrica ByteCount](/azure/load-balancer/load-balancer-standard-diagnostics#multi-dimensional-metrics) da Load Balancer Standard. 

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=ByteCount&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
```

### <a name="request-headers"></a>Intestazioni della richiesta

Gli argomenti seguenti sono obbligatori: 

|Intestazione della richiesta|Description|  
|--------------------|-----------------|  
|*Content-Type:*|Richiesto. Impostare su `application/json`.|  
|*Authorization:*|Richiesto. Impostare su un [token di accesso](/rest/api/azure/#authorization-code-grant-interactive-clients) `Bearer` valido. |  

### <a name="uri-parameters"></a>Parametri URI

| name | Description |
| :--- | :---------- |
| subscriptionId | ID sottoscrizione che identifica una sottoscrizione di Azure. Se sono disponibili più sottoscrizioni, vedere [Uso di più sottoscrizioni](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). |
| resourceGroupName | Nome del gruppo di risorse contenente la risorsa. È possibile ottenere questo valore dall'API di Azure Resource Manager, dall'interfaccia della riga di comando o dal portale. |
| loadBalancerName | Nome di Azure Load Balancer. |
| metric names | Elenco delimitato da virgole delle [metriche di Load Balancer](/azure/load-balancer/load-balancer-standard-diagnostics) valide. |
| api-version | Versione dell'API da usare per la richiesta.<br /><br /> Questo documento illustra la versione api-version `2018-01-01`, inclusa nell'URL precedente.  |
| timespan | Intervallo di tempo della query. It's a string with the following format `startDateTime_ISO/endDateTime_ISO`. Questo parametro facoltativo è impostato per restituire, nell'esempio, i dati relativi a un singolo giorno. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Corpo della richiesta

Per questa operazione non è necessario alcun corpo della richiesta.

## <a name="handle-the-response"></a>Gestire la risposta

Quando l'elenco dei valori delle metriche viene restituito correttamente, viene visualizzato il codice di stato 200. La [documentazione di riferimento](/rest/api/monitor/metrics/list#errorresponse) contiene un elenco completo dei codici di errore.

## <a name="example-response"></a>Risposta di esempio 

```json
{
    "cost": 0,
    "timespan": "2018-06-05T03:00:00Z/2018-06-07T03:00:00Z",
    "interval": "PT1M",
    "value": [
        {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/Microsoft.Insights/metrics/ByteCount",
            "type": "Microsoft.Insights/metrics",
            "name": {
                "value": "ByteCount",
                "localizedValue": "Byte Count"
            },
            "unit": "Count",
            "timeseries": [
                {
                    "metadatavalues": [],
                    "data": [
                        {
                            "timeStamp": "2018-06-06T17:24:00Z",
                            "total": 1067921034.0
                        },
                        {
                            "timeStamp": "2018-06-06T17:25:00Z",
                            "total": 0.0
                        },
                        {
                            "timeStamp": "2018-06-06T17:26:00Z",
                            "total": 3781344.0
                        },
                    ]
                }
            ]
        }
    ],
    "namespace": "Microsoft.Network/loadBalancers",
    "resourceregion": "eastus"
}
```
