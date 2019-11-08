---
title: Considerazioni sulle API | Azure Marketplace
description: Controllo delle versioni, gestione degli errori e problemi di autorizzazione quando si usano le API del marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 07cdb5e44dde0ca655191111d0a23dbab85b4cb2
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819737"
---
# <a name="api-considerations"></a>Considerazioni sulle API


<a name="api-versioning"></a>Controllo delle versioni API
--------------

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
