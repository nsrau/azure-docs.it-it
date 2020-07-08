---
title: Parametri e intestazioni comuni
description: Parametri e intestazioni comuni a tutte le operazioni eseguibili con le risorse di Key Vault.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: d1d93bcd84fd9460e658b221089a4b24d46b0429
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83005807"
---
# <a name="common-parameters-and-headers"></a>Parametri e intestazioni comuni

Le seguenti informazioni sono comuni a tutte le operazioni che è possibile eseguire in relazione alle risorse di Key Vault:

- L' `Host` intestazione HTTP deve essere sempre presente e deve specificare il nome host dell'insieme di credenziali. Esempio: `Host: contoso.vault.azure.net`. Si noti che la maggior parte delle tecnologie client popola l' `Host` intestazione dall'URI. Ad esempio, `GET https://contoso.vault.azure.net/secrets/mysecret{...}` imposta `Host` come `contoso.vault.azure.net` . Ciò significa che se si accede a Key Vault usando un indirizzo IP non elaborato, ad esempio `GET https://10.0.0.23/secrets/mysecret{...}` , il valore automatico dell' `Host` intestazione sarà errato e sarà necessario assicurarsi manualmente che l' `Host` intestazione contenga il nome host dell'insieme di credenziali.
- Sostituire `{api-version}` con la versione dell'API nell'URI.
- Sostituire `{subscription-id}` con l'identificatore della sottoscrizione nell'URI
- Sostituire `{resource-group-name}` con il gruppo di risorse. Per altre informazioni, vedere Uso di gruppi di risorse per gestire le risorse di Azure.
- Sostituire `{vault-name}` con il nome dell'insieme di credenziali delle chiavi nell'URI.
- Impostare l'intestazione Content-Type su application/json.
- Impostare l'intestazione dell'autorizzazione su un token JSON Web ottenuto da Azure Active Directory. Per ulteriori informazioni, vedere [autenticazione](authentication-requests-and-responses.md) delle richieste di Azure Resource Manager.

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

|Nome dell'elemento | Type | Descrizione |
|---|---|---|
| codice | string | Il tipo di errore che si è verificato.|
| message | string | Descrizione della causa dell'errore. |



## <a name="see-also"></a>Vedere anche
 [Informazioni di riferimento sull'API REST di Azure Key Vault](/rest/api/keyvault/)
