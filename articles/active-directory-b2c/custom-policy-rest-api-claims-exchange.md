---
title: Scambi di attestazioni API REST - Azure Active Directory B2C
description: Aggiungere scambi di attestazioni API REST ai criteri personalizzati in Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/18/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e22a6028f5b7fa8cf81ddf0e3e2a550859aad0ac
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91259595"
---
# <a name="walkthrough-add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>Procedura dettagliata: Aggiungere scambi di attestazioni API REST ai criteri personalizzati in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) consente agli sviluppatori delle identità di integrare un'interazione con un'API RESTful in un percorso utente. Al termine di questa procedura dettagliata sarà possibile creare un percorso utente di Azure AD B2C che interagisce con i [servizi RESTful](custom-policy-rest-api-intro.md).

In questo scenario i dati del token dell'utente vengono arricchiti mediante l'integrazione con un flusso di lavoro line-of-business aziendale. Durante l'iscrizione o l'accesso con un account locale o federato, Azure AD B2C richiama un'API REST per ottenere i dati del profilo esteso dell'utente da un'origine dati remota. In questo esempio Azure AD B2C invia l'identificatore univoco dell'utente, ovvero objectId. L'API REST restituisce quindi il saldo dell'account utente (un numero casuale). Usare questo esempio come punto iniziale per l'integrazione con il sistema CRM, il database di marketing o qualsiasi flusso di lavoro line-of-business.

