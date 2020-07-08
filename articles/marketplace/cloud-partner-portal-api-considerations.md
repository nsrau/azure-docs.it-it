---
title: Considerazioni sulle API-Azure Marketplace
description: Controllo delle versioni, gestione degli errori e problemi di autorizzazione quando si usano le API del marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 11ec6f9a38c5fe957eba922f3136f4fa0b2c4995
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85516058"
---
# <a name="api-considerations"></a>Considerazioni sulle API

<a name="api-versioning"></a>Controllo delle versioni API
--------------

> [!NOTE]
> Le API del portale Cloud Partner sono integrate con il Centro per i partner e continueranno a funzionare dopo la migrazione delle offerte in esso. L'integrazione introduce piccole modifiche. Esaminare le modifiche elencate in [portale cloud partner riferimento API](./cloud-partner-portal-api-overview.md) per assicurarsi che il codice continui a funzionare dopo la migrazione al centro per i partner.

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

<a name="errors"></a>Errori
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
