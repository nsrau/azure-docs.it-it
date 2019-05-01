---
title: Tenere traccia del comportamento degli utenti usando gli eventi di Application Insights da Azure Active Directory B2C | Microsoft Docs
description: Informazioni su come abilitare i log eventi di Application Insights dai percorsi utente di Azure AD B2C usando criteri personalizzati (anteprima).
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 10/12/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 8284be21db01ed7bd3215f7a67c8bfb40e0d73de
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64705144"
---
# <a name="track-user-behavior-in-azure-active-directory-b2c-using-application-insights"></a>Tenere traccia del comportamento degli utenti in Azure Active Directory B2C usando Application Insights

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Quando si usa Azure Active Directory (Azure AD) B2C con Azure Application Insights, è possibile ottenere log eventi dettagliati e personalizzati per i percorsi utente. In questo articolo viene spiegato come:

* Ottenere informazioni dettagliate sul comportamento degli utenti.
* Risolvere i problemi relativi ai criteri in ambiente di sviluppo o di produzione.
* Misurare le prestazioni.
* Creare notifiche da Application Insights.

## <a name="how-it-works"></a>Funzionamento

Il framework dell'esperienza di gestione delle identità in Azure AD B2C include il provider `Handler="Web.TPEngine.Providers.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0`. Tale provider invia i dati degli eventi direttamente ad Application Insights usando la chiave di strumentazione fornita ad Azure AD B2C.

Un profilo tecnico usa questo provider per definire un evento da Azure AD B2C. Il profilo specifica il nome dell'evento, le attestazioni che verranno registrate e la chiave di strumentazione. Per registrare un evento, il profilo tecnico viene quindi aggiunto come `orchestration step` o `validation technical profile` a un percorso utente personalizzato.

Application Insights può unificare gli eventi usando un ID di correlazione per registrare una sessione utente. Application Insights rende disponibili l'evento e la sessione entro pochi secondi e offre molti strumenti di visualizzazione, esportazione e analisi.

## <a name="prerequisites"></a>Prerequisiti

Completare la procedura descritta in [Introduzione ai criteri personalizzati](active-directory-b2c-get-started-custom.md). Questo articolo presuppone che sia in uso lo starter pack per i criteri personalizzati, che tuttavia non è obbligatorio.

## <a name="create-an-application-insights-resource"></a>Creare una risorsa di Application Insights

Quando si usa Application Insights con Azure AD B2C, è sufficiente creare una risorsa e ottenere la chiave di strumentazione.

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Assicurarsi di usare la directory che contiene la sottoscrizione di Azure. A tale scopo, fare clic sul **filtro delle directory e delle sottoscrizioni** nel menu in alto e scegliere la directory che contiene la sottoscrizione. Questo non è il tenant di Azure AD B2C.
3. Scegliere **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure e quindi cercare e selezionare **Application Insights**.
4. Fare clic su **Create**(Crea).
5. Immettere un **nome** per la risorsa.
6. In **Tipo di applicazione** selezionare **Applicazione Web ASP.NET**.
7. In **Gruppo di risorse** selezionare un gruppo esistente o immettere un nome per un nuovo gruppo.
8. Fare clic su **Create**(Crea).
4. Dopo aver creato la risorsa di Application Insights, espandere **Informazioni di base** e prendere nota della chiave di strumentazione.

![Panoramica e chiave di strumentazione di Application Insights](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-insights.png)

## <a name="add-new-claimtype-definitions"></a>Aggiungere nuove definizioni ClaimType

Aprire il file *TrustFrameworkExtensions.xml* dallo starter pack e aggiungere gli elementi seguenti all'elemento [BuildingBlocks](buildingblocks.md):

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

## <a name="add-new-technical-profiles"></a>Aggiungere nuovi profili tecnici

I profili tecnici possono essere considerati funzioni in Framework dell'esperienza di gestione delle identità di Azure AD B2C. Questa tabella definisce i profili tecnici usati per aprire una sessione e pubblicare gli eventi.

