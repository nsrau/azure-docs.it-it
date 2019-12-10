---
title: Definire un profilo tecnico di OpenID Connect in un criterio personalizzato
titleSuffix: Azure AD B2C
description: Definire un profilo tecnico di OpenID Connect in un criterio personalizzato in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/24/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: fdb925f820cad79fe68e7082f4ed63292a7d9444
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951089"
---
# <a name="define-an-openid-connect-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definire un profilo tecnico di OpenID Connect in un Azure Active Directory B2C criteri personalizzati

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) fornisce il supporto per il provider di identità del protocollo [OpenID Connect](https://openid.net/2015/04/17/openid-connect-certification-program/) . OpenID Connect 1.0 definisce un livello di identità su OAuth 2.0 e rappresenta i più avanzati protocolli di autenticazione moderni. Con un profilo tecnico di OpenID Connect, è possibile eseguire la Federazione con un provider di identità basato su OpenID Connect, ad esempio Azure AD. La Federazione con un provider di identità consente agli utenti di accedere con le identità aziendali o di social networking esistenti.

## <a name="protocol"></a>Protocol

L'attributo **Nome** dell'elemento **Protocollo** deve essere impostato su `OpenIdConnect`. Ad esempio, il protocollo per il profilo tecnico **MSA-OIDC** è `OpenIdConnect`:

```XML
<TechnicalProfile Id="MSA-OIDC">
  <DisplayName>Microsoft Account</DisplayName>
  <Protocol Name="OpenIdConnect" />
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

L'elemento **OutputClaims** contiene un elenco di attestazioni restituite dal provider di identità OpenID Connect. Può essere necessario eseguire il mapping del nome dell'attestazione definito nei criteri per il nome definito nel provider di identità. È anche possibile includere le attestazioni che non vengono restituite dal provider di identità, fino a quando è impostato l'attributo `DefaultValue`.

L'elemento **OutputClaimsTransformations** può contenere una raccolta di elementi **OutputClaimsTransformation** che vengono usati per modificare le attestazioni di output o per generarne di nuove.

L'esempio seguente illustra le attestazioni restituite dal provider di identità dell'account Microsoft:

- Attestazione **secondaria** di cui è stato eseguito il mapping all'attestazione **issuerUserId** .
- L'attestazione **nome** di cui viene eseguito il mapping per l'attestazione **displayName**.
- La **posta elettronica** senza il mapping del nome.

Il profilo tecnico restituisce anche le attestazioni che non vengono restituite dal provider di identità:

- L'attestazione **identityProvider** che contiene il nome del provider di identità.
- L'attestazione **authenticationSource** con un valore predefinito di **socialIdpAuthentication**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
</OutputClaims>
```

## <a name="metadata"></a>Metadata

| Attributo | Obbligatoria | Description |
| --------- | -------- | ----------- |
| client_id | SÌ | L'identificatore dell'applicazione del provider di identità. |
| IdTokenAudience | No | I destinatari dell'id_token. Se specificato, Azure AD B2C controlla se il token è in un'attestazione restituita dal provider di identità ed è uguale a quello specificato. |
| METADATI | SÌ | Un URL che punta a un documento di configurazione JSON formattato in base alla specifica di OpenID Connect Discovery, noto anche come un endpoint di configurazione openid. |
| ProviderName | No | Il nome del provider di identità. |
| response_types | No | Il tipo di risposta in base alla specifica di OpenID Connect Core 1.0. I valori possibili sono: `id_token`, `code` o `token`. |
| response_mode | No | Il metodo che usa il provider di identità per restituire il risultato ad Azure AD B2C. I valori possibili sono: `query`, `form_post` (impostazione predefinita), o `fragment`. |
| scope | No | Ambito della richiesta definito in base alla specifica di OpenID Connect Core 1,0. Ad esempio `openid`, `profile`, e `email`. |
| HttpBinding | No | L'associazione HTTP prevista per il token di accesso e per gli endpoint del token delle attestazioni. I possibili valori sono: `GET` o `POST`.  |
| ValidTokenIssuerPrefixes | No | Una chiave che può essere usata per accedere ai tenant quando si usa un provider di identità multi-tenant, ad esempio Azure Active Directory. |
| UsePolicyInRedirectUri | No | Indica se usare un criterio durante la costruzione dell'URI di reindirizzamento. Quando si configura l'applicazione nel provider di identità, è necessario specificare l'URI di reindirizzamento. L'URI di reindirizzamento punta a Azure AD B2C, `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp`.  Se si specifica `false`, è necessario aggiungere un URI di reindirizzamento per ogni criterio usato. Ad esempio: `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/{policy-name}/oauth2/authresp`. |
| MarkAsFailureOnStatusCode5xx | No | Indica se una richiesta a un servizio esterno deve essere contrassegnata come non riuscita se il codice di stato http è compreso nell'intervallo 5xx. Il valore predefinito è `false`. |
| DiscoverMetadataByTokenIssuer | No | Indica se i metadati OIDC devono essere individuati tramite l'autorità di certificazione nel token JWT. |

## <a name="cryptographic-keys"></a>Chiavi crittografiche

L'elemento **CryptographicKeys** contiene l'attributo seguente:

| Attributo | Obbligatoria | Description |
| --------- | -------- | ----------- |
| client_secret | SÌ | Il segreto client dell'applicazione del provider di identità. La chiave di crittografia è necessaria solo se i metadati **response_type** sono impostati su `code`. In questo caso, Azure AD B2C effettua un'altra chiamata per scambiare il codice di autorizzazione per un token di accesso. Se i metadati sono impostati su `id_token` è possibile omettere la chiave di crittografia.  |

## <a name="redirect-uri"></a>Uri di reindirizzamento

Quando si configura l'URI di reindirizzamento del provider di identità, immettere `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp`. Assicurarsi di sostituire `{your-tenant-name}` con il nome del tenant. L'URI di reindirizzamento deve essere composto da lettere minuscole.

Esempi:

- [Aggiungere un account Microsoft come provider di identità tramite i criteri personalizzati](active-directory-b2c-custom-setup-msa-idp.md)
- [Accedere usando gli account di Azure AD](active-directory-b2c-setup-aad-custom.md)
- [Consentire agli utenti di accedere a un provider di identità Azure AD multi-tenant tramite i criteri personalizzati](active-directory-b2c-setup-commonaad-custom.md)
