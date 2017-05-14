---
title: API per clienti Enterprise per la fatturazione di Azure - Dettagli sull&quot;uso | Microsoft Docs
description: Informazioni sull&quot;utilizzo dell&quot;API di fatturazione e dell&quot;API RestCard di Azure, utilizzate per offrire informazioni sul consumo di risorse e sulle tendenze di Azure.
services: 
documentationcenter: 
author: aedwin
manager: aedwin
editor: 
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 04/25/2017
ms.author: aedwin
ms.translationtype: Human Translation
ms.sourcegitcommit: f6006d5e83ad74f386ca23fe52879bfbc9394c0f
ms.openlocfilehash: d6fe7a020100e3372c1fa0e244838d4c40839dbf
ms.contentlocale: it-it
ms.lasthandoff: 05/03/2017


---
# <a name="reporting-apis-for-enterprise-customers---usage-details-preview"></a>API di creazione di report per i clienti Enterprise - Dettagli sull'uso (anteprima)

L'API per dettagli sull'uso offre un'analisi giornaliera dettagliata delle quantità usate e delle spese stimate in base a una registrazione. Il risultato include anche informazioni su istanze, contatori e reparti. Le query sull'API possono essere eseguite in base al periodo di fatturazione oppure in base a un intervallo definito da date di inizio e di fine specificate. 
## <a name="consumption-apis"></a>API per l'uso


##<a name="request"></a>Richiesta 
Le proprietà di intestazione comuni che devono essere aggiunte vengono specificate [qui](billing-enterprise-api.md). Se non viene specificato alcun periodo di fatturazione, vengono restituiti i dati per il periodo di fatturazione corrente. Gli intervalli di tempo personalizzato possono essere specificati con parametri di data di inizio e di fine nel formato aaaa-MM-gg. L'intervallo di tempo massimo supportato è 36 mesi.  

|Metodo | URI della richiesta|
|-|-|
|GET|https://consumption.azure.com/v1/enrollments/{enrollmentNumber}/usagedetails 
|GET|https://consumption.azure.com/v1/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/usagedetails|
|GET|https://consumption.azure.com/v1/enrollments/{enrollmentNumber}/usagedetailsbycustomdate?startTime=2017-01-01&endTime=2017-01-10|

## <a name="response"></a>Response

> A causa del volume di dati potenzialmente elevato, il set di risultati viene visualizzato in pagine. La proprietà nextLink, se presente, specifica il collegamento alla pagina di dati successiva. Se il collegamento è vuoto, si tratta dell'ultima pagina. 
<br/>

    {
        "id": "string",
        "data": [
            {                        
            "accountId": 0,
            "productId": 0,
            "resourceLocationId": 0,
            "consumedServiceId": 0,
            "departmentId": 0,
            "accountOwnerEmail": "string",
            "accountName": "string",
            "serviceAdministratorId": "string",
            "subscriptionId": 0,
            "subscriptionGuid": "string",
            "subscriptionName": "string",
            "date": "2017-04-27T23:01:43.799Z",
            "product": "string",
            "meterId": "string",
            "meterCategory": "string",
            "meterSubCategory": "string",
            "meterRegion": "string",
            "meterName": "string",
            "consumedQuantity": 0,
            "resourceRate": 0,
            "extendedCost": 0,
            "resourceLocation": "string",
            "consumedService": "string",
            "instanceId": "string",
            "serviceInfo1": "string",
            "serviceInfo2": "string",
            "additionalInfo": "string",
            "tags": "string",
            "storeServiceIdentifier": "string",
            "departmentName": "string",
            "costCenter": "string",
            "unitOfMeasure": "string",
            "resourceGroup": "string"
            }
        ]
        "nextLink": "string"
    }

<br/>

**Definizioni delle proprietà di risposta**

|Nome proprietà| Tipo| Descrizione
|-|-|-|
|id| string| ID univoco per la chiamata all'API. |
|data| Matrice JSON| Matrice dei dettagli sull'uso giornaliero per ogni istanza\contatore|
|nextLink| string| Quando sono presenti più pagine di dati, la proprietà nextLink punta all'URL per restituire la pagina di dati successiva |

## <a name="see-also"></a>Vedere anche
* [API per periodi di fatturazione](billing-enterprise-api-billing-periods.md)

* [API per saldi e riepilogo](billing-enterprise-api-balance-summary.md)

* [API per spese per il Marketplace Store](billing-enterprise-api-marketplace-storecharge.md) 

* [API per l'elenco prezzi](billing-enterprise-api-pricesheet.md)
