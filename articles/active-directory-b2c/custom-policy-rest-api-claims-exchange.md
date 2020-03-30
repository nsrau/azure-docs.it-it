---
title: Scambi di attestazioni dell'API REST - Azure Active Directory B2C
description: Aggiungere scambi di attestazioni dell'API REST ai criteri personalizzati in Active Directory B2C.Add REST API claims exchanges to custom policies in Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6316165ba08d055be1186995e2fe2ad5a0079fb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80330716"
---
# <a name="walkthrough-add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>Procedura dettagliata: Aggiungere scambi di attestazioni DELL'API REST ai criteri personalizzati in Azure Active Directory B2CWalkthrough: Add REST API claims exchanges to custom policies in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) consente agli sviluppatori di identità di integrare un'interazione con un'API RESTful in un percorso utente. Al termine di questa procedura dettagliata, sarà possibile creare un percorso utente B2C di Azure AD che interagisce con i [servizi RESTful.](custom-policy-rest-api-intro.md)

In questo scenario vengono arricchiti i dati token dell'utente integrandosi con un flusso di lavoro line-of-business aziendale. Durante l'iscrizione o l'accesso con account locale o federato, Azure AD B2C richiama un'API REST per ottenere i dati del profilo esteso dell'utente da un'origine dati remota. In questo esempio Azure AD B2C invia l'identificatore univoco dell'utente, objectId. L'API REST restituisce quindi il saldo dell'account dell'utente (un numero casuale). Utilizzare questo esempio come punto di partenza per l'integrazione con il proprio sistema CRM, database di marketing o qualsiasi flusso di lavoro line-of-business.

