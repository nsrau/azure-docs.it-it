---
title: Definire un profilo tecnico OAuth2 in un criterio personalizzato in Azure Active Directory B2C | Microsoft Docs
description: Definire un profilo tecnico OAuth2 in un criterio personalizzato in Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: fde556c60f823f4bd287ca5672503158c7292f51
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2019
ms.locfileid: "58918927"
---
# <a name="define-an-oauth2-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definire un profilo tecnico OAuth2 in un criterio personalizzato di Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory (Azure AD) B2C fornisce assistenza per il provider di identità di protocollo OAuth2. Questo è il protocollo principale per l'autorizzazione e l'autenticazione delegata. Per altre informazioni, vedere la [RFC 6749 The OAuth 2.0 Authorization Framework](https://tools.ietf.org/html/rfc6749). Con il profilo tecnico OAuth2 è possibile attuare la federazione con un provider di identità basato su OAuth2, ad esempio Facebook e Live.com, consentendo agli utenti di effettuare l'accesso con i propri social network o con le identità aziendali.

## <a name="protocol"></a>Protocollo

L'attributo **Nome** dell'elemento **Protocollo** deve essere impostato su `OAuth2`. Ad esempio, il protocollo per il profilo tecnico **Facebook-OAUTH** è `OAuth2`:

```XML
<TechnicalProfile Id="Facebook-OAUTH">
  <DisplayName>Facebook</DisplayName>
  <Protocol Name="OAuth2" />
  ...    
```

## <a name="input-claims"></a>Attestazioni di input

Gli elementi **InputClaims** e **InputClaimsTransformations** non sono obbligatori. Tuttavia, è possibile inviare parametri aggiuntivi al provider di identità. L'esempio seguente aggiunge il parametro della stringa di query **domain_hint** e il valore di `contoso.com` alla richiesta di autorizzazione.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>Attestazioni di output

L'elemento **OutputClaims** contiene un elenco di attestazioni restituite dal provider di identità OAuth2. Può essere necessario eseguire il mapping del nome dell'attestazione definito nei criteri per il nome definito nel provider di identità. È anche possibile includere le attestazioni che non vengono restituite dal provider di identità, purché sia impostato l'attributo `DefaultValue`.

L'elemento **OutputClaimsTransformations** può contenere una raccolta di elementi **OutputClaimsTransformation** che vengono usati per modificare le attestazioni di output o per generarne di nuove.

L'esempio seguente illustra le attestazioni restituite dal provider di identità Facebook:

- Il mapping dell'attestazione **first_name** viene eseguito per l'attestazione **givenName**.
- Il mapping dell'attestazione **last_name** viene eseguito per l'attestazione **surname**.
- L'attestazione **displayName** senza l'esecuzione del mapping del nome.
- L'attestazione **email** senza l'esecuzione del mapping del nome.

Il profilo tecnico restituisce anche le attestazioni che non vengono restituite dal provider di identità: 

- L'attestazione **identityProvider** che contiene il nome del provider di identità.
- L'attestazione **authenticationSource** con un valore predefinito di **socialIdpAuthentication**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="id" />
  <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
  <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="facebook.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Metadata

