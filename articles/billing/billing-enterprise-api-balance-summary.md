---
title: API per clienti Enterprise per la fatturazione di Azure - Saldi e riepilogo | Microsoft Docs
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
ms.openlocfilehash: 7af4865a5455c314c1bdeb315fb87b0f92c1ddf9
ms.contentlocale: it-it
ms.lasthandoff: 05/03/2017


---
# <a name="reporting-apis-for-enterprise-customers---balance-and-summary-preview"></a>API di creazione report per clienti Enterprise - Saldi e riepilogo (anteprima)

L'API per saldi e riepilogo offre un riepilogo mensile delle informazioni su saldi, nuovi acquisti, addebiti per il servizio Azure Marketplace e spese per modifiche e da pagare in eccedenza.


##<a name="request"></a>Richiesta 
Le proprietà di intestazione comuni che devono essere aggiunte vengono specificate [qui](billing-enterprise-api.md). Se non viene specificato alcun periodo di fatturazione, vengono restituiti i dati per il periodo di fatturazione corrente.

|Metodo | URI della richiesta|
|-|-|
|GET| https://consumption.azure.com/v1/enrollments/{enrollmentNumber}/balancesummary|
|GET| https://consumption.azure.com/v1/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/balancesummary|


## <a name="response"></a>Response

        {
            "id": "enrollments/100/billingperiods/201507/balancesummaries",
              "billingPeriodId": 201507,
              "currencyCode": "USD",
              "beginningBalance": 0,
              "endingBalance": 1.1,
              "newPurchases": 1,
              "adjustments": 1.1,
              "utilized": 1.1,
              "serviceOverage": 1,
              "chargesBilledSeparately": 1,
              "totalOverage": 1,
              "totalUsage": 1.1,
              "azureMarketplaceServiceCharges": 1,
              "newPurchasesDetails": [
                {
                  "name": "",
                  "value": 1
                }
              ],
              "adjustmentDetails": [
                {
                  "name": "Promo Credit",
                  "value": 1.1
                },
                {
                  "name": "SIE Credit",
                  "value": 1.0
                }
              ]
        }


**Definizioni delle proprietà di risposta**

|Nome proprietà| Tipo| Descrizione
|-|-|-|
|id|string|ID univoco per un periodo di fatturazione e per una registrazione specifici|
|billingPeriodId|string |ID periodo di fatturazione|
|currencyCode|string |Codice della valuta|
|beginningBalance|decimal| Saldo iniziale per il periodo di fatturazione|
|endingBalance|decimal| Saldo finale per il periodo di fatturazione (viene aggiornato quotidianamente per i periodi aperti)|
|newPurchases|decimal| Totale nuovo importo di acquisto|
|adjustments|decimal| Quantità totale rettificata|
|utilized|decimal| Uso totale dell'impegno|
|serviceOverage|decimal| Eccedenza per i servizi di Azure|
|chargesBilledSeparately|decimal| chargesBilledSeparately|
|totalOverage|decimal| serviceOverage + chargesBilledSeparately|
|totalUsage|decimal| Eccedenza totale + impegno servizio di Azure|
|azureMarketplaceServiceCharges|decimal| Spese totali per Azure Marketplace|
|newPurchasesDetails|Matrice di stringhe JSON di coppie nome-valore|Elenco di nuovi acquisti|
|adjustmentDetails|Matrice di stringhe JSON di coppie nome-valore|Elenco di modifiche (credito promo, credito SIE e così via) |


<br/>
## <a name="see-also"></a>Vedere anche
* [API per periodi di fatturazione](billing-enterprise-api-billing-periods.md)

* [API per dettagli sull'uso](billing-enterprise-api-usage-detail.md) 

* [API per spese per il Marketplace Store](billing-enterprise-api-marketplace-storecharge.md) 

* [API per l'elenco prezzi](billing-enterprise-api-pricesheet.md)
