---
title: API per clienti Enterprise per la fatturazione di Azure - Dettagli sull'uso | Microsoft Docs
description: Informazioni sull'utilizzo dell'API di fatturazione e dell'API RestCard di Azure, utilizzate per offrire informazioni sul consumo di risorse e sulle tendenze di Azure.
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
ms.translationtype: HT
ms.sourcegitcommit: 6e76ac40e9da2754de1d1aa50af3cd4e04c067fe
ms.openlocfilehash: 0f751063510707d53ac76a287aa420818a31b04b
ms.contentlocale: it-it
ms.lasthandoff: 07/31/2017

---
# <a name="reporting-apis-for-enterprise-customers---usage-details"></a>API di creazione di report per i clienti Enterprise - Dettagli di utilizzo

L'API per dettagli sull'uso offre un'analisi giornaliera dettagliata delle quantità usate e delle spese stimate in base a una registrazione. Il risultato include anche informazioni su istanze, contatori e reparti. Le query sull'API possono essere eseguite in base al periodo di fatturazione oppure in base a un intervallo definito da date di inizio e di fine specificate. 
## <a name="consumption-apis"></a>API per l'uso


##<a name="request"></a>Richiesta 
Le proprietà di intestazione comuni che devono essere aggiunte vengono specificate [qui](billing-enterprise-api.md). Se non viene specificato alcun periodo di fatturazione, vengono restituiti i dati per il periodo di fatturazione corrente. Gli intervalli di tempo personalizzato possono essere specificati con parametri di data di inizio e di fine nel formato aaaa-MM-gg. L'intervallo di tempo massimo supportato è 36 mesi.  

|Metodo | URI della richiesta|
|-|-|
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/usagedetails 
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/usagedetails|
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/usagedetailsbycustomdate?startTime=2017-01-01&endTime=2017-01-10|

> [!Note]
> Per usare la versione di anteprima dell'API, sostituire v2 con v1 nell'URL precedente.
>

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
            "Cost": 0,
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
        ],
        "nextLink": "string"
    }

<br/>
**Definizioni delle proprietà di risposta**

|Nome proprietà| Tipo| Descrizione
|-|-|-|
|id| string| ID univoco per la chiamata all'API. |
|data| Matrice JSON| Matrice dei dettagli sull'uso giornaliero per ogni istanza\contatore.|
|nextLink| string| Quando sono presenti più pagine di dati, la proprietà nextLink punta all'URL per restituire la pagina di dati successiva. |
|accountId| int| Campo obsoleto. Presente per compatibilità con le versioni precedenti. |
|productId| int| Campo obsoleto. Presente per compatibilità con le versioni precedenti. |
|resourceLocationId| int| Campo obsoleto. Presente per compatibilità con le versioni precedenti. |
|consumedServiceID| int| Campo obsoleto. Presente per compatibilità con le versioni precedenti. |
|departmentId| int| Campo obsoleto. Presente per compatibilità con le versioni precedenti. |
|accountOwnerEmail| string| Account di posta elettronica del proprietario dell'account. |
|accountName| string| Nome dell'account immesso dal cliente. |
|serviceAdministratorId| string| Indirizzo di posta elettronica dell'amministratore del servizio. |
|subscriptionId| int| Campo obsoleto. Presente per compatibilità con le versioni precedenti. |
|subscriptionGuid| string| Identificatore univoco globale per la sottoscrizione. |
|subscriptionName| string| Nome della sottoscrizione. |
|date| string| Data in cui si è verificato l'utilizzo. |
|product| string| Dettagli aggiuntivi sul contatore. Esempio: A1(VM)Windows - Asia Pacifico orientale|
|meterId| string| Identificatore per il contatore che ha emesso l'utilizzo. |
|meterCategory| string| Indica il servizio della piattaforma Azure usato. |
|meterSubCategory| string| Definisce il servizio di Azure e può influire sulla tariffa. Esempio: A1 VM (non Windows)|
|meterRegion| string| Identifica la posizione del datacenter per determinati servizi il cui prezzo dipende dalla posizione stessa. |
|meterName| string| Nome del contatore. |
|consumedQuantity| double| Quantità utilizzata del contatore. |
|resourceRate| double| Tariffa applicabile per ogni unità fatturabile. |
|cost| double| Addebito applicato per il contatore. |
|resourceLocation| string| Identifica il data center in cui il contatore è in esecuzione. |
|consumedService| string| Indica il servizio della piattaforma Azure usato. |
|instanceId| string| Questo identificatore indica il nome della risorsa o l'ID risorsa completo. Per altre informazioni, vedere l'[API di Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources). |
|serviceInfo1| string| Metadati del servizio di Azure interno. |
|serviceInfo2| string| Ad esempio, un tipo di immagine per una macchina virtuale e un nome di ISP per ExpressRoute. |
|additionalInfo| string| Metadati specifici del servizio. Ad esempio un tipo di immagine per una macchina virtuale. |
|tags| string| Tag aggiunti dal cliente. Per altre informazioni, vedere [Organize your Azure resources with tags](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-using-tags) (Organizzare le risorse di Azure con i tag). |
|storeServiceIdentifier| string| Questa colonna non viene usata. Presente per compatibilità con le versioni precedenti. |
|departmentName| string| Nome del reparto. |
|costCenter| string| Centro di costo a cui è associato l'utilizzo. |
|unitOfMeasure| string| Identifica l'unità in base alla quale viene addebitato il servizio. Esempio: GB, ore, decine di migliaia. |
|resourceGroup| string| Definisce il gruppo di risorse in cui è in esecuzione il contatore distribuito. Per altre informazioni, vedere [Panoramica di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). |
<br/>
## <a name="see-also"></a>Vedere anche

* [API per periodi di fatturazione](billing-enterprise-api-billing-periods.md)

* [API per saldi e riepilogo](billing-enterprise-api-balance-summary.md)

* [API per spese per il Marketplace Store](billing-enterprise-api-marketplace-storecharge.md) 

* [API per l'elenco prezzi](billing-enterprise-api-pricesheet.md)

