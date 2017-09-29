---
title: API per clienti Enterprise per la fatturazione di Azure - Periodi di fatturazione | Microsoft Docs
description: Informazioni sulle API di creazione di report che consentono ai clienti Enterprise di Azure di estrarre i dati sull'uso a livello di codice.
services: 
documentationcenter: 
author: cwatson-cat
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
ms.translationtype: HT
ms.sourcegitcommit: 6e76ac40e9da2754de1d1aa50af3cd4e04c067fe
ms.openlocfilehash: c6880b79189e0683387a7aafbd6fa4805b3b42ef
ms.contentlocale: it-it
ms.lasthandoff: 07/31/2017

---
# <a name="reporting-apis-for-enterprise-customers---billing-periods"></a>API di creazione report per clienti Enterprise - Periodi di fatturazione

L'API per periodi di fatturazione restituisce un elenco di periodi di fatturazione contenente i dati sull'uso per la registrazione specificata in ordine cronologico inverso. Ogni periodo contiene una proprietà che punta alla route API per i quattro set di dati, ovvero BalanceSummary, UsageDetails, MarketplaceCharges e PriceSheet. Se per il periodo non sono presenti dati, la proprietà corrispondente è null. 


##<a name="request"></a>Richiesta 
Le proprietà di intestazione comuni che devono essere aggiunte vengono specificate [qui](billing-enterprise-api.md). 

|Metodo | URI della richiesta|
|-|-|
|GET| https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingperiods|

> [!Note]
> Per usare la versione di anteprima dell'API, sostituire v2 con v1 nell'URL precedente.
>

## <a name="response"></a>Response
 
    
    
      [
            {
                "billingPeriodId": "201704",
                "billingStart": "2017-04-01T00:00:00Z",
                "billingEnd": "2017-04-30T11:59:59Z",
                "balanceSummary": "/v1/enrollments/100/billingperiods/201704/balancesummary",
                "usageDetails": "/v1/enrollments/100/billingperiods/201704/usagedetails",
                "marketplaceCharges": "/v1/enrollments/100/billingperiods/201704/marketplacecharges",
                "priceSheet": "/v1/enrollments/100/billingperiods/201704/pricesheet"
            },          
            ....
      ]
    

**Definizioni delle proprietà di risposta**

|Nome proprietà| Tipo| Descrizione
|-|-|-|
|billingPeriodId| string| ID univoco che rappresenta un determinato periodo di fatturazione|
|billingStart| datetime| Stringa ISO 8601 che rappresenta la data di inizio del periodo|
|billingEnd| datetime| Stringa ISO 8601 che rappresenta la data di fine del periodo|
|balanceSummary| string| Percorso dell'URL che punta ai dati di riepilogo saldi per il periodo|
|usageDetails| string| Percorso dell'URL che punta ai dati dei dettagli sull'uso per il periodo|
|marketplaceCharges| string| Percorso URL che punta ai dati delle spese per il Marketplace per il periodo|
|priceSheet| string| Percorso dell'URL che punta ai dati dell'elenco prezzi per il periodo|

<br/>
## <a name="see-also"></a>Vedere anche

* [API per saldi e riepilogo](billing-enterprise-api-balance-summary.md)

* [API per dettagli sull'uso](billing-enterprise-api-usage-detail.md) 

* [API per spese per il Marketplace Store](billing-enterprise-api-marketplace-storecharge.md) 

* [API per l'elenco prezzi](billing-enterprise-api-pricesheet.md)
