---
title: Considerazioni sull'API - Azure Marketplace
description: Controllo delle versioni, gestione degli errori e problemi di autorizzazione quando si usano le API del marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: e4d4d5cb16e1037458d09f8c7681ab2d2ecf8676
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81256332"
---
# <a name="api-considerations"></a>Considerazioni sulle API

<a name="api-versioning"></a>Controllo delle versioni API
--------------

> [!NOTE]
> Le API del portale per i partner cloud sono integrate con il Centro per i partner e continueranno a funzionare dopo la migrazione delle offerte al Centro per i partner. L'integrazione introduce piccole modifiche. Esaminare le modifiche elencate in Riferimento all'API del [portale per i partner cloud](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) per assicurarsi che il codice continui a funzionare dopo la migrazione al Centro per i partner.

Potrebbero essere presenti più versioni dell'API disponibili nello stesso momento. I client devono indicare di quale versione intendono richiamare l'uso specificando il parametro `api-version` nell'ambito della stringa di query.

   `GET https://cloudpartner.azure.com/api/offerTypes?api-version=2017-10-31`

La risposta a una richiesta con una versione API sconosciuta o non valida è un codice HTTP 400. Questo errore restituisce la raccolta delle versioni di API note nel corpo della risposta.

``` json
    {
        "error": { 
            "code":"InvalidAPIVersion",
            "message":"Invalid api version. Allowed values are [2016-08-01-preview]"
        }
    }
```            

<a name="errors"></a>Errors
------

L'API risponde agli errori con i codici di stato HTTP corrispondenti ed eventualmente altre informazioni nella risposta serializzata in formato JSON.
Quando si riceve un errore, in particolare un errore di classe 400, non ripetere la richiesta prima di aver corretto la causa sottostante. Ad esempio, nella risposta di esempio precedente, correggere il parametro della versione API prima di inviare nuovamente la richiesta.

<a name="authorization-header"></a>Authorization header (Intestazione dell'autorizzazione)
--------------------

Per tutte le API in questo riferimento, è necessario passare l'intestazione dell'autorizzazione insieme al token di connessione ottenuto da Azure Active Directory (Azure AD). Questa intestazione è obbligatoria per ricevere una risposta valida; se non è presente, viene restituito un errore `401 Unauthorized`. 

``` HTTP
  GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview

    Accept: application/json 
    Authorization: Bearer <YOUR_TOKEN> 
```
