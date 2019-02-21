---
title: Parametri e intestazioni comuni
description: Parametri e intestazioni comuni a tutte le operazioni eseguibili con le risorse di Key Vault.
services: key-vault
documentationcenter: ''
author: bryanla
manager: barbkess
tags: azure-resource-manager
ms.assetid: a715d13ca9-d6e8-4e54-ac5e-0ed9400fb15b15d13ca9-d6e8-4e54-ac5e-0ed9400fb15b
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: bryanla
ms.openlocfilehash: 1ac0f54aa4dfdc9db4724629c4dbfe7a4982838f
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/15/2019
ms.locfileid: "56301231"
---
# <a name="common-parameters-and-headers"></a>Parametri e intestazioni comuni

Le seguenti informazioni sono comuni a tutte le operazioni che è possibile eseguire in relazione alle risorse di Key Vault:

- Sostituire `{api-version}` con la versione dell'API nell'URI.
- Sostituire `{subscription-id}` con l'identificatore della sottoscrizione nell'URI
- Sostituire `{resource-group-name}` con il gruppo di risorse. Per altre informazioni, vedere Uso di gruppi di risorse per gestire le risorse di Azure.
- Sostituire `{vault-name}` con il nome dell'insieme di credenziali delle chiavi nell'URI.
- Impostare l'intestazione Content-Type su application/json.
- Impostare l'intestazione dell'autorizzazione su un token JSON Web ottenuto da Azure Active Directory. Per altre informazioni, vedere [Autenticazione delle richieste di Azure Resource Manager](authentication-requests-and-responses.md).

## <a name="common-error-response"></a>Risposta di errore comune
Il servizio userà i codici di stato HTTP per indicare l'esito positivo o negativo. Gli errori contengono inoltre una risposta nel formato seguente:

```
   {  
     "error": {  
     "code": "BadRequest",  
     "message": "The key vault sku is invalid."  
     }  
   }  
```

|Nome dell'elemento | Type | DESCRIZIONE |
|---|---|---|
| code | stringa | Il tipo di errore che si è verificato.|
| Message | stringa | Descrizione della causa dell'errore. |



## <a name="see-also"></a>Vedere anche
 [Informazioni di riferimento sull'API REST di Azure Key Vault](/rest/api/keyvault/)