| Attributo | Obbligatorio | DESCRIZIONE |
| --------- | -------- | ----------- |
| client_id | Sì | L'identificatore dell'attestazione del provider di identità. |
| IdTokenAudience | No  | I destinatari dell'id_token. Se specificato, Azure AD B2C controlla se il token è in un'attestazione restituita dal provider di identità ed è uguale a quello specificato. |
| authorization_endpoint | Sì | L'URL dell'endpoint di autorizzazione come per RFC 6749. |
| AccessTokenEndpoint | Sì | L'URL dell'endpoint token come per RFC 6749. |  
| ClaimsEndpoint | Sì | L'URL dell'endpoint di informazioni per l'utente come per RFC 6749. | 
| AccessTokenResponseFormat | No  | Il formato delle chiamate a endpoint del token di accesso. Ad esempio, Facebook richiede un metodo HTTP GET, ma la risposta del token di accesso è in formato JSON. |
| AdditionalRequestQueryParameters | No  | Parametri di query della richiesta aggiuntivi. Ad esempio, è possibile inviare parametri aggiuntivi al provider di identità. È possibile includere più parametri usando la virgola di delimitazione. | 
| ClaimsEndpointAccessTokenName | No  | Il nome del parametro della stringa di query del token di accesso. Gli endpoint di alcuni provider di identità delle attestazioni supportano la richiesta HTTP GET. In questo caso, il token di connessione viene inviato usando un parametro della stringa di query anziché l'intestazione dell'autorizzazione. |
| ClaimsEndpointFormatName | No  | Il nome del parametro della stringa di query di formato. Ad esempio, è possibile impostare il nome come `format` in questo endpoint attestazioni LinkedIn`https://api.linkedin.com/v1/people/~?format=json`. | 
| ClaimsEndpointFormat | No  | Il valore del parametro della stringa di query di formato. Ad esempio, è possibile impostare il valore su `json` in questo endpoint attestazioni LinkedIn `https://api.linkedin.com/v1/people/~?format=json`. | 
| ProviderName | No  | Il nome del provider di identità. |
| response_mode | No  | Il metodo che usa il provider di identità per restituire il risultato ad Azure AD B2C. I valori possibili sono: `query`, `form_post` (impostazione predefinita), o `fragment`. |
| scope | No  | L'ambito della richiesta di accesso definita sulla base della specifica del provider di identità OAuth2. Ad esempio `openid`, `profile`, e `email`. |
| HttpBinding | No  | L'associazione HTTP prevista per il token di accesso e per gli endpoint del token delle attestazioni. I valori possibili sono: `GET` o `POST`.  |
| ResponseErrorCodeParamName | No  | Il nome del parametro che contiene il messaggio di errore restituito nel messaggio HTTP 200 (Ok). |
| ExtraParamsInAccessTokenEndpointResponse | No  | Contiene altri parametri che possono essere restituiti nella risposta di **AccessTokenEndpoint** da alcuni provider di identità. Ad esempio, la risposta di **AccessTokenEndpoint** contiene un altro parametro, ad esempio `openid`, ovvero un parametro obbligatorio oltre all'access_token in una stringa di query della richiesta **ClaimsEndpoint**. Più nomi di parametro devono essere preceduti dal carattere di escape e separati da una virgola di delimitazione ','. |
| ExtraParamsInClaimsEndpointRequest | No  | Contiene altri parametri che possono essere restituiti nella richiesta **ClaimsEndpoint** da alcuni provider di identità. Più nomi di parametro devono essere preceduti dal carattere di escape e separati da una virgola di delimitazione ','. |

## <a name="cryptographic-keys"></a>Chiavi crittografiche

L'elemento **CryptographicKeys** contiene l'attributo seguente:

| Attributo | Obbligatorio | DESCRIZIONE |
| --------- | -------- | ----------- |
| client_secret | Sì | Il segreto client dell'applicazione del provider di identità. La chiave di crittografia è necessaria solo se i metadati **response_type** sono impostati su `code`. In questo caso, Azure AD B2C effettua un'altra chiamata per scambiare il codice di autorizzazione per un token di accesso. Se i metadati sono impostati su `id_token` è possibile omettere la chiave di crittografia.  |  

## <a name="redirect-uri"></a>URI di reindirizzamento

Quando si configura l'URL di reindirizzamento del provider di identità, immettere `https://login.microsoftonline.com/te/tenant/policyId/oauth2/authresp`. Assicurarsi di sostituire il **tenant** con il nome del tenant (ad esempio, contosob2c.onmicrosoft.com) e **policyId** con l'identificatore dei criteri (ad esempio, b2c_1a_policy). L'URI di reindirizzamento deve essere tutto minuscolo.

Se si usa il dominio **b2clogin.com** anziché **login.microsoftonline.com** assicurarsi di usare b2clogin.com invece di login.microsoftonline.com.

Esempi:

- [Aggiungere Google + come provider di identità OAuth2 tramite criteri personalizzati](active-directory-b2c-custom-setup-goog-idp.md)













