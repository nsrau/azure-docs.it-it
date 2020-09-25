---
title: Resolver di attestazioni nei criteri personalizzati
titleSuffix: Azure AD B2C
description: Informazioni su come usare i resolver di attestazioni in un criterio personalizzato in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/21/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 466e590ba22efe1c2fbb457c15bc7f979f8a172e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91259637"
---
# <a name="about-claim-resolvers-in-azure-active-directory-b2c-custom-policies"></a>Informazioni sui resolver di attestazioni nei criteri personalizzati in Azure Active Directory B2C

I resolver di attestazioni nei [criteri personalizzati](custom-policy-overview.md) Azure Active Directory B2C (Azure ad B2C) forniscono informazioni sul contesto di una richiesta di autorizzazione, ad esempio il nome del criterio, l'ID di correlazione della richiesta, la lingua dell'interfaccia utente e altro ancora.

Per usare un resolver di attestazioni in un'attestazione di input o output, si definisce un **ClaimType** di tipo stringa, nell'elemento [ClaimsSchema](claimsschema.md), quindi si imposta **DefaultValue** sul resolver di attestazioni nell'elemento attestazione di input o output. Azure AD B2C legge il valore del resolver di attestazioni e usa il valore nel profilo tecnico.

Nell'esempio seguente viene definito un tipo di attestazione denominato `correlationId` con **DataType**`string`.

```xml
<ClaimType Id="correlationId">
  <DisplayName>correlationId</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Request correlation Id</UserHelpText>
</ClaimType>
```

Nel profilo tecnico, mappare il resolver di attestazioni al tipo di attestazione. Azure AD B2C popola il valore del resolver di attestazioni `{Context:CorrelationId}` nell'attestazione `correlationId` e invia l'attestazione al profilo tecnico.

```xml
<InputClaim ClaimTypeReferenceId="correlationId" DefaultValue="{Context:CorrelationId}" />
```

## <a name="claim-resolver-types"></a>Tipi di resolver di attestazioni

Le sezioni seguenti elencano i resolver di attestazioni disponibili.

### <a name="culture"></a>Impostazioni cultura

| Attestazione | Descrizione | Esempio |
| ----- | ----------- | --------|
| {Culture:LanguageName} | Codice ISO di due lettere per la lingua. | en |
| {Culture:LCID}   | Identificatore LCID del codice della lingua. | 1033 |
| {Culture:RegionName} | Codice ISO di due lettere per la regione. | US |
| {Culture:RFC5646} | Codice RFC5646 della lingua. | it-IT |

### <a name="policy"></a>Policy

| Attestazione | Descrizione | Esempio |
| ----- | ----------- | --------|
| {Policy:PolicyId} | Nome dei criteri della relying party. | B2C_1A_signup_signin |
| {Policy:RelyingPartyTenantId} | ID del tenant dei criteri della relying party. | your-tenant.onmicrosoft.com |
| {Policy:TenantObjectId} | ID oggetto del tenant dei criteri della relying party. | 00000000-0000-0000-0000-000000000000 |
| {Policy:TrustFrameworkTenantId} | ID del tenant del framework attendibilità. | your-tenant.onmicrosoft.com |

### <a name="openid-connect"></a>OpenID Connect

| Attestazione | Descrizione | Esempio |
| ----- | ----------- | --------|
| {OIDC:AuthenticationContextReferences} |Parametro di stringa di query `acr_values`. | N/D |
| {OIDC:ClientId} |Parametro di stringa di query `client_id`. | 00000000-0000-0000-0000-000000000000 |
| {OIDC:DomainHint} |Parametro di stringa di query `domain_hint`. | facebook.com |
| {OIDC:LoginHint} |  Parametro di stringa di query `login_hint`. | someone@contoso.com |
| {OIDC:MaxAge} | Oggetto `max_age`. | N/D |
| {OIDC:Nonce} |Parametro di stringa di query `Nonce`. | defaultNonce |
| {OIDC: password}| La password dell'utente del [flusso di credenziali password del proprietario della risorsa](ropc-custom.md) .| Password1| 
| {OIDC:Prompt} | Parametro di stringa di query `prompt`. | login |
| {OIDC: RedirectUri} |Parametro di stringa di query `redirect_uri`. | https://jwt.ms |
| {OIDC:Resource} |Parametro di stringa di query `resource`. | N/D |
| {OIDC: ambito} |Parametro di stringa di query `scope`. | openid |
| {OIDC: nomeutente}| Il nome utente del [flusso di credenziali della password del proprietario della risorsa](ropc-custom.md) .| emily@contoso.com| 

### <a name="context"></a>Context