È anche possibile progettare l'interazione come un profilo tecnico di convalida. Questa situazione è idonea quando l'API REST convalida i dati sullo schermo e restituisce attestazioni. Per altre informazioni, vedere [Procedura dettagliata: Integrare scambi di attestazioni API REST nel percorso utente di Azure AD B2C come convalida dell'input utente](custom-policy-rest-api-claims-validation.md).

## <a name="prerequisites"></a>Prerequisiti

- Completare la procedura descritta in [Introduzione ai criteri personalizzati](custom-policy-get-started.md). È necessario disporre di un criterio personalizzato di lavoro per l'iscrizione e l'accesso con account locali.
- Informazioni su come [integrare scambi di attestazioni API REST nei criteri personalizzati di Azure AD B2C](custom-policy-rest-api-intro.md).

## <a name="prepare-a-rest-api-endpoint"></a>Preparare un endpoint dell'API REST

Per questa procedura dettagliata è necessario disporre di un'API REST per verificare che l'objectId Azure AD B2C di un utente sia registrato nel sistema back-end. Se registrata, l'API REST restituisce il saldo dell'account utente. In caso contrario, l'API REST registra il nuovo account nella directory e restituisce il saldo iniziale `50.00`.

Il codice JSON seguente illustra i dati che Azure AD B2C invierà all'endpoint dell'API REST. 

```json
{
    "objectId": "User objectId",
    "lang": "Current UI language"
}
```

Quando l'API REST convalida i dati, deve restituire un codice HTTP 200 (Ok) con i dati JSON seguenti:

```json
{
    "balance": "760.50"
}
```

La configurazione dell'endpoint dell'API REST non rientra nell'ambito di questo articolo. È stata creato un esempio di [Funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-reference). È possibile accedere al codice completo della funzione di Azure in [GitHub](https://github.com/azure-ad-b2c/rest-api/tree/master/source-code/azure-function).

## <a name="define-claims"></a>Definire attestazioni

Un'attestazione fornisce un'archiviazione temporanea dei dati durante l'esecuzione dei criteri di Azure AD B2C. È possibile dichiarare attestazioni nella sezione [ClaimsSchema](claimsschema.md). 

1. Aprire il file di estensioni dei criteri, ad esempio <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.
1. Cercare l'elemento [BuildingBlocks](buildingblocks.md). Se l'elemento non esiste, aggiungerlo.
1. Individuare l'elemento [ClaimsSchema](claimsschema.md). Se l'elemento non esiste, aggiungerlo.
1. Aggiungere le attestazioni seguenti all'elemento **ClaimsSchema**.  

```xml
<ClaimType Id="balance">
  <DisplayName>Your Balance</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="configure-the-restful-api-technical-profile"></a>Configurare il profilo tecnico dell'API RESTful 

Un [profilo tecnico RESTful](restful-technical-profile.md) fornisce supporto per l'interazione con il servizio RESTful. Azure AD B2C invia dati al servizio RESTful in una raccolta `InputClaims` e riceve dati in una raccolta `OutputClaims`. Trovare l'elemento **ClaimsProviders** nel file <em> **`TrustFrameworkExtensions.xml`**</em> e aggiungere un nuovo provider di attestazioni come indicato di seguito:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="balance" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

In questo esempio `userLanguage` viene inviato al servizio REST come `lang` nel payload JSON. Il valore dell'attestazione `userLanguage` contiene l'ID della lingua utente corrente. Per altre informazioni, vedere i [resolver di attestazioni](claim-resolver-overview.md).

I commenti precedenti `AuthenticationType` e `AllowInsecureAuthInProduction` specificano le modifiche che è necessario apportare quando si passa a un ambiente di produzione. Per informazioni su come proteggere le API RESTful per la produzione, vedere [Proteggere i servizi RESTful](secure-rest-api.md).

## <a name="add-an-orchestration-step"></a>Per aggiungere un passaggio di orchestrazione

I [percorsi utente](userjourneys.md) specificano percorsi espliciti attraverso cui un criterio consente a un'applicazione relying party di ottenere le attestazioni desiderate per un utente. Un percorso utente è costituito da una sequenza di orchestrazione da seguire per garantire l'esito positivo di una transazione. È possibile aggiungere o sottrarre passaggi di orchestrazione. In questo caso, viene aggiunto un nuovo passaggio di orchestrazione usato per ampliare le informazioni fornite all'applicazione dopo l'iscrizione o l'accesso dell'utente tramite la chiamata all'API REST.

1. Aprire il file di base dei criteri, ad esempio <em>`SocialAndLocalAccounts/`**`TrustFrameworkBase.xml`**</em>.
1. Cercare l'elemento `<UserJourneys>`. Copiare l'intero elemento, quindi eliminarlo.
1. Aprire il file di estensioni dei criteri, ad esempio <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.
1. Incollare `<UserJourneys>` nel file delle estensioni, dopo la chiusura dell'elemento `<ClaimsProviders>`.
1. Individuare `<UserJourney Id="SignUpOrSignIn">` e aggiungere il passaggio di orchestrazione seguente prima dell'ultimo.

    ```xml
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

1. Effettuare il refactoring dell'ultimo passaggio di orchestrazione modificando `Order` in `8`. Gli ultimi due passaggi di orchestrazione appariranno come illustrato di seguito:

    ```xml
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>

    <OrchestrationStep Order="8" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
    ```

1. Ripetere gli ultimi due passaggi per i percorsi utente **ProfileEdit** e **PasswordReset**.


## <a name="include-a-claim-in-the-token"></a>Includere un'attestazione nel token 

Per restituire l'attestazione `balance` all'applicazione relying party, aggiungere un'attestazione di output al file <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em>. Se si aggiunge un'attestazione di output, l'attestazione viene rilasciata nel token dopo un percorso utente con esito positivo e viene inviata all'applicazione. Modificare l'elemento profilo tecnico nella sezione relying party per aggiungere `balance` come attestazione di output.
 
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
      <OutputClaim ClaimTypeReferenceId="balance" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

Ripetere questo passaggio per i percorsi utente **ProfileEdit.xml** e **PasswordReset.xml**.

Salvare i file modificati: *TrustFrameworkBase.xml*, *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml*e *PasswordReset.xml*. 

## <a name="test-the-custom-policy"></a>Testare i criteri personalizzati

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD. A tale scopo, selezionare il filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant di Azure AD.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Registrazioni per l'app**.
1. Fare clic su **Framework dell'esperienza di gestione delle identità**.
1. Selezionare **Carica i criteri personalizzati** e caricare i file dei criteri modificati: *TrustFrameworkBase.xml*, *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml*e *PasswordReset.xml*. 
1. Selezionare il criterio di iscrizione o di accesso che è stato caricato e fare clic sul pulsante **Esegui adesso**.
1. Dovrebbe essere possibile iscriversi usando un indirizzo e-mail o un account Facebook.
1. Il token inviato all'applicazione include l'attestazione `balance`.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584961516,
  "nbf": 1584957916,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584957916,
  "auth_time": 1584957916,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "balance": "202.75"
  ...
}
```

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti per informazioni su come proteggere le API:

- [Procedura dettagliata: Integrare scambi di attestazioni API REST nei percorsi utente di Azure AD B2C come passaggio di orchestrazione](custom-policy-rest-api-claims-exchange.md)
- [Proteggere i servizi RESTful](secure-rest-api.md)
- [Informazioni di riferimento: Profilo tecnico RESTful](restful-technical-profile.md)
