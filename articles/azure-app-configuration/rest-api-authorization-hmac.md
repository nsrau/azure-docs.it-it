---
title: API REST di configurazione app Azure-autorizzazione HMAC
description: Usare HMAC per l'autorizzazione per la configurazione di app Azure usando l'API REST
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 8d1f8a17f51711cc5c10567797e1224f96eb7630
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424160"
---
# <a name="hmac-authorization---rest-api-reference"></a>Autorizzazione HMAC: informazioni di riferimento sull'API REST

Quando si usa l'autenticazione HMAC, le operazioni rientreranno in una delle due categorie, ovvero Read o Write. Le chiavi di accesso in lettura e scrittura concedono l'autorizzazione per chiamare tutte le operazioni. Le chiavi di accesso di sola lettura concedono l'autorizzazione per chiamare solo le operazioni di lettura. Se una chiave di accesso è di sola lettura o in lettura/scrittura è determinata dalla relativa `readOnly` Proprietà. Qualsiasi tentativo di eseguire una richiesta di scrittura con una chiave di accesso di sola lettura comporterà la mancata autorizzazione della richiesta.

## <a name="obtaining-access-keys"></a>Recupero delle chiavi di accesso

Le specifiche che descrivono le chiavi di accesso e l'API usata per ottenerle sono descritte in dettaglio nella specifica del provider di risorse di configurazione [app Azure.](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/appconfiguration/resource-manager/Microsoft.AppConfiguration/stable/2019-10-01/appconfiguration.json) Le chiavi di accesso vengono ottenute tramite l'operazione "ConfigurationStores_ListKeys".

## <a name="errors"></a>Errors

```http
HTTP/1.1 403 Forbidden
```

**Motivo:** La chiave di accesso usata per autenticare la richiesta non fornisce le autorizzazioni necessarie per eseguire l'operazione richiesta.
**Soluzione:** Ottenere una chiave di accesso che fornisce l'autorizzazione per eseguire l'operazione richiesta e usarla per autenticare la richiesta.
