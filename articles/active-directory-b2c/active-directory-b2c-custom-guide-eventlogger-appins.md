---
title: Tenere traccia del comportamento degli utenti usando gli eventi di Application Insights da Azure Active Directory B2C | Microsoft Docs
description: Guida dettagliata per abilitare i log eventi di Application Insights dai percorsi utente di Azure AD B2C usando criteri personalizzati (anteprima)
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 04/16/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 300a8a15552fe1e8ec9d6b434a14a31b3d827f19
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2018
ms.locfileid: "37445586"
---
# <a name="track-user-behavior-in-azure-ad-b2c-journeys-by-using-application-insights"></a>Tenere traccia del comportamento degli utenti nei percorsi di Azure AD B2C usando Application Insights

Azure Active Directory B2C (Azure AD B2C) interagisce in modo efficace con Azure Application Insights. Queste soluzioni forniscono log eventi dettagliati e personalizzati per i percorsi utente creati. Questo articolo illustra le attività iniziali da eseguire per:

* Ottenere informazioni dettagliate sul comportamento degli utenti.
* Risolvere i problemi relativi ai criteri in ambiente di sviluppo o di produzione.
* Misurare le prestazioni.
* Creare notifiche da Application Insights.

> [!NOTE]
> Questa funzionalità è in anteprima.

## <a name="how-it-works"></a>Funzionamento

Framework dell'esperienza di gestione delle identità in Azure AD B2C include ora il provider `Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0`.  Tale provider invia i dati degli eventi direttamente ad Application Insights usando la chiave di strumentazione fornita ad Azure AD B2C.

Un profilo tecnico usa questo provider per definire un evento da B2C.  Il profilo specifica il nome dell'evento, le attestazioni che verranno registrate e la chiave di strumentazione.  Per registrare un evento, il profilo tecnico viene quindi aggiunto come `orchestration step` o `validation technical profile` a un percorso utente personalizzato.

Application Insights può unificare gli eventi usando un ID di correlazione per registrare una sessione utente. Application Insights rende disponibili l'evento e la sessione entro pochi secondi e offre molti strumenti di visualizzazione, esportazione e analisi.

## <a name="prerequisites"></a>prerequisiti

Completare la procedura descritta in [Introduzione ai criteri personalizzati](active-directory-b2c-get-started-custom.md). Questo articolo presuppone che sia in uso lo starter pack per i criteri personalizzati, che tuttavia non è obbligatorio.

## <a name="step-1-create-an-application-insights-resource-and-get-the-instrumentation-key"></a>Passaggio 1. Creare una risorsa di Application Insights e ottenere la chiave di strumentazione

Quando si usa Application Insights con Azure AD B2C, l'unico requisito è quello di creare una risorsa e ottenere una chiave di strumentazione. È possibile creare una risorsa nel [portale di Azure](https://portal.azure.com)

1. Nel portale di Azure, nel tenant della sottoscrizione, selezionare **+ Crea una risorsa**. Questo non è il tenant di Azure AD B2C.  
2. Cercare e selezionare **Application Insights**.  
3. Creare una risorsa che usa **Applicazione Web ASP.NET** come **Tipo di applicazione** nella sottoscrizione preferita.
4. Dopo aver creato la risorsa di Application Insights, aprirla e prendere nota della chiave di strumentazione.

![Panoramica e chiave di strumentazione di Application Insights](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-key.png)

## <a name="step-2-add-new-claimtype-definitions-to-your-trust-framework-extension-file"></a>Passaggio 2. Aggiungere nuove definizioni ClaimType nel file delle estensioni del framework attendibilità

Aprire il file delle estensioni dal pacchetto Starter e aggiungere gli elementi seguenti nel nodo `<BuildingBlocks>`. Il nome del file è in genere `yourtenant.onmicrosoft.com-B2C_1A_TrustFrameworkExtensions.xml`

```xml
<ClaimsSchema>
  <ClaimType Id="EventType">
    <DisplayName>EventType</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="PolicyId">
    <DisplayName>PolicyId</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="Culture">
    <DisplayName>Culture</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="CorrelationId">
    <DisplayName>CorrelationId</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <!--Additional claims used for passing claims to Application Insights Provider -->
  <ClaimType Id="federatedUser">
    <DisplayName>federatedUser</DisplayName>
    <DataType>boolean</DataType>
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="parsedDomain">
    <DisplayName>Parsed Domain</DisplayName>
    <DataType>string</DataType>
    <UserHelpText>The domain portion of the email address.</UserHelpText>
  </ClaimType>
  <ClaimType Id="userInLocalDirectory">
    <DisplayName>userInLocalDirectory</DisplayName>
    <DataType>boolean</DataType>
    <UserHelpText />
  </ClaimType>
</ClaimsSchema>
```

