---
title: Come tenere traccia del comportamento degli utenti usando gli eventi di Application Insights da Azure AD B2C | Microsoft Docs
description: Guida dettagliata per abilitare i log eventi di Application Insights dai percorsi utente di Azure AD B2C usando criteri personalizzati - ANTEPRIMA
services: active-directory-b2c
documentationcenter: dev-center-name
author: davidmu1
manager: mtillman
ms.service: active-directory-b2c
ms.topic: article
ms.workload: identity
ms.date: 3/15/2018
ms.author: davidmu
ms.openlocfilehash: 1e8c4a53266072db71952ee35b15e5de54fabb95
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="tracking-user-behavior-inside-azure-ad-b2c-journeys-using-application-insights"></a>Come tenere traccia del comportamento degli utenti nei percorsi di Azure AD B2C usando Application Insights

Azure Active Directory B2C (Azure AD B2C) interagisce in modo efficace con Azure Application Insights  fornendo log eventi dettagliati e personalizzati per i percorsi utente definiti.  Questa guida illustra le attività iniziali da eseguire per: 
* Ottenere informazioni dettagliate sul comportamento degli utenti
* Risolvere i problemi relativi ai criteri in fase di sviluppo o nell'ambiente di produzione
* Misurare le prestazioni
* Creare notifiche da Application Insights

## <a name="how-it-works"></a>Funzionamento
In Framework dell'esperienza di gestione delle identità di Azure AD B2C è stato aggiunto un nuovo provider, `Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0`,  che invia i dati degli eventi direttamente ad Application Insights usando la chiave di strumentazione fornita da Azure AD B2C.  Un profilo tecnico usa questo provider per definire un evento da B2C.  Il profilo specifica il nome dell'evento, le attestazioni che verranno registrate e la chiave di strumentazione.  Per registrare un evento, il profilo tecnico viene quindi aggiunto come `orchestration step` o `validation technical profile` in un percorso utente personalizzato.  Gli eventi possono essere unificati da Application Insights usando un ID di correlazione per registrare una sessione utente.  Application Insights rende disponibili l'evento e la sessione entro pochi secondi e offre molti strumenti di visualizzazione, esportazione e analisi.



## <a name="prerequisites"></a>Prerequisiti
Completare la procedura descritta in [Introduzione ai criteri personalizzati](active-directory-b2c-get-started-custom.md).  Questa guida presuppone che venga usato il pacchetto Starter per i criteri personalizzati,  anche se il pacchetto non è obbligatorio.



## <a name="step-1-create-an-application-insights-resource-and-get-the-instrumentation-key"></a>Passaggio 1. Creare una risorsa di Application Insights e ottenere la chiave di strumentazione

Application Insights è uno strumento potente. Quando viene usato con Azure AD B2C, l'unico requisito è quello di creare una risorsa e ottenere una chiave di strumentazione.  La risorsa di Application Insights deve essere creata nel [portale di Azure](https://portal.azure.com).

