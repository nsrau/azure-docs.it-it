---
title: API per clienti Enterprise per la fatturazione di Azure - Spese per il Marketplace | Microsoft Docs
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
ms.openlocfilehash: 5539623f7ae35e14b6dafe6fdf9efe4bcaba4fd3
ms.contentlocale: it-it
ms.lasthandoff: 07/31/2017

---
# <a name="reporting-apis-for-enterprise-customers---marketplace-store-charge"></a>API di creazione report per clienti Enterprise - Spese per il Marketplace Store

L'API per spese per il Marketplace Store restituisce le spese giornaliere dettagliate in base all'uso correlate al Marketplace per il periodo di fatturazione specificato o per le date di inizio e fine indicate (le spese una tantum non sono incluse).

##<a name="request"></a>Richiesta 
Le proprietà di intestazione comuni che devono essere aggiunte vengono specificate [qui](billing-enterprise-api.md). Se non viene specificato alcun periodo di fatturazione, vengono restituiti i dati per il periodo di fatturazione corrente. Gli intervalli di tempo personalizzati possono essere specificati con i parametri di data di inizio di fine nel formato aaaa-MM-gg e l'intervallo di tempo massimo supportato è 36 mesi.  

|Metodo | URI della richiesta|
|-|-|
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/marketplacecharges|
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/marketplacecharges|
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/marketplacechargesbycustomdate?startTime=2017-01-01&endTime=2017-01-10|

> [!Note]
> Per usare la versione di anteprima dell'API, sostituire v2 con v1 nell'URL precedente.
>

## <a name="response"></a>Response
 
    
        [
            {
                "id": "id",
                "subscriptionGuid": "00000000-0000-0000-0000-000000000000",
                "subscriptionName": "subName",
                "meterId": "2core",
                "usageStartDate": "2015-09-17T00:00:00Z",
                "usageEndDate": "2015-09-17T23:59:59Z",
                "offerName": "Virtual LoadMaster™ (VLM) for Azure",
                "resourceGroup": "Res group",
                "instanceId": "id",
                "additionalInfo": "{\"ImageType\":null,\"ServiceType\":\"Medium\"}",
                "tags": "",
                "orderNumber": "order",
                "unitOfMeasure": "",
                "costCenter": "100",
                "accountId": 100,
                "accountName": "Account Name",
                "accountOwnerId": "account@live.com",
                "departmentId": 101,
                "departmentName": "Department 1",
                "publisherName": "Publisher 1",
                "planName": "Plan name",
                "consumedQuantity": 1.15,
                "resourceRate": 0.1,
                "extendedCost": 1.11
            },
            ...
        ]
    

**Definizioni delle proprietà di risposta**

|Nome proprietà| Tipo| Descrizione
|-|-|-|
|id|string|ID univoco per la voce di spese per il Marketplace|
|subscriptionGuid|Guid|GUID della sottoscrizione|
|subscriptionName|string|Nome della sottoscrizione|
|meterId|string|ID per il contatore generato|
|usageStartDate|DateTime|Ora di inizio per il record di uso|
|usageEndDate|DateTime|Ora di fine per il record di uso|
|offerName|string|Nome dell'offerta|
|resourceGroup|string|Gruppo di risorse|
|instanceId|string|ID istanza|
|additionalInfo|string|Stringa JSON per informazioni aggiuntive|
|tags|string|Stringa JSON di tag|
|orderNumber|string|Numero di ordine|
|unitOfMeasure|string|Unità di misura per il contatore|
|costCenter|string|Centro di costo|
|accountId|int|ID account|
|accountName|string |Nome dell'account|
|accountOwnerId|string|ID del proprietario dell'account|
|departmentId|int|ID reparto|
|departmentName|string|Nome del reparto|
|publisherName|string|Nome del nome dell'autore|
|planName|string|Nome del piano|
|consumedQuantity|decimal|Quantità consumata durante il periodo di tempo|
|resourceRate|decimal|Prezzo unitario per il contatore|
|extendedCost|decimal|Spesa prevista in base alla quantità usata e al costo esteso|
<br/>
## <a name="see-also"></a>Vedere anche

* [API per periodi di fatturazione](billing-enterprise-api-billing-periods.md)

* [API per dettagli sull'uso](billing-enterprise-api-usage-detail.md) 

* [API per saldi e riepilogo](billing-enterprise-api-balance-summary.md)

* [API per l'elenco prezzi](billing-enterprise-api-pricesheet.md)