## <a name="step-3-add-new-technical-profiles-that-use-the-application-insights-provider"></a>Passaggio 3. Aggiungere nuovi profili tecnici che usano il provider di Application Insights

I profili tecnici possono essere considerati funzioni in Framework dell'esperienza di gestione delle identità di Azure AD B2C. Questo esempio definisce cinque profili tecnici per aprire una sessione e registrare gli eventi:

| Profilo tecnico | Attività |
| ----------------- | -----|
| AzureInsights-Common | Crea un set di parametri comune da includere in tutti i profili tecnici AzureInsights | 
| JourneyContextForInsights | Apre la sessione in Application Insights e invia un ID di correlazione |
| AzureInsights-SignInRequest | Crea un evento `SignIn` con un set di attestazioni quando viene ricevuta una richiesta di accesso | 
| AzureInsights-UserSignup | Crea un evento UserSignup quando l'utente attiva l'opzione di registrazione in un percorso di registrazione/accesso | 
| AzureInsights-SignInComplete | Registra il completamento di un'autenticazione quando un token è stato inviato all'applicazione relying party | 

Aggiungere i profili al file delle estensioni dal pacchetto Starter inserendo questi elementi nel nodo `<ClaimsProviders>`.  Il nome del file è in genere `yourtenant.onmicrosoft.com-B2C_1A_TrustFrameworkExtensions.xml`

> [!IMPORTANT]
> Modificare la chiave di strumentazione nel profilo tecnico `ApplicationInsights-Common` con il GUID fornito dalla risorsa di Application Insights.

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="JourneyContextForInsights">
      <DisplayName>Application Insights</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="CorrelationId" />
      </OutputClaims>
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-SignInRequest">
      <InputClaims>
        <!-- An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider to create an event with the specified value. -->
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInRequest" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-SignInComplete">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInComplete" />
        <InputClaim ClaimTypeReferenceId="federatedUser" PartnerClaimType="{property:FederatedUser}" DefaultValue="false" />
        <InputClaim ClaimTypeReferenceId="parsedDomain" PartnerClaimType="{property:FederationPartner}" DefaultValue="Not Applicable" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-UserSignup">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="UserSignup" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-Common">
      <DisplayName>Alternate Email</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- The ApplicationInsights instrumentation key which will be used for logging the events -->
        <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
        <!-- A Boolean that indicates whether developer mode is enabled. This controls how events are buffered. In a development environment with minimal event volume, enabling developer mode results in events being sent immediately to ApplicationInsights. -->
        <Item Key="DeveloperMode">false</Item>
        <!-- A Boolean that indicates whether telemetry should be enabled or not. -->
        <Item Key="DisableTelemetry ">false</Item>
      </Metadata>
      <InputClaims>
        <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
        <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
        <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" />
        <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="step-4-add-the-technical-profiles-for-application-insights-as-orchestration-steps-in-an-existing-user-journey"></a>Passaggio 4. Aggiungere i profili tecnici per Application Insights come passaggi di orchestrazione in un percorso utente esistente

Chiamare `JournyeContextForInsights` come primo passaggio di orchestrazione:

```xml
<!-- Initialize a session with Application Insights -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="JourneyContextForInsights" TechnicalProfileReferenceId="JourneyContextForInsights" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Chiamare `Azure-Insights-SignInRequest` come secondo passaggio di orchestrazione per tenere traccia della ricezione di una richiesta di accesso/iscrizione:

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AzureInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Immediatamente *prima* del passaggio di orchestrazione `SendClaims`, aggiungere un nuovo passaggio che chiama `Azure-Insights-UserSignup`. L'attivazione avviene quando l'utente fa clic sul pulsante di iscrizione in un percorso di iscrizione/accesso.

```xml
<!-- Handles the user clicking the sign up link in the local account sign in page -->
<OrchestrationStep Order="9" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
      <Value>newUser</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
    <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
      <Value>newUser</Value>
      <Value>false</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackUserSignUp" TechnicalProfileReferenceId="AzureInsights-UserSignup" />
  </ClaimsExchanges>
