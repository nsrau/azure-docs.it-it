---
title: API per clienti Enterprise per la fatturazione di Azure - Elenco prezzi | Microsoft Docs
description: Informazioni sulle API di creazione di report che consentono ai clienti Enterprise di Azure di estrarre i dati sull&quot;uso a livello di codice.
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
ms.openlocfilehash: e5deb660ae5cfed5ac64000d070f4dd2e42ec9c0
ms.contentlocale: it-it
ms.lasthandoff: 05/03/2017


---
# <a name="reporting-apis-for-enterprise-customers---pricesheet-preview"></a>API di creazione di report per clienti Enterprise - Elenco prezzi (anteprima)

L'API elenco prezzi offre la tariffa applicabile per ogni contatore per la registrazione e il periodo di fatturazione specificati.

##<a name="request"></a>Richiesta
Le proprietà di intestazione comuni che devono essere aggiunte vengono specificate [qui](billing-enterprise-api.md). Se non viene specificato alcun periodo di fatturazione, vengono restituiti i dati per il periodo di fatturazione corrente.

|Metodo | URI della richiesta|
|-|-|
|GET|https://consumption.azure.com/v1/enrollments/{enrollmentNumber}/pricesheet|
|GET|https://consumption.azure.com/v1/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/pricesheet|


## <a name="response"></a>Response

    
          [
            {
                  "id": "enrollments/57354989/billingperiods/201601/products/343/pricesheets",
                  "billingPeriodId": "201704",
                  "meterName": "A1 VM",
                  "unitOfMeasure": "100 Hours",
                  "includedQuantity": 0,
                  "partNumber": "N7H-00015",
                  "unitPrice": 0.00,
                  "currencyCode": "USD"
            },
            {
                  "id": "enrollments/57354989/billingperiods/201601/products/2884/pricesheets",
                  "billingPeriodId": "201404",
                  "meterName": "Locally Redundant Storage Premium Storage - Snapshots - AU East",
                  "unitOfMeasure": "100 GB",
                  "includedQuantity": 0,
                  "partNumber": "N9H-00402",
                  "unitPrice": 0.00,
                  "currencyCode": "USD"
            },
            ...
        ]
    

**Definizioni delle proprietà di risposta**

|Nome proprietà| Tipo| Descrizione
|-|-|-|
|id| string| ID univoco che rappresenta un particolare elemento PriceSheet (contatore per periodo di fatturazione)|
|billingPeriodId| string| ID univoco che rappresenta un determinato periodo di fatturazione|
|meterName| string| Nome del contatore|
|unitOfMeasure| string| Unità di misura per misurare il servizio|
|includedQuantity| decimal| Quantità inclusa |
|partNumber| string| Numero di parte associato al contatore|
|unitPrice| decimal| Prezzo unitario per il contatore|
|currencyCode| string| Codice della valuta per l'elemento unitPrice|
<br/>
## <a name="see-also"></a>Vedere anche

* [API per periodi di fatturazione](billing-enterprise-api-billing-periods.md)

* [API per dettagli sull'uso](billing-enterprise-api-usage-detail.md)

* [API per saldi e riepilogo](billing-enterprise-api-balance-summary.md)

* [API per spese per il Marketplace Store](billing-enterprise-api-marketplace-storecharge.md)