| Attestazione | Descrizione | Esempio |
| ----- | ----------- | --------|
| {Context:BuildNumber} | Versione del framework dell'esperienza di gestione delle identità (numero di build).  | 1.0.507.0 |
| {Context:CorrelationId} | L'ID di correlazione.  | 00000000-0000-0000-0000-000000000000 |
| {Context:DateTimeInUtc} |Data e ora in formato UTC.  | 10/10/2018 12:00:00 PM |
| {Context:DeploymentMode} |Modalità di distribuzione dei criteri.  | Produzione |
| {Context:IPAddress} | Indirizzo IP utente. | 11.111.111.11 |
| {Context: KMSI} | Indica se la casella [di controllo Mantieni l'accesso](custom-policy-keep-me-signed-in.md) è selezionata. |  true |

### <a name="claims"></a>Attestazioni 

| Attestazione | Descrizione | Esempio |
| ----- | ----------- | --------|
| {Claim: tipo di attestazione} | Identificatore di un tipo di attestazione già definito nella sezione ClaimsSchema del file di criteri o del file di criteri padre.  Ad esempio: `{Claim:displayName}` o `{Claim:objectId}` . | Valore del tipo di attestazione.|


### <a name="oauth2-key-value-parameters"></a>Parametri chiave-valore OAuth2

I nomi di parametro inclusi in una richiesta OIDC o OAuth2 possono essere mappati a un'attestazione nel percorso utente Ad esempio, la richiesta generata dall'applicazione può includere un parametro di stringa di query denominato `app_session`, `loyalty_number` o qualsiasi stringa di query personalizzata.

| Attestazione | Descrizione | Esempio |
| ----- | ----------------------- | --------|
| {OAUTH-KV:campaignId} | Parametro di stringa di query. | Hawaii |
| {OAUTH-KV:app_session} | Parametro di stringa di query. | A3C5R |
| {OAUTH-KV:loyalty_number} | Parametro di stringa di query. | 1234 |
| {OAUTH-KV:qualsiasi stringa di query personalizzata} | Parametro di stringa di query. | N/D |

### <a name="oauth2"></a>OAuth2

| Attestazione | Descrizione | Esempio |
| ----- | ----------------------- | --------|
| {oauth2:access_token} | Token di accesso. | N/D |
| {OAuth2: refresh_token} | Token di aggiornamento. | N/D |


### <a name="saml"></a>SAML

| Attestazione | Descrizione | Esempio |
| ----- | ----------- | --------|
| {SAML: AuthnContextClassReferences} | `AuthnContextClassRef`Valore dell'elemento, dalla richiesta SAML. | urn: Oasis: Names: TC: SAML: 2.0: AC: Classes: PasswordProtectedTransport |
| {SAML: NameIdPolicyFormat} | `Format`Attributo, dall' `NameIDPolicy` elemento della richiesta SAML. | urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress |
| {SAML: emittente} |  Valore dell' `Issuer` elemento SAML della richiesta SAML.| `https://contoso.com` |
| {SAML: AllowCreate} | `AllowCreate`Valore dell'attributo, dall' `NameIDPolicy` elemento della richiesta SAML. | Vero |
| {SAML: ForceAuthn} | `ForceAuthN`Valore dell'attributo, dall' `AuthnRequest` elemento della richiesta SAML. | Vero |
| {SAML: ProviderName} | `ProviderName`Valore dell'attributo, dall' `AuthnRequest` elemento della richiesta SAML.| Contoso.com |
| {SAML: RelayState} | Parametro di stringa di query `RelayState`.| 

## <a name="using-claim-resolvers"></a>Uso di resolver di attestazioni

È possibile usare i resolver di attestazioni con gli elementi seguenti:

| Elemento | Elemento | Impostazioni |
| ----- | ----------------------- | --------|
|Profilo tecnico di Application Insights |`InputClaim` | |
|[Azure Active Directory](active-directory-technical-profile.md) profilo tecnico| `InputClaim`, `OutputClaim`| 1, 2|
|Profilo tecnico [OAuth2](oauth2-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|Profilo tecnico di [OpenID Connect](openid-connect-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|Profilo tecnico per la [trasformazione delle attestazioni](claims-transformation-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|Profilo tecnico del [provider RESTful](restful-technical-profile.md)| `InputClaim`| 1, 2|
|Profilo tecnico del [provider di identità SAML](saml-identity-provider-technical-profile.md)| `OutputClaim`| 1, 2|
|Profilo tecnico [autocertificato](self-asserted-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|[ContentDefinition](contentdefinitions.md)| `LoadUri`| |
|[ContentDefinitionParameters](relyingparty.md#contentdefinitionparameters)| `Parameter` | |
|Profilo tecnico [RelyingParty](relyingparty.md#technicalprofile)| `OutputClaim`| 2 |

Impostazioni:
1. I `IncludeClaimResolvingInClaimsHandling` metadati devono essere impostati su `true` .
1. L'attributo Claims di input o output `AlwaysUseDefaultValue` deve essere impostato su `true` .

## <a name="claim-resolvers-samples"></a>Esempi di resolver di attestazioni

### <a name="restful-technical-profile"></a>Profilo tecnico RESTful

In un profilo tecnico [RESTful](restful-technical-profile.md) può essere utile inviare la lingua dell'utente, il nome dei criteri, l'ambito e l'ID client. In base alle attestazioni, l'API REST può eseguire la logica di business personalizzata e, se necessario, generare un messaggio di errore localizzato.

Nell'esempio seguente viene illustrato un profilo tecnico RESTful con questo scenario:

```xml
<TechnicalProfile Id="REST">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="policyName" DefaultValue="{Policy:PolicyId}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="{OIDC:Scope}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="clientId" DefaultValue="{OIDC:ClientId}" AlwaysUseDefaultValue="true" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="direct-sign-in"></a>Accesso diretto

Con i resolver di attestazioni è possibile precompilare il nome di accesso o l'accesso diretto a un provider di identità di social networking specifico, ad esempio Facebook, LinkedIn o un account Microsoft. Per altre informazioni, vedere [Configurare l'accesso diretto tramite Active Directory B2C](direct-signin.md).

### <a name="dynamic-ui-customization"></a>Personalizzazione dell'interfaccia utente dinamica

Azure AD B2C consente di passare i parametri della stringa di query agli endpoint della definizione del contenuto HTML per eseguire dinamicamente il rendering del contenuto della pagina. Questa funzionalità consente, ad esempio, di modificare l'immagine di sfondo nella pagina Azure AD B2C iscrizione o accesso in base a un parametro personalizzato passato dall'applicazione Web o per dispositivi mobili. Per altre informazioni, vedere [Azure Active Directory B2C: Configurare l'interfaccia utente con contenuto dinamico usando criteri personalizzati](custom-policy-ui-customization.md#configure-dynamic-custom-page-content-uri). È anche possibile localizzare la pagina HTML in base a un parametro di lingua oppure è possibile modificare il contenuto in base all'ID client.

Nell'esempio seguente viene passato il parametro della stringa di query denominato **campaignId** con il valore `Hawaii` , il codice della **lingua** `en-US` e l' **app** che rappresenta l'ID client:

```xml
<UserJourneyBehaviors>
  <ContentDefinitionParameters>
    <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
    <Parameter Name="language">{Culture:RFC5646}</Parameter>
    <Parameter Name="app">{OIDC:ClientId}</Parameter>
  </ContentDefinitionParameters>
</UserJourneyBehaviors>
```

Di conseguenza, Azure AD B2C invia i parametri precedenti alla pagina contenuto HTML:

```
/selfAsserted.aspx?campaignId=hawaii&language=en-US&app=0239a9cc-309c-4d41-87f1-31288feb2e82
```

### <a name="content-definition"></a>Definizione del contenuto

In un [ContentDefinition](contentdefinitions.md) `LoadUri` è possibile inviare i resolver di attestazioni per il pull del contenuto da posizioni diverse in base ai parametri utilizzati.

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

### <a name="application-insights-technical-profile"></a>Profilo tecnico di Application Insights

Con Azure Application Insights e i resolver di attestazioni è possibile ottenere informazioni dettagliate sul comportamento degli utenti. Nel profilo tecnico di Application Insights, si inviano le attestazioni di input che vengono salvate in Azure Application Insights. Per altre informazioni, vedere [Tenere traccia del comportamento degli utenti nei percorsi di Azure AD B2C usando Application Insights](analytics-with-application-insights.md). L'esempio seguente invia l'ID dei criteri, l'ID di correlazione, la lingua e l'ID client ad Azure Application Insights.

```xml
<TechnicalProfile Id="AzureInsights-Common">
  <DisplayName>Alternate Email</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
    <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:CorrelationId}" DefaultValue="{Context:CorrelationId}" />
    <InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
    <InputClaim ClaimTypeReferenceId="AppId" PartnerClaimType="{property:App}" DefaultValue="{OIDC:ClientId}" />
  </InputClaims>
</TechnicalProfile>
```

### <a name="relying-party-policy"></a>Criteri della relying party

In un profilo tecnico dei criteri della [relying party](relyingparty.md) , è possibile inviare l'ID tenant o l'ID di correlazione all'applicazione relying party all'interno di JWT.

```xml
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
        <OutputClaim ClaimTypeReferenceId="correlationId" AlwaysUseDefaultValue="true" DefaultValue="{Context:CorrelationId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
```