| Profilo tecnico | Attività |
| ----------------- | -----|
| AzureInsights-Common | Crea un set di parametri comune da includere in tutti i profili tecnici AzureInsights. | 
| AzureInsights-SignInRequest | Crea un evento SignIn con un set di attestazioni quando viene ricevuta una richiesta di accesso. | 
| AzureInsights-UserSignup | Crea un evento UserSignup quando l'utente attiva l'opzione di registrazione in un percorso di registrazione/accesso. | 
| AzureInsights-SignInComplete | Registra il completamento di un'autenticazione quando un token è stato inviato all'applicazione relying party. | 

Aggiungere i profili al file *TrustFrameworkExtensions.xml* dallo starter pack. Aggiungere questi elementi all'elemento **ClaimsProviders**:

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
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

> [!IMPORTANT]
> Modificare la chiave di strumentazione nel profilo tecnico `ApplicationInsights-Common` con il GUID fornito dalla risorsa di Application Insights.

## <a name="add-the-technical-profiles-as-orchestration-steps"></a>Aggiungere i profili tecnici come passaggi di orchestrazione

Chiamare `Azure-Insights-SignInRequest` come secondo passaggio di orchestrazione per tenere traccia della ricezione di una richiesta di accesso/iscrizione:

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AzureInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Immediatamente *prima* del passaggio di orchestrazione `SendClaims`, aggiungere un nuovo passaggio che chiama `Azure-Insights-UserSignup`. L'attivazione avviene quando l'utente fa clic sul pulsante di iscrizione in un percorso di iscrizione/accesso.

```xml
<!-- Handles the user clicking the sign up link in the local account sign in page -->
<OrchestrationStep Order="8" Type="ClaimsExchange">
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
</OrchestrationStep>
```

Immediatamente dopo il passaggio di orchestrazione `SendClaims`, chiamare `Azure-Insights-SignInComplete`. Questo passaggio mostra un percorso completato correttamente.

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="10" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AzureInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Dopo avere aggiunto i nuovi passaggi di orchestrazione, rinumerare i passaggi in sequenza senza ignorare alcun numero intero, da 1 a N.


## <a name="upload-your-file-run-the-policy-and-view-events"></a>Caricare il file, eseguire i criteri e visualizzare gli eventi

Salvare e caricare il file *TrustFrameworkExtensions.xml*. Chiamare quindi i criteri della relying party dall'applicazione o usare **Esegui adesso** nel portale di Azure. Entro pochi secondi, gli eventi saranno disponibili in Application Insights.

1. Aprire la risorsa di **Application Insights** nel tenant di Azure Active Directory.
2. Selezionare **Utilizzo** > **Eventi**.
3. Impostare **Durante** su **Ultima ora** e **Da** su **3 minuti**.  Potrebbe essere necessario selezionare **Aggiorna** per visualizzare i risultati.

![Pannello degli eventi di utilizzo in Application Insights](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-graphic.png)

## <a name="next-steps"></a>Passaggi successivi

Aggiungere tipi di attestazioni ed eventi al proprio percorso utente in base alle esigenze. È possibile usare [resolver di attestazioni](claim-resolver-overview.md) o qualsiasi tipo di attestazione stringa, aggiungere attestazioni aggiungendo un elemento **Input Claim** (Attestazione input) all'evento di Application Insights o al profilo tecnico AzureInsights-Common. 

- **ClaimTypeReferenceId** è il riferimento a un tipo di attestazione.
- **PartnerClaimType** è il nome della proprietà visualizzato in Azure Insights. Usare la sintassi `{property:NAME}`, dove `NAME` è la proprietà da aggiungere all'evento. 
- Per **DefaultValue** usare qualsiasi valore stringa o il resolver di attestazioni. 

```XML
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="{property:app_session}" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="{property:loyalty_number}" DefaultValue="{OAUTH-KV:loyalty_number}" />
<InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
```

