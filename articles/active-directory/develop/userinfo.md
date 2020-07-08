---
title: Endpoint UserInfo di Microsoft Identity Platform | Azure
titleSuffix: Microsoft identity platform
description: Informazioni sull'endpoint UserInfo sulla piattaforma di identità Microsoft.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/22/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 08386e2c54a45fe4a6e35ce6d7ebb51145827a6d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84268439"
---
# <a name="microsoft-identity-platform-userinfo-endpoint"></a>Endpoint UserInfo di Microsoft Identity Platform

L'endpoint UserInfo fa parte dello [standard OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html#UserInfo) (OIDC), progettato per restituire attestazioni relative all'utente che ha eseguito l'autenticazione.  Per la piattaforma di identità Microsoft, l'endpoint UserInfo è ospitato in Microsoft Graph ( https://graph.microsoft.com/oidc/userinfo) . 

## <a name="find-the-well-known-configuration-endpoint"></a>Trovare l'endpoint di configurazione. well-known

È possibile individuare l'endpoint UserInfo a livello di codice usando il documento di individuazione di OpenID Connect, all'indirizzo `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration` . Viene elencato nel `userinfo_endpoint` campo e questo modello può essere usato tra i cloud per fare riferimento all'endpoint corretto.  Non è consigliabile impostare come hardcoded l'endpoint UserInfo nell'app. utilizzare il documento di individuazione OIDC per trovare questo endpoint in fase di esecuzione.

Come parte della specifica di OpenID Connect, l'endpoint UserInfo viene spesso chiamato automaticamente da [librerie conformi a OIDC](https://openid.net/developers/certified/) per ottenere informazioni sull'utente.  Senza l'hosting di un endpoint di questo tipo, la piattaforma di identità Microsoft non sarà conforme agli standard e alcune librerie avranno esito negativo.  Dall' [elenco di attestazioni identificato nello standard OIDC](https://openid.net/specs/openid-connect-core-1_0.html#StandardClaims) , le attestazioni del nome, l'attestazione dell'oggetto e il messaggio di posta elettronica vengono prodotti quando disponibili e sono consentiti per.  

## <a name="consider-use-an-id-token-instead"></a>Prendere in considerazione: usare invece un token ID

Le informazioni disponibili nel token ID che l'app può ricevere sono un superset delle informazioni che può ottenere dall'endpoint UserInfo.  Poiché è possibile ottenere un token ID nello stesso momento in cui si ottiene un token per chiamare l'endpoint UserInfo, è consigliabile usare tale token ID per ottenere informazioni sull'utente anziché chiamare l'endpoint UserInfo.  L'uso del token ID eliminerà una o due richieste di rete dall'avvio dell'applicazione, riducendo la latenza nell'applicazione.

Se sono necessari altri dettagli sull'utente, è necessario chiamare l' [ `/user` API Microsoft Graph](https://docs.microsoft.com/graph/api/user-get) per ottenere informazioni come il numero di ufficio o il titolo del processo.   È anche possibile usare [attestazioni facoltative](active-directory-optional-claims.md) per includere informazioni utente aggiuntive nell'ID e nei token di accesso.

## <a name="calling-the-userinfo-endpoint"></a>Chiamata all'endpoint UserInfo

UserInfo è un'API del token di connessione OAuth standard, denominata come qualsiasi altra API Microsoft Graph usando il token di accesso ricevuto durante il recupero di un token per Microsoft Graph. Restituisce una risposta JSON che contiene le attestazioni relative all'utente.

### <a name="permissions"></a>Autorizzazioni

Usare le [autorizzazioni OIDC](v2-permissions-and-consent.md#openid-connect-scopes) seguenti per chiamare l'API UserInfo. `openid`è obbligatorio e gli `profile` `email` ambiti e assicurano che nella risposta vengano fornite informazioni aggiuntive.

|Tipo di autorizzazione      | Autorizzazioni    |
|:--------------------|:---------------------------------------------------------|
|Delegato (account aziendale o dell'Istituto di istruzione) | OpenID (obbligatorio), profilo, indirizzo di posta elettronica |
|Delegata (personale account Microsoft) | OpenID (obbligatorio), profilo, indirizzo di posta elettronica |
|Applicazione | Non applicabile |

> [!TIP]
> Copiare questo URL nel browser per ottenere un token per l'endpoint UserInfo, nonché un [token ID](id-tokens.md) e sostituire l'ID client e l'URI di reindirizzamento con il proprio. Si noti che richiede solo ambiti per gli ambiti OpenID o Graph e nient'altro.  Questa operazione è necessaria, poiché non è possibile richiedere autorizzazioni per due risorse diverse nella stessa richiesta di token.
>
> `https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=<yourClientID>&response_type=token+id_token&redirect_uri=<YourRedirectUri>&scope=user.read+openid+profile+email&response_mode=fragment&state=12345&nonce=678910`
>
> È possibile usare questo token di accesso nella sezione successiva.

Come per qualsiasi altro token di Microsoft Graph, il token ricevuto qui potrebbe non essere un JWT. Se è stato effettuato l'accesso a un utente account Microsoft, sarà un formato di token crittografato. Questo perché Microsoft Graph ha un modello di rilascio di token speciale. Questa operazione non ha alcun effetto sulla possibilità di usare il token di accesso per chiamare l'endpoint UserInfo.

### <a name="calling-the-api"></a>Chiamata all'API

L'API UserInfo supporta sia GET che POST, per la specifica OIDC.

```http
GET or POST /oidc/userinfo HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJub25jZSI6Il…
```

### <a name="userinfo-response"></a>Risposta UserInfo

```jsonc
{
    "sub": "OLu859SGc2Sr9ZsqbkG-QbeLgJlb41KcdiPoLYNpSFA",
    "name": "Mikah Ollenburg", // names all require the “profile” scope.
    "family_name": " Ollenburg",
    "given_name": "Mikah",
    "email": "mikoll@contoso.com" //requires the “email” scope.
}
```

Le attestazioni elencate qui, incluse `sub` , sono le stesse attestazioni che l'app dovrebbe visualizzare nel [token ID](id-tokens.md) emesso per l'app.  

## <a name="notes-and-caveats-on-the-userinfo-endpoint"></a>Note e avvertenze sull'endpoint UserInfo

* Se si vuole chiamare questo endpoint UserInfo, è necessario usare l'endpoint v 2.0.  Se si usa l'endpoint v 1.0 si otterrà un token per l'endpoint UserInfo v 1.0, ospitato in login.microsoftonline.com.  È consigliabile che tutte le app e le librerie conformi a OIDC usino l'endpoint v 2.0 per garantire la compatibilità.
* Non è possibile personalizzare la risposta dall'endpoint UserInfo.  Per personalizzare le attestazioni, usare il mapping delle [attestazioni]( active-directory-claims-mapping.md) per modificare le informazioni restituite nei token.
* Non è possibile aggiungere la risposta dall'endpoint UserInfo a.  Se si vuole ottenere altre attestazioni sull'utente, usare [attestazioni facoltative]( active-directory-optional-claims.md) per aggiungere nuove attestazioni ai token.

## <a name="next-steps"></a>Passaggi successivi

* [Esaminare il contenuto dei token ID](id-tokens.md)
* [Personalizzare il contenuto di un token ID usando attestazioni facoltative](active-directory-optional-claims.md)
* [Richiedere un token di accesso e un token ID usando il protocollo OAuth2](v2-protocols-oidc.md)