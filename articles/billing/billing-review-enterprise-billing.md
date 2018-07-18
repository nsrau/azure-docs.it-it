---
title: Esaminare i dati di fatturazione dell'Iscrizione Enterprise di Azure con l'API REST | Microsoft Docs
description: Imparare a usare le API REST di Azure per esaminare i dati di fatturazione dell'Iscrizione Enterprise.
services: billing
documentationcenter: na
author: lleonard-msft
manager: MBaldwin
editor: ''
ms.assetid: 82D50B98-40F2-44B1-A445-4391EA9EBBAA
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2018
ms.author: alleonar
ms.openlocfilehash: 046b2e31aaefa5916a42b3652f9e6a8fdceff367
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064618"
---
# <a name="review-enterprise-enrollment-billing-using-rest-apis"></a>Esaminare i dati di fatturazione dell'Iscrizione Enterprise tramite le API REST

Le API di creazione di report di Azure consentono di esaminare e gestire i costi di Azure.

Vengono qui fornite informazioni per recuperare la fattura corrente associata a un account di Iscrizione Enterprise.

Per recuperare la fattura corrente:
``` http
GET https://consumption.azure.com/v2/enrollments/{enrollmentID}/usagedetails
Content-Type: application/json   
Authorization: Bearer
```

## <a name="build-the-request"></a>Compilare la richiesta  

Il parametro `{enrollmentID}` è obbligatorio e deve contenere l'ID di registrazione per l'account Enterprise.

Gli argomenti seguenti sono obbligatori: 

|Intestazione della richiesta|DESCRIZIONE|  
|--------------------|-----------------|  
|*Content-Type:*|Richiesto. Impostare su `application/json`.|  
|*Authorization:*|Richiesto. Impostare un valore valido per la `Bearer` [chiave API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based). |  

In questo esempio viene illustrata una chiamata sincrona che restituisce i dettagli del ciclo di fatturazione corrente. Per motivi di prestazioni, le chiamate sincrone restituiscono i dati relativi all'ultimo mese.  È anche possibile chiamare l'[API in modo asincrono](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) per restituire i dati relativi a 36 mesi.


## <a name="response"></a>Risposta  

Una risposta corretta prevede la restituzione del codice di stato 200 (OK), con un elenco dei costi dettagliati per l'account.

``` json
{
    "id": "${id}",
    "data": [
        {
            "cost": ${cost}, 
            "departmentId": ${departmentID},
            "subscriptionGuid" : ${subscriptionGuid} 
            "date": "${date}",
            "tags": "${tags}",
            "resourceGroup": "${resourceGroup}"
        } // ...
    ],
    "nextLink": "${nextLinkURL}"
}
```  

Ogni elemento dei **dati** rappresenta un addebito:

|Proprietà Response|DESCRIZIONE|
|----------------|----------|
|**cost** | Importo addebitato, in una valuta appropriata per l'ubicazione del data center. |
|**subscriptionGuid** | ID univoco globale per la sottoscrizione. | 
|**departmentId** | ID del reparto, se presente. |
|**date** | Data di addebito. |
|**tag** | Stringa JSON contenente i tag associati alla sottoscrizione. |
|**resourceGroup**|Nome del gruppo di risorse contenente l'oggetto inerente al costo. |
|**nextLink**| Se impostato, specifica un URL per la "pagina" dettagli successiva. Se la pagina è l'ultima, è vuoto. |  
||
  
ID reparto, gruppi di risorse, tag e campi correlati vengono definiti dall'amministratore EA.  

Questo esempio è abbreviato; vedere [Get usage details](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) (Ottenere i dettagli d'uso) per una descrizione completa di ogni campo di risposta. 

Altri codici di stato indicano condizioni di errore. In questi casi l'oggetto risposta spiega il motivo per cui la richiesta non è riuscita.

``` json
{  
  "error": [  
    { "code": "Error type." 
      "message": "Error response describing why the operation failed."  
    }  
  ]  
}  
```  

## <a name="next-steps"></a>Passaggi successivi 
- Vedere [Panoramica della creazione di report aziendali](https://docs.microsoft.com/azure/billing/billing-enterprise-api)
- Approfondire [Enterprise Billing REST API](https://docs.microsoft.com/rest/api/billing/) (API REST di fatturazione Enterprise)   
- [Introduzione all'API REST di Azure](https://docs.microsoft.com/rest/api/azure/)   
