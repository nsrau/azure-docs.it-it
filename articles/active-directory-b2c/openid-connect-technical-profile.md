---
title: Definire un profilo tecnico di OpenID Connect in un criterio personalizzato
titleSuffix: Azure AD B2C
description: Definire un profilo tecnico di OpenID Connect in un criterio personalizzato in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/03/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f06ae55dc48152c2c10183cc60cb098b6c3786fa
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2020
ms.locfileid: "89433756"
---
# <a name="define-an-openid-connect-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definire un profilo tecnico di OpenID Connect in un Azure Active Directory B2C criteri personalizzati

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) fornisce il supporto per il provider di identità del protocollo [OpenID Connect](https://openid.net/2015/04/17/openid-connect-certification-program/) . OpenID Connect 1.0 definisce un livello di identità su OAuth 2.0 e rappresenta i più avanzati protocolli di autenticazione moderni. Con un profilo tecnico di OpenID Connect, è possibile eseguire la Federazione con un provider di identità basato su OpenID Connect, ad esempio Azure AD. La Federazione con un provider di identità consente agli utenti di accedere con le identità aziendali o di social networking esistenti.

## <a name="protocol"></a>Protocollo

L'attributo **Nome** dell'elemento **Protocollo** deve essere impostato su `OpenIdConnect`. Ad esempio, il protocollo per il profilo tecnico **MSA-OIDC** è `OpenIdConnect`:

```xml
<TechnicalProfile Id="MSA-OIDC">
  <DisplayName>Microsoft Account</DisplayName>
  <Protocol Name="OpenIdConnect" />
  ...
```

## <a name="input-claims"></a>Attestazioni di input

Gli elementi **InputClaims** e **InputClaimsTransformations** non sono obbligatori. Tuttavia, è possibile inviare parametri aggiuntivi al provider di identità. L'esempio seguente aggiunge il parametro della stringa di query **domain_hint** e il valore di `contoso.com` alla richiesta di autorizzazione.

```xml
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

## <a name="metadata"></a>Metadati

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| client_id | Sì | L'identificatore dell'attestazione del provider di identità. |
| IdTokenAudience | No | I destinatari dell'id_token. Se specificato, Azure AD B2C controlla se l' `aud` attestazione in un token restituito dal provider di identità è uguale a quella specificata nei metadati IdTokenAudience.  |
| METADATI | Sì | Un URL che punta a un documento di configurazione del provider di identità OpenID Connect, noto anche come endpoint di configurazione OpenID noto. L'URL può contenere l' `{tenant}` espressione, che viene sostituita con il nome del tenant.  |
| authorization_endpoint | No | URL che punta a un endpoint di autorizzazione della configurazione del provider di identità OpenID Connect. Il valore di authorization_endpoint metadati ha la precedenza su quello `authorization_endpoint` specificato nell'endpoint di configurazione OpenID noto. L'URL può contenere l' `{tenant}` espressione, che viene sostituita con il nome del tenant. |
| autorità di certificazione | No | Identificatore univoco di un provider di identità OpenID Connect. Il valore dei metadati dell'autorità emittente ha la precedenza su quello `issuer` specificato nell'endpoint di configurazione OpenID noto.  Se specificato, Azure AD B2C controlla se l' `iss` attestazione in un token restituito dal provider di identità è uguale a quella specificata nei metadati dell'emittente. |
| ProviderName | No | Il nome del provider di identità.  |
| response_types | No | Il tipo di risposta in base alla specifica di OpenID Connect Core 1.0. I valori possibili sono: `id_token`, `code` o `token`. |
| response_mode | No | Il metodo che usa il provider di identità per restituire il risultato ad Azure AD B2C. I valori possibili sono: `query`, `form_post` (impostazione predefinita), o `fragment`. |
| ambito | No | Ambito della richiesta definito in base alla specifica di OpenID Connect Core 1,0. Ad esempio `openid`, `profile`, e `email`. |
| HttpBinding | No | L'associazione HTTP prevista per il token di accesso e per gli endpoint del token delle attestazioni. I valori possibili sono: `GET` o `POST`.  |
| ValidTokenIssuerPrefixes | No | Una chiave che può essere usata per accedere ai tenant quando si usa un provider di identità multi-tenant, ad esempio Azure Active Directory. |
| UsePolicyInRedirectUri | No | Indica se usare un criterio durante la costruzione dell'URI di reindirizzamento. Quando si configura l'applicazione nel provider di identità, è necessario specificare l'URI di reindirizzamento. L'URI di reindirizzamento punta a Azure AD B2C, `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp` .  Se si specifica `false`, è necessario aggiungere un URI di reindirizzamento per ogni criterio usato. Ad esempio: `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/{policy-name}/oauth2/authresp`. |
| MarkAsFailureOnStatusCode5xx | No | Indica se una richiesta a un servizio esterno deve essere contrassegnata come non riuscita se il codice di stato http è compreso nell'intervallo 5xx. Il valore predefinito è `false`. |
| DiscoverMetadataByTokenIssuer | No | Indica se i metadati OIDC devono essere individuati tramite l'autorità di certificazione nel token JWT. |
| IncludeClaimResolvingInClaimsHandling  | No | Per le attestazioni di input e output, specifica se la [risoluzione delle attestazioni](claim-resolver-overview.md) è inclusa nel profilo tecnico. Valori possibili: `true` , o `false`   (impostazione predefinita). Se si desidera utilizzare un resolver di attestazioni nel profilo tecnico, impostare questa impostazione su `true` . |
|token_endpoint_auth_method| No| Specifica il modo in cui Azure AD B2C invia l'intestazione di autenticazione all'endpoint del token. Valori possibili: `client_secret_post` (impostazione predefinita) e `client_secret_basic` (anteprima pubblica). Per altre informazioni, vedere la [sezione autenticazione client OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication). |


```xml
<Metadata>
  <Item Key="ProviderName">https://login.live.com</Item>
  <Item Key="METADATA">https://login.live.com/.well-known/openid-configuration</Item>
  <Item Key="response_types">code</Item>
  <Item Key="response_mode">form_post</Item>
  <Item Key="scope">openid profile email</Item>
  <Item Key="HttpBinding">POST</Item>
  <Item Key="UsePolicyInRedirectUri">0</Item>
  <Item Key="client_id">Your Microsoft application client ID</Item>
</Metadata>
```

### <a name="ui-elements"></a>Elementi dell'interfaccia utente
 
Per configurare il messaggio di errore visualizzato in caso di errore, è possibile utilizzare le impostazioni seguenti. I metadati devono essere configurati nel profilo tecnico di OpenID Connect. I messaggi di errore possono essere [localizzati](localization-string-ids.md#sign-up-or-sign-in-error-messages).

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| UserMessageIfClaimsPrincipalDoesNotExist | No | Messaggio da visualizzare all'utente se non è stato trovato un account con il nome utente specificato nella directory. |
| UserMessageIfInvalidPassword | No | Messaggio da visualizzare all'utente se la password non è corretta. |
| UserMessageIfOldPasswordUsed| No |  Messaggio da visualizzare all'utente se viene utilizzata una vecchia password.|

## <a name="cryptographic-keys"></a>Chiavi di crittografia

L'elemento **CryptographicKeys** contiene l'attributo seguente:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| client_secret | Sì | Il segreto client dell'applicazione del provider di identità. La chiave di crittografia è necessaria solo se i metadati **response_type** sono impostati su `code`. In questo caso, Azure AD B2C effettua un'altra chiamata per scambiare il codice di autorizzazione per un token di accesso. Se i metadati sono impostati su `id_token` è possibile omettere la chiave di crittografia.  |

## <a name="redirect-uri"></a>Uri di reindirizzamento

Quando si configura l'URI di reindirizzamento del provider di identità, immettere `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp`. Assicurarsi di sostituire `{your-tenant-name}` con il nome del tenant. L'URI di reindirizzamento deve essere tutto minuscolo.

Esempi:

- [Aggiungere un account Microsoft come provider di identità tramite i criteri personalizzati](identity-provider-microsoft-account-custom.md)
- [Accedere usando gli account di Azure AD](identity-provider-azure-ad-single-tenant-custom.md)
- [Consentire agli utenti di accedere a un provider di identità Azure AD multi-tenant tramite i criteri personalizzati](identity-provider-azure-ad-multi-tenant-custom.md)