È inoltre possibile progettare l'interazione come profilo tecnico di convalida. Ciò è adatto quando l'API REST sollevirà i dati sullo schermo e restituirà attestazioni. Per altre informazioni, vedere [Procedura dettagliata: Integrare scambi di attestazioni dell'API REST nel percorso utente B2C di Azure AD per convalidare l'input dell'utente.](custom-policy-rest-api-claims-validation.md)

## <a name="prerequisites"></a>Prerequisiti

- Completare la procedura descritta in [Introduzione ai criteri personalizzati](custom-policy-get-started.md). È necessario disporre di un criterio personalizzato di lavoro per l'iscrizione e l'accesso con account locali.
- Informazioni su come integrare gli scambi di [attestazioni dell'API REST nei criteri personalizzati di Azure AD B2C.](custom-policy-rest-api-intro.md)

## <a name="prepare-a-rest-api-endpoint"></a>Preparare un endpoint dell'API RESTPrepare a REST API endpoint

Per questa procedura dettagliata, è necessario disporre di un'API REST che convalida se l'objectId B2C di Azure AD di un utente è registrato nel sistema back-end. Se registrata, l'API REST restituisce il saldo dell'account utente. In caso contrario, l'API REST registra il `50.00`nuovo account nella directory e restituisce il saldo iniziale.

Il codice JSON seguente illustra i dati che Azure AD B2C invierà all'endpoint dell'API REST. 

```json
{
    "objectId": "User objectId",
    "language": "Current UI language"
}
```

Una volta che l'API REST convalida i dati, deve restituire un HTTP 200 (Ok), con i seguenti dati JSON:

```json
{
    "balance": "760.50"
}
```

La configurazione dell'endpoint dell'API REST non rientra nell'ambito di questo articolo. È stato creato un esempio di funzioni di Azure.We have created an [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-reference) sample. È possibile accedere al codice completo della funzione di Azure in [GitHub](https://github.com/azure-ad-b2c/rest-api/tree/master/source-code/azure-function).

## <a name="define-claims"></a>Definire le attestazioni

Un'attestazione fornisce l'archiviazione temporanea dei dati durante l'esecuzione di criteri B2C di Azure AD. È possibile dichiarare attestazioni all'interno della sezione [dello schema delle attestazioni.](claimsschema.md) 

1. Aprire il file delle estensioni del criterio. Ad esempio, <em> `SocialAndLocalAccounts/` </em>.
1. Cercare l'elemento [BuildingBlocks](buildingblocks.md). Se l'elemento non esiste, aggiungerlo.
1. Individuare l'elemento [ClaimsSchema.](claimsschema.md) Se l'elemento non esiste, aggiungerlo.
1. Aggiungere le attestazioni seguenti all'elemento **ClaimsSchema.**  

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

Un [profilo tecnico Restful](restful-technical-profile.md) fornisce supporto per l'interfacciamento con il proprio servizio RESTful. Azure AD B2C invia i dati al `InputClaims` servizio RESTful in `OutputClaims` una raccolta e riceve i dati in una raccolta. Individuare l'elemento **ClaimsProviders** nel <em>**`TrustFrameworkExtensions.xml`**</em> file e aggiungere un nuovo provider di attestazioni come segue:

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

In questo esempio, l'oggetto `userLanguage` verrà `lang` inviato al servizio REST come all'interno del payload JSON. Il valore `userLanguage` dell'attestazione contiene l'ID lingua dell'utente corrente. Per ulteriori informazioni, vedere [resolver di attestazioni](claim-resolver-overview.md).

I commenti `AuthenticationType` precedenti `AllowInsecureAuthInProduction` e specificare le modifiche da apportare quando ci si sposta in un ambiente di produzione. Per informazioni su come proteggere le API RESTful per la produzione, vedere [Secure RESTful API](secure-rest-api.md).

## <a name="add-an-orchestration-step"></a>Aggiungere un passaggio di orchestrazioneAdd an orchestration step

[I percorsi utente specificano](userjourneys.md) percorsi espliciti attraverso i quali un criterio consente a un'applicazione relying party di ottenere le attestazioni desiderate per un utente. Un percorso utente è costituito da una sequenza di orchestrazione da seguire per garantire l'esito positivo di una transazione. È possibile aggiungere o sottrarre passaggi di orchestrazione. In questo caso, si aggiungerà un nuovo passaggio di orchestrazione che viene utilizzato per aumentare le informazioni fornite all'applicazione dopo l'iscrizione o l'accesso dell'utente tramite la chiamata all'API REST.

1. Aprire il file di base dei criteri, Ad esempio, <em> `SocialAndLocalAccounts/` </em>.
1. Cercare l'elemento `<UserJourneys>`. Copiare l'intero elemento ed eliminarlo.
1. Aprire il file delle estensioni del criterio. Ad esempio, <em> `SocialAndLocalAccounts/` </em>.
1. Incollare `<UserJourneys>` il nel file delle estensioni, dopo la chiusura dell'elemento. `<ClaimsProviders>`
1. Individuare `<UserJourney Id="SignUpOrSignIn">`il , e aggiungere il passaggio di orchestrazione seguente prima dell'ultimo.

    ```XML
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

1. Eseguire il refactoring dell'ultimo `Order` `8`passaggio dell'orchestrazione modificando l'oggetto in . Gli ultimi due passaggi di orchestrazione dovrebbero essere simili ai seguenti:Your final two orchestration steps should look like the following:

    ```XML
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>

    <OrchestrationStep Order="8" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
    ```

1. Ripetere gli ultimi due passaggi per i percorsi utente **ProfileEdit** e **PasswordReset.**


## <a name="include-a-claim-in-the-token"></a>Includere un'attestazione nel tokenInclude a claim in the token 

Per restituire `balance` l'attestazione all'applicazione relying party, <em> `SocialAndLocalAccounts/` </em> aggiungere un'attestazione di output al file. L'aggiunta di un'attestazione di output emetterà l'attestazione nel token dopo un percorso utente riuscito e verrà inviata all'applicazione. Modificare l'elemento del profilo tecnico all'interno della sezione relying party da aggiungere `balance` come attestazione di output.
 
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

Ripetere questo passaggio per i percorsi utente **ProfileEdit.xml**e **PasswordReset.xml.**

Salvare i file modificati: *TrustFrameworkBase.xml*e *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml*e *PasswordReset.xml*. 

## <a name="test-the-custom-policy"></a>Testare i criteri personalizzati

1. Accedere al [portale](https://portal.azure.com)di Azure .
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD selezionando il filtro **Directory e sottoscrizione** nel menu in alto e scegliendo la directory che contiene il tenant di Azure AD.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Registrazioni per l'app**.
1. Selezionare **Identity Experience Framework**.
1. Selezionare **Carica criteri personalizzati**, quindi caricare i file dei criteri modificati: *TrustFrameworkBase.xml*e *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml*e *PasswordReset.xml*. 
1. Selezionare il criterio di iscrizione o di accesso che è stato caricato e fare clic sul pulsante **Esegui adesso**.
1. Dovresti essere in grado di registrarti utilizzando un indirizzo email o un account Facebook.
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


## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come proteggere le API, vedere gli articoli seguenti:To learn how to secure your APIs, see the following articles:

- [Procedura dettagliata: Integrare scambi di attestazioni API REST nei percorsi utente di Azure AD B2C come passaggio di orchestrazione](custom-policy-rest-api-claims-exchange.md)
- [Proteggi la tua API RESTful](secure-rest-api.md)
- [Riferimento: RESTful profilo tecnico](restful-technical-profile.md)
