---
title: Tenere traccia del comportamento degli utenti con Application Insights
titleSuffix: Azure AD B2C
description: Informazioni su come abilitare i registri eventi in Application Insights da Azure AD B2C percorsi utente usando criteri personalizzati.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 04/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 67ea7324419d86fa5b5c23a2f0aa5f8c057495d1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85385978"
---
# <a name="track-user-behavior-in-azure-active-directory-b2c-using-application-insights"></a>Tenere traccia del comportamento degli utenti in Azure Active Directory B2C usando Application Insights

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure Active Directory B2C (Azure AD B2C) supporta l'invio diretto di dati di evento a [Application Insights](../azure-monitor/app/app-insights-overview.md) usando la chiave di strumentazione fornita per Azure ad B2C.  Con un profilo tecnico Application Insights, è possibile ottenere registri eventi dettagliati e personalizzati per i percorsi utente per:

* Ottenere informazioni dettagliate sul comportamento degli utenti.
* Risolvere i problemi relativi ai criteri in ambiente di sviluppo o di produzione.
* Misurare le prestazioni.
* Creare notifiche da Application Insights.

## <a name="how-it-works"></a>Funzionamento

Il profilo tecnico [Application Insights](application-insights-technical-profile.md) definisce un evento da Azure ad B2C. Il profilo specifica il nome dell'evento, le attestazioni che verranno registrate e la chiave di strumentazione. Per pubblicare un evento, il profilo tecnico viene quindi aggiunto come passaggio di orchestrazione in un [percorso utente](userjourneys.md).

Application Insights può unificare gli eventi usando un ID di correlazione per registrare una sessione utente. Application Insights rende disponibili l'evento e la sessione entro pochi secondi e offre molti strumenti di visualizzazione, esportazione e analisi.

## <a name="prerequisites"></a>Prerequisiti

Completare la procedura descritta in [Introduzione ai criteri personalizzati](custom-policy-get-started.md). È necessario disporre di un criterio personalizzato di lavoro per l'iscrizione e l'accesso con account locali.

## <a name="create-an-application-insights-resource"></a>Creare una risorsa di Application Insights

Quando si usa Application Insights con Azure AD B2C, è sufficiente creare una risorsa e ottenere la chiave di strumentazione. Per informazioni, vedere [creare una risorsa Application Insights](../azure-monitor/app/create-new-resource.md)

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Assicurarsi di usare la directory che contiene la sottoscrizione di Azure selezionando il filtro **directory + sottoscrizione** nel menu in alto e scegliendo la directory che contiene la sottoscrizione. Questo non è il tenant di Azure AD B2C.
3. Scegliere **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure e quindi cercare e selezionare **Application Insights**.
4. Fare clic su **Crea**.
5. Immettere un **nome** per la risorsa.
6. In **Tipo di applicazione** selezionare **Applicazione Web ASP.NET**.
7. In **Gruppo di risorse** selezionare un gruppo esistente o immettere un nome per un nuovo gruppo.
8. Fare clic su **Crea**.
4. Dopo aver creato la risorsa di Application Insights, espandere **Informazioni di base** e prendere nota della chiave di strumentazione.

![Panoramica e chiave di strumentazione di Application Insights](./media/analytics-with-application-insights/app-insights.png)

## <a name="define-claims"></a>Definire attestazioni

Un'attestazione fornisce un'archiviazione temporanea dei dati durante l'esecuzione di un Azure AD B2C criteri. Lo [schema di attestazioni](claimsschema.md) è la posizione in cui si dichiarano le attestazioni.

1. Aprire il file di estensioni dei criteri, ad esempio <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.
1. Cercare l'elemento [BuildingBlocks](buildingblocks.md). Se l'elemento non esiste, aggiungerlo.
1. Individuare l'elemento [ClaimsSchema](claimsschema.md). Se l'elemento non esiste, aggiungerlo.
1. Aggiungere le attestazioni seguenti all'elemento **ClaimsSchema**. 

```xml
<ClaimType Id="EventType">
  <DisplayName>Event type</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="EventTimestamp">
  <DisplayName>Event timestamp</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="PolicyId">
  <DisplayName>Policy Id</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="Culture">
  <DisplayName>Culture ID</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="CorrelationId">
  <DisplayName>Correlation Id</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="federatedUser">
  <DisplayName>Federated user</DisplayName>
  <DataType>boolean</DataType>
</ClaimType>
<ClaimType Id="parsedDomain">
  <DisplayName>Domain name</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>The domain portion of the email address.</UserHelpText>
</ClaimType>
<ClaimType Id="userInLocalDirectory">
  <DisplayName>userInLocalDirectory</DisplayName>
  <DataType>boolean</DataType>
</ClaimType>
```

## <a name="add-new-technical-profiles"></a>Aggiungere nuovi profili tecnici

I profili tecnici possono essere considerati funzioni in Framework dell'esperienza di gestione delle identità di Azure AD B2C. Questa tabella definisce i profili tecnici usati per aprire una sessione e pubblicare gli eventi.

