---
title: Esaminare i dati di fatturazione dell'Iscrizione Enterprise di Azure con l'API REST | Microsoft Docs
description: Imparare a usare le API REST di Azure per esaminare i dati di fatturazione dell'Iscrizione Enterprise.
services: billing
documentationcenter: na
author: lleonard-msft
manager: ''
editor: ''
ms.assetid: 82D50B98-40F2-44B1-A445-4391EA9EBBAA
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2018
ms.author: erikre
ms.openlocfilehash: 5cb26b98f5969032bcff95e4408fcf685399d6da
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2019
ms.locfileid: "56114502"
---
# <a name="review-enterprise-enrollment-billing-using-rest-apis"></a>Esaminare i dati di fatturazione dell'Iscrizione Enterprise tramite le API REST

Le API di creazione di report di Azure consentono di esaminare e gestire i costi di Azure.

Questo articolo illustra come recuperare le informazioni di fatturazione associate agli account di fatturazione, al reparto o agli account di registrazione del Contratto Enterprise (EA) tramite le API REST di Azure. 

## <a name="individual-account-billing"></a>Fatturazione di account singoli

Per ottenere i dettagli sull'utilizzo per account singoli in un reparto:

```http
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

Il parametro `{billingAccountId}` è obbligatorio e deve contenere l'ID per l'account.

Gli argomenti seguenti sono obbligatori: 

|Intestazione della richiesta|DESCRIZIONE|  
|--------------------|-----------------|  
|*Content-Type:*|Richiesto. Impostare su `application/json`.|  
|*Authorization:*|Richiesto. Impostare un valore valido per la `Bearer` [chiave API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based). |  

In questo esempio viene illustrata una chiamata sincrona che restituisce i dettagli del ciclo di fatturazione corrente. Per motivi di prestazioni, le chiamate sincrone restituiscono i dati relativi all'ultimo mese.  È anche possibile chiamare l'[API in modo asincrono](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) per restituire i dati relativi a 36 mesi.


## <a name="response"></a>Risposta  

Una risposta corretta prevede la restituzione del codice di stato 200 (OK) e dell'elenco dei costi dettagliati per l'account.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/BillingAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        ...
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        "instanceName": "shared1",
        "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-Web-eastasia/providers/Microsoft.Web/sites/shared1",
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        "isEstimated": false,
        ...
      }
    }
  ]
}
```  

Questo esempio è abbreviato. Per la descrizione completa di ogni campo della risposta e per la gestione degli errori, vedere [Get usage detail for a billing account](/rest/api/consumption/usagedetails/listbybillingaccount) (Ottenere i dettagli d'utilizzo per un account di fatturazione).

## <a name="department-billing"></a>Fatturazione di reparto 

È possibile ottenere i dettagli di utilizzo aggregati per tutti gli account di un reparto. 

```http
GET https://management.azure.com/providers/Microsoft.Billing/departments/{departmentId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

Il parametro `{departmentId}` è obbligatorio e deve contenere l'ID del reparto nell'account di registrazione.

Gli argomenti seguenti sono obbligatori: 

|Intestazione della richiesta|DESCRIZIONE|  
|--------------------|-----------------|  
|*Content-Type:*|Richiesto. Impostare su `application/json`.|  
|*Authorization:*|Richiesto. Impostare un valore valido per la `Bearer` [chiave API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based). |  

In questo esempio viene illustrata una chiamata sincrona che restituisce i dettagli del ciclo di fatturazione corrente. Per motivi di prestazioni, le chiamate sincrone restituiscono i dati relativi all'ultimo mese.  È anche possibile chiamare l'[API in modo asincrono](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) per restituire i dati relativi a 36 mesi.

### <a name="response"></a>Risposta  

Il codice di stato 200 (OK) viene restituito per una risposta con esito positivo, contenente un elenco di informazioni dettagliate sull'utilizzo e sui costi per un periodo di fatturazione e un ID fattura specifici per il reparto.


L'esempio seguente illustra l'output dell'API REST per il reparto `1234`.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/billingPeriods/201702",
        "invoiceId": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/invoices/201703-123456789",
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        "instanceName": "shared1",
        "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-Web-eastasia/providers/Microsoft.Web/sites/shared1",
        "instanceLocation": "eastasia",
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        ...
      }
    }
  ]
}
```  

Questo esempio è abbreviato. Per la descrizione completa di ogni campo della risposta e per la gestione degli errori, vedere [Get usage detail for a department](/rest/api/consumption/usagedetails/listbydepartment) (Ottenere i dettagli d'utilizzo per un reparto).

## <a name="enrollment-account-billing"></a>Fatturazione di account di registrazione

È possibile ottenere i dettagli di utilizzo aggregati per l'account di registrazione.

```http
GET GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/{enrollmentAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

Il parametro `{enrollmentAccountId}` è obbligatorio e deve contenere l'ID dell'account di registrazione.

Gli argomenti seguenti sono obbligatori: 

|Intestazione della richiesta|DESCRIZIONE|  
|--------------------|-----------------|  
|*Content-Type:*|Richiesto. Impostare su `application/json`.|  
|*Authorization:*|Richiesto. Impostare un valore valido per la `Bearer` [chiave API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based). |  

In questo esempio viene illustrata una chiamata sincrona che restituisce i dettagli del ciclo di fatturazione corrente. Per motivi di prestazioni, le chiamate sincrone restituiscono i dati relativi all'ultimo mese.  È anche possibile chiamare l'[API in modo asincrono](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) per restituire i dati relativi a 36 mesi.

### <a name="response"></a>Risposta  

Il codice di stato 200 (OK) viene restituito per una risposta con esito positivo, contenente un elenco di informazioni dettagliate sull'utilizzo e sui costi per un periodo di fatturazione e un ID fattura specifici per il reparto.

L'esempio seguente illustra l'output dell'API REST per l'Iscrizione Enterprise `1234`.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702",
        "invoiceId": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/invoices/201703-123456789",
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        ....
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        ...
      }
    }
  ]
}
``` 

Questo esempio è abbreviato. Per la descrizione completa di ogni campo della risposta e per la gestione degli errori, vedere [Get usage detail for an enrollment account](/rest/api/consumption/usagedetails/listbyenrollmentaccount) (Ottenere i dettagli d'utilizzo per un account di registrazione).

## <a name="next-steps"></a>Passaggi successivi 
- Vedere [Panoramica della creazione di report aziendali](https://docs.microsoft.com/azure/billing/billing-enterprise-api)
- Approfondire [Enterprise Billing REST API](https://docs.microsoft.com/rest/api/billing/) (API REST di fatturazione Enterprise)   
- [Introduzione all'API REST di Azure](https://docs.microsoft.com/rest/api/azure/)   
