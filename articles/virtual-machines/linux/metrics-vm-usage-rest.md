---
title: Ottenere i dati di utilizzo delle macchine virtuali di Azure usando l'API REST
description: Usare le API REST di Azure per raccogliere le metriche di utilizzo di una macchina virtuale.
services: virtual-machines
author: rloutlaw
ms.reviewer: routlaw
manager: gwallace
ms.service: load-balancer
ms.custom: REST
ms.topic: article
ms.date: 06/13/2018
ms.author: routlaw
ms.openlocfilehash: 523b81e53f2b0622b237993dbd88fb9492079c86
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035814"
---
# <a name="get-virtual-machine-usage-metrics-using-the-rest-api"></a>Ottenere le metriche di utilizzo di una macchina virtuale usando l'API REST

Questo esempio illustra come recuperare l'utilizzo della CPU per una [macchina virtuale Linux](https://docs.microsoft.com/azure/virtual-machines/linux/monitor) usando l'[API REST di Azure](/rest/api/azure/).

La documentazione di riferimento completa e altri esempi relativi all'API REST sono disponibili nelle [informazioni di riferimento REST di Monitoraggio di Azure](/rest/api/monitor). 

## <a name="build-the-request"></a>Compilare la richiesta

Usare la richiesta GET seguente per raccogliere la [metrica Percentuale CPU](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftcomputevirtualmachines) da una macchina virtuale

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmname}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=Percentage%20CPU&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
```

### <a name="request-headers"></a>Intestazioni della richiesta

Gli argomenti seguenti sono obbligatori: 

|Intestazione della richiesta|DESCRIZIONE|  
|--------------------|-----------------|  
|*Content-Type:*|Obbligatorio. Impostare su `application/json`.|  
|*Authorization:*|Obbligatorio. Impostare su un `Bearer`token di accesso[ ](/rest/api/azure/#authorization-code-grant-interactive-clients) valido. |  

### <a name="uri-parameters"></a>Parametri URI

| Nome | DESCRIZIONE |
| :--- | :---------- |
| subscriptionId | ID sottoscrizione che identifica una sottoscrizione di Azure. Se sono disponibili più sottoscrizioni, vedere [Uso di più sottoscrizioni](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). |
| resourceGroupName | Nome del gruppo di risorse di Azure associato alla risorsa. È possibile ottenere questo valore dall'API di Azure Resource Manager, dall'interfaccia della riga di comando o dal portale. |
| vmname | Nome della macchina virtuale di Azure. |
| metricnames | Elenco delimitato da virgole delle [metriche di Load Balancer](/azure/load-balancer/load-balancer-standard-diagnostics) valide. |
| api-version | Versione dell'API da usare per la richiesta.<br /><br /> Questo documento illustra la versione api-version `2018-01-01`, inclusa nell'URL precedente.  |
| timespan | Stringa con il formato `startDateTime_ISO/endDateTime_ISO` che definisce l'intervallo di tempo delle metriche restituite. Questo parametro facoltativo è impostato per restituire, nell'esempio, i dati relativi a un singolo giorno. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Corpo della richiesta

Per questa operazione non è necessario alcun corpo della richiesta.

## <a name="handle-the-response"></a>Gestire la risposta

Quando l'elenco dei valori delle metriche viene restituito correttamente, viene visualizzato il codice di stato 200. La [documentazione di riferimento](/rest/api/monitor/metrics/list#errorresponse) contiene un elenco completo dei codici di errore.

## <a name="example-response"></a>Risposta di esempio 

```json
{
    "cost": 0,
    "timespan": "2018-06-08T23:48:10Z/2018-06-09T00:48:10Z",
    "interval": "PT1M",
    "value": [
        {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmname}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=Percentage%20CPU",
            "type": "Microsoft.Insights/metrics",
            "name": {
                "value": "Percentage CPU",
                "localizedValue": "Percentage CPU"
            },
            "unit": "Percent",
            "timeseries": [
                {
                    "metadatavalues": [],
                    "data": [
                        {
                            "timeStamp": "2018-06-08T23:48:00Z",
                            "average": 0.44
                        },
                        {
                            "timeStamp": "2018-06-08T23:49:00Z",
                            "average": 0.31
                        },
                        {
                            "timeStamp": "2018-06-08T23:50:00Z",
                            "average": 0.29
                        },
                        {
                            "timeStamp": "2018-06-08T23:51:00Z",
                            "average": 0.29
                        },
                        {
                            "timeStamp": "2018-06-08T23:52:00Z",
                            "average": 0.285
                        } ]
                } ]
        } ]
}
```