| Profilo tecnico | Attività |
| ----------------- | -----|
| AppInsights-Common | Set comune di parametri da includere in tutti i profili tecnici di Azure Insights. |
| AppInsights-SignInRequest | Registra un `SignInRequest` evento con un set di attestazioni quando è stata ricevuta una richiesta di accesso. |
| AppInsights-UserSignUp | Registra un `UserSignUp` evento quando l'utente attiva l'opzione di iscrizione in un percorso di iscrizione/accesso. |
| AppInsights-SignInComplete | Registra un `SignInComplete` evento al completamento di un'autenticazione quando un token è stato inviato all'applicazione relying party. |

Aggiungere i profili al file *TrustFrameworkExtensions.xml* dallo starter pack. Aggiungere questi elementi all'elemento **ClaimsProviders**:

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AppInsights-Common">
      <DisplayName>Application Insights</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- The ApplicationInsights instrumentation key which will be used for logging the events -->
        <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
        <Item Key="DeveloperMode">false</Item>
        <Item Key="DisableTelemetry ">false</Item>
      </Metadata>
      <InputClaims>
        <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
        <InputClaim ClaimTypeReferenceId="EventTimestamp" PartnerClaimType="{property:EventTimestamp}" DefaultValue="{Context:DateTimeInUtc}" />
        <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
        <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:CorrelationId}" DefaultValue="{Context:CorrelationId}" />
        <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
      </InputClaims>
    </TechnicalProfile>

    <TechnicalProfile Id="AppInsights-SignInRequest">
      <InputClaims>
        <!-- An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider to create an event with the specified value. -->
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInRequest" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>

    <TechnicalProfile Id="AppInsights-UserSignUp">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="UserSignUp" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>
    
    <TechnicalProfile Id="AppInsights-SignInComplete">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInComplete" />
        <InputClaim ClaimTypeReferenceId="federatedUser" PartnerClaimType="{property:FederatedUser}" DefaultValue="false" />
        <InputClaim ClaimTypeReferenceId="parsedDomain" PartnerClaimType="{property:FederationPartner}" DefaultValue="Not Applicable" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

> [!IMPORTANT]
> Modificare la chiave di strumentazione nel profilo tecnico `AppInsights-Common` con il GUID fornito dalla risorsa di Application Insights.

## <a name="add-the-technical-profiles-as-orchestration-steps"></a>Aggiungere i profili tecnici come passaggi di orchestrazione

Chiamare `AppInsights-SignInRequest` come secondo passaggio di orchestrazione per tenere traccia della ricezione di una richiesta di accesso/iscrizione:

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AppInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Immediatamente *prima* del passaggio di orchestrazione `SendClaims`, aggiungere un nuovo passaggio che chiama `AppInsights-UserSignup`. L'attivazione avviene quando l'utente fa clic sul pulsante di iscrizione in un percorso di iscrizione/accesso.

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
    <ClaimsExchange Id="TrackUserSignUp" TechnicalProfileReferenceId="AppInsights-UserSignup" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Immediatamente dopo il passaggio di orchestrazione `SendClaims`, chiamare `AppInsights-SignInComplete`. Questo passaggio mostra un percorso completato correttamente.

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="10" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AppInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Dopo avere aggiunto i nuovi passaggi di orchestrazione, rinumerare i passaggi in sequenza senza ignorare alcun numero intero, da 1 a N.


## <a name="upload-your-file-run-the-policy-and-view-events"></a>Caricare il file, eseguire i criteri e visualizzare gli eventi

Salvare e caricare il file *TrustFrameworkExtensions.xml*. Chiamare quindi i criteri della relying party dall'applicazione o usare **Esegui adesso** nel portale di Azure. Entro pochi secondi, gli eventi saranno disponibili in Application Insights.

1. Aprire la risorsa di **Application Insights** nel tenant di Azure Active Directory.
2. Selezionare **Usage**  >  **eventi**di utilizzo.
3. Impostare **Durante** su **Ultima ora** e **Da** su **3 minuti**.  Potrebbe essere necessario selezionare **Aggiorna** per visualizzare i risultati.

![Pannello degli eventi di utilizzo in Application Insights](./media/analytics-with-application-insights/app-ins-graphic.png)

## <a name="optional-collect-more-data"></a>Opzionale Raccogli più dati

Aggiungere tipi di attestazioni ed eventi al proprio percorso utente in base alle esigenze. È possibile usare i [resolver di attestazioni](claim-resolver-overview.md) o qualsiasi tipo di attestazione stringa, aggiungere le attestazioni aggiungendo un elemento **Claim di Input** all'evento Application Insights o al profilo tecnico AppInsights-Common.

- **ClaimTypeReferenceId** è il riferimento a un tipo di attestazione.
- **PartnerClaimType** è il nome della proprietà visualizzato in Azure Insights. Usare la sintassi `{property:NAME}`, dove `NAME` è la proprietà da aggiungere all'evento.
- Per **DefaultValue** usare qualsiasi valore stringa o il resolver di attestazioni.

```xml
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="{property:app_session}" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="{property:loyalty_number}" DefaultValue="{OAUTH-KV:loyalty_number}" />
<InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
```

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Application Insights](application-insights-technical-profile.md) profilo tecnico sono disponibili nella Guida di riferimento a Framework dell'esperienza. 