[Documentazione completa di Application Insights](https://docs.microsoft.com/azure/application-insights/)

1. Fare clic su `+ Create a resource` nel portale di Azure, all'interno del tenant della propria sottoscrizione.  Questo non è il tenant di Azure AD B2C.  
2. Cercare e selezionare `Application Insights`.  
3. Creare una risorsa di `Application Type` `ASP.NET web application` in una sottoscrizione di preferenza.
4. Dopo aver creato la risorsa di Application Insights, aprirla e prendere nota del valore di `Instrumentation Key`. 

![Panoramica e chiave di strumentazione di Application Insights](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-key.png)

## <a name="step-2-add-new-claimtype-definitions-to-your-trust-framework-extension-file"></a>Passaggio 2. Aggiungere nuove definizioni ClaimType nel file delle estensioni del framework attendibilità

### <a name="open-the-extension-file-from-the-starter-pack-and-add-the-following-elements-to-the-buildingblocks-node--the-extensions-filename-is-typically-yourtenantonmicrosoftcom-b2c1atrustframeworkextensionsxml"></a>Aprire il file delle estensioni dal pacchetto Starter e aggiungere gli elementi seguenti nel nodo `<BuildingBlocks>`.  Il nome del file delle estensioni è in genere `yourtenant.onmicrosoft.com-B2C_1A_TrustFrameworkExtensions.xml`

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

I profili di tecnici possono essere considerati funzioni in Framework dell'esperienza di gestione delle identità di Azure AD B2C.  In questo esempio vengono definiti cinque profili tecnici per aprire una sessione e registrare gli eventi.

| Profilo tecnico       | Attività |
| ----------------------------- |:---------------------------------------------|
| AzureInsights-Common | Set comune di parametri da includere in tutti i profili tecnici Azure-Insights     | 
| JourneyContextForInsights   | Apre la sessione in Application Insights e invia un ID di correlazione |
| AzureInsights-SignInRequest     | Crea un evento "SignIn" con un set di attestazioni quando viene ricevuta una richiesta di accesso      | 
| AzureInsights-UserSignup | Crea un evento denominato "UserSignup" quando l'opzione di iscrizione è stata attivata dall'utente in un percorso di iscrizione/accesso     | 
| AzureInsights-SignInComplete | Registra il completamento di un'autenticazione quando un token è stato inviato all'applicazione relying party     | 

Aggiungere i profili al file delle estensioni dal pacchetto Starter inserendo questi elementi nel nodo `<ClaimsProviders>`.  Il nome del file delle estensioni è in genere `yourtenant.onmicrosoft.com-B2C_1A_TrustFrameworkExtensions.xml`

> [!IMPORTANT]
> Sostituire il valore di `Instrumentation Key` nel profilo tecnico `ApplicationInsights-Common` con il GUID fornito dalla risorsa di Application Insights.

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
            <!-- 
                            An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider
                            to create an event with the specified value.
                        -->
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
            <!-- 
                            A boolean indicating whether delevoper mode is enabled. This controls how events are buffered. In a development environment
                            with minimal event volume, enabling developer mode results in events being sent immediately to ApplicationInsights.
                        -->
            <Item Key="DeveloperMode">false</Item>
            <!-- 
                            A boolean indicating whether telemetry should be enabled or not.
                        -->
            <Item Key="DisableTelemetry ">false</Item>
          </Metadata>
          <InputClaims>
            <!--
                            Properties of an event are added using the syntax {property:NAME} where NAME is the name of the property being added
                            to the event. DefaultValue can be either a static value or one resolved by one of the supported DefaultClaimResolvers.
                        -->
            <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
            <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" />
            <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
          </InputClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

```

## <a name="step-4-add-the-technical-profiles-for-application-insights-as-orchestration-steps-in-an-existing-user-journey"></a>Passaggio 4. Aggiungere i profili di tecnici per Application Insights come passaggi di orchestrazione in un percorso utente esistente

Chiamare `JournyeContextForInsights` come primo passaggio di orchestrazione.

```xml
<!-- Initialize a session with Application Insights -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="JourneyContextForInsights" TechnicalProfileReferenceId="JourneyContextForInsights" />
          </ClaimsExchanges>
        </OrchestrationStep>
```

Chiamare `Azure-Insights-SignInRequest` come secondo passaggio di orchestrazione per tenere traccia della ricezione di una richiesta di accesso/iscrizione.

```xml
<!-- Track that we have received a sign in request -->
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AzureInsights-SignInRequest" />
          </ClaimsExchanges>
        </OrchestrationStep>
```

Immediatamente **prima** del passaggio di orchestrazione `SendClaims`, aggiungere un nuovo passaggio che chiama `Azure-Insights-UserSignup`. Viene attivato dopo che è stato fatto clic sul pulsante di iscrizione in percorso di iscrizione/accesso.

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

Immediatamente dopo il passaggio di orchestrazione `SendClaims`, chiamare `Azure-Insights-SignInComplete`.   Questo passaggio indicherà che il percorso è stato completato.

```xml
        <!-- Track that we have successfully sent a token -->
        <OrchestrationStep Order="11" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AzureInsights-SignInComplete" />
          </ClaimsExchanges>
        </OrchestrationStep>

```

> [!IMPORTANT]
> Dopo aver aggiunto nuovi passaggi di orchestrazione, rinumerare i passaggi in sequenza senza ignorare alcun numero intero, da 1 a N.


## <a name="step-5-upload-your-modified-extensions-file-run-the-policy-and-view-events-in-application-insights"></a>Passaggio 5. Caricare il file delle estensioni modificato, eseguire i criteri e visualizzare gli eventi in Application Insights

Salvare e caricare il nuovo file delle estensioni del framework attendibilità.  Chiamare quindi i criteri di relying party dall'applicazione o usare `Run Now` nell'interfaccia di Azure AD B2C.  Entro pochi secondi, gli eventi saranno disponibili in Application Insights.

1. Aprire la risorsa di Application Insights nel tenant di Azure Active Directory.
2. Fare clic su `Events` nel sottomenu `USAGE`.
3. Impostare `During` su `Last hour` e `By` su `3 minutes`.  Può essere necessario fare clic su `Refresh` per visualizzare i risultati.

![Pannello degli eventi di utilizzo in Application Insights](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-graphic.png)





##  <a name="next-steps"></a>PASSAGGI SUCCESSIVI

Aggiungere altri tipi di attestazione ed eventi al proprio percorso utente in base alle esigenze.  Ecco un elenco di possibili attestazioni ottenute usando altri resolver di attestazioni.

### <a name="culture-specific-claims"></a>Attestazioni specifiche delle impostazioni cultura

```xml
Culture-specific Claims
            Referenced using {Culture:One of the property names below}
 
        /// An enumeration of the claims supported by the <see cref="JourneyCultureDefaultClaimProcessor"/>
        public enum SupportedClaim
        {
             /// The two letter ISO code for the language i.e. en
            LanguageName
             
            /// The two letter ISO code for the region i.e. US
            RegionName,
 
            /// The RFC5646 language code i.e. en-US
            RFC5646,

            /// The LCID of language code i.e. 1033
            LCID
        }

```

### <a name="policy-specific-claims"></a>Attestazioni specifiche dei criteri

```xml
Policy-specific Claims
Referenced using {Policy:One of the property names below}
 
        /// An enumeration of the claims supported by the <see cref="PolicyDefaultClaimProcessor"/> 
        public enum SupportedClaim
        {
            /// The trustframework tenant id
            TrustFrameworkTenantId,
  
            /// The tenant id of the relying party
            RelyingPartyTenantId,
 
            /// The policy id of the policy
            PolicyId,
 
            /// The tenant object id of the policy
            TenantObjectId
}

```

### <a name="openidconnect-specific-claims"></a>Attestazioni specifiche di OpenIDConnect

```xml
OpenIDConnect Specific Claims
Referenced using {OIDC:One of the property names below}
 
/// 
        /// An enumeration of the claims supported by the <see cref="OpenIdConnectDefaultClaimProcessor"/>

        public enum SupportedClaim
        {
            /// The OpenIdConnect prompt parameter
            Prompt,
 
            /// The OpenIdConnect login_hint parameter
            LoginHint,

            /// The OpenIdConnect domain_hint parameter
            DomainHint,
 
             /// The OpenIdConnect max_age parameter
            MaxAge,
 
            /// The OpenIdConnect client_id parameter
            ClientId,
 
            /// The OpenIdConnect username parameter
            Username,

            /// The OpenIdConnect password parameter
            Password,
 
            /// The OpenIdConnect resource type parameter
            Resource,
 
            /// The OpendIdConext acr_values parameter
             AuthenticationContextReferences
        }
 
```

### <a name="non-protocol-parameters-included-with-oidc--oauth2-requests"></a>Parametri non di protocollo inclusi nelle richieste OIDC e OAuth2

```xml
Referenced using { OAUTH-KV:Querystring parameter name }
```

I nomi di parametro inclusi in una richiesta OIDC o OAuth2 possono essere mappati a un'attestazione nel percorso utente  e possono quindi essere registrati nell'evento. Ad esempio, la richiesta generata dall'applicazione può includere un parametro di stringa di query con il nome `app_session`, `loyalty_number` o `any_string`.

Esempio di richiesta generata dall'applicazione:
```
https://login.microsoftonline.com/sampletenant.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_signup_signin&client_id=e1d2612f-c2bc-4599-8e7b-d874eaca1ae1&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&app_session=0a2b45c&loyalty_number=1234567

```
Le attestazioni possono quindi essere aggiunte includendo un elemento di attestazione di input nell'evento di Application Insights nel modo seguente:
```
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="app_session" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="loyalty_number" DefaultValue="{OAUTH-KV:loyalty_number}" />
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