```

Immediatamente dopo il passaggio di orchestrazione `SendClaims`, chiamare `Azure-Insights-SignInComplete`. Questo passaggio indica che il percorso è stato completato correttamente.

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="11" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AzureInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Dopo avere aggiunto i nuovi passaggi di orchestrazione, rinumerare i passaggi in sequenza senza ignorare alcun numero intero, da 1 a N.


## <a name="step-5-upload-your-modified-extensions-file-run-the-policy-and-view-events-in-application-insights"></a>Passaggio 5. Caricare il file delle estensioni modificato, eseguire i criteri e visualizzare gli eventi in Application Insights

Salvare e caricare il nuovo file delle estensioni del framework attendibilità. Chiamare quindi i criteri di relying party dall'applicazione o usare `Run Now` nell'interfaccia di Azure AD B2C. Entro pochi secondi, gli eventi saranno disponibili in Application Insights.

1. Aprire la risorsa di **Application Insights** nel tenant di Azure Active Directory.
2. Selezionare **Utilizzo** > **Eventi**.
3. Impostare **Durante** su **Ultima ora** e **Da** su **3 minuti**.  Potrebbe essere necessario selezionare **Aggiorna** per visualizzare i risultati.

![Pannello degli eventi di utilizzo in Application Insights](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-graphic.png)

##  <a name="next-steps"></a>Passaggi successivi

Aggiungere tipi di attestazioni ed eventi al proprio percorso utente in base alle esigenze. Ecco un elenco di possibili attestazioni, con resolver di attestazioni aggiuntivi

### <a name="culture-specific-claims"></a>Attestazioni specifiche delle impostazioni cultura

```xml
Referenced using: {Culture:One of the property names below}
```

| Attestazione | Definizione | Esempio |
| ----- | -----------| --------|
| LanguageName | Codice ISO di due lettere per la lingua | en |
| RegionName | Codice ISO di due lettere per la regione | Stati Uniti |
| RFC5646 | Codice RFC5646 della lingua | it-IT |
| LCID   | Identificatore LCID del codice della lingua | 1040 |

### <a name="policy-specific-claims"></a>Attestazioni specifiche dei criteri

```xml
Referenced using {Policy:One of the property names below}
```

| Attestazione | Definizione | Esempio |
| ----- | -----------| --------|
| TrustFrameworkTenantId | ID del tenant trustframework | N/D |
| RelyingPartyTenantId | ID del tenant della relying party | N/D |
| PolicyId | ID dei criteri | N/D |
| TenantObjectId | Oggetto tenant dei criteri | N/D |

### <a name="openid-connect-specific-claims"></a>Attestazioni specifiche di OpenID Connect

```xml
Referenced using {OIDC:One of the property names below}
```

| Attestazione | Parametro OpenIdConnect | Esempio |
| ----- | ----------------------- | --------|
| Prompt | prompt | N/D |
| LoginHint |  login_hint | N/D |
| DomainHint | domain_hint | N/D |
|  MaxAge | max_age | N/D |
| ClientId | client_id | N/D |
| Username | login_hint | N/D |
|  Risorsa | resource| N/D |
| AuthenticationContextReferences | acr_values | N/D |

### <a name="non-protocol-parameters-included-with-oidc--oauth2-requests"></a>Parametri non di protocollo inclusi nelle richieste OIDC e OAuth2

```xml
Referenced using { OAUTH-KV:Querystring parameter name }
```

I nomi di parametro inclusi in una richiesta OIDC o OAuth2 possono essere mappati a un'attestazione nel percorso utente È quindi possibile registrarli nell'evento. Ad esempio, la richiesta generata dall'applicazione può includere un parametro di stringa di query denominato `app_session`, `loyalty_number` o `any_string`.

Ecco un esempio di richiesta generata dall'applicazione:

```
https://login.microsoftonline.com/sampletenant.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_signup_signin&client_id=e1d2612f-c2bc-4599-8e7b-d874eaca1ae1&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&app_session=0a2b45c&loyalty_number=1234567

```
È quindi possibile aggiungere le attestazioni aggiungendo un elemento `Input Claim` nell'evento di Application Insights. Le proprietà di un evento vengono aggiunte tramite la sintassi {property:NOME}, dove NOME è la proprietà da aggiungere all'evento. Ad esempio: 

```
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="{property:app_session}" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="{property:loyalty_number}" DefaultValue="{OAUTH-KV:loyalty_number}" />
```

### <a name="other-system-claims"></a>Altre attestazioni di sistema

Per essere disponibili per la registrazione come eventi, alcune attestazioni di sistema devono essere aggiunte all'elenco delle attestazioni. Prima che queste attestazioni siano disponibili, è necessario chiamare il profilo tecnico `SimpleUJContext` come passaggio di orchestrazione o profilo tecnico di convalida.

```xml
<ClaimsProvider>
  <DisplayName>User Journey Context Provider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SimpleUJContext">
      <DisplayName>User Journey Context Provide</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="IP-Address" />
        <OutputClaim ClaimTypeReferenceId="CorrelationId" />
        <OutputClaim ClaimTypeReferenceId="DateTimeInUtc" />
        <OutputClaim ClaimTypeReferenceId="Build" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```


