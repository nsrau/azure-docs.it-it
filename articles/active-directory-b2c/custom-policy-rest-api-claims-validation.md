---
title: Scambi di attestazioni API REST come convalida
titleSuffix: Azure AD B2C
description: Procedura dettagliata per la creazione di un percorso utente Azure AD B2C che interagisce con i servizi RESTful.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6381f678979437fdfc10d2ea63a79ed347183e92
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85388919"
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-to-validate-user-input"></a>Procedura dettagliata: integrare scambi di attestazioni API REST nel percorso utente di Azure AD B2C per convalidare l'input dell'utente

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Il Framework Framework dell'esperienza (Identity Experience Framework) che sottende Azure Active Directory B2C (Azure AD B2C) consente agli sviluppatori di identità di integrare un'interazione con un'API RESTful in un percorso utente.  Al termine di questa procedura dettagliata, sarà possibile creare un percorso utente Azure AD B2C che interagisce con i [servizi RESTful](custom-policy-rest-api-intro.md) per convalidare l'input dell'utente.

In questo scenario verrà aggiunta la possibilità per gli utenti di immettere un numero di fedeltà nella pagina di iscrizione Azure AD B2C. Si convaliderà se questa combinazione di indirizzo di posta elettronica e numero di fedeltà viene mappata a un codice promozionale inviando questi dati a un'API REST. Se l'API REST trova un codice promozionale per questo utente, verrà restituito Azure AD B2C. Infine, il codice promozionale verrà inserito nelle attestazioni del token per l'applicazione da utilizzare.

È possibile progettare l'interazione anche come passaggio di orchestrazione. Questo è adatto quando l'API REST non convaliderà i dati sullo schermo e restituirà sempre le attestazioni. Per altre informazioni, vedere [Procedura dettagliata: Integrare scambi di attestazioni API REST nei percorsi utente di Azure AD B2C come passaggio di orchestrazione](custom-policy-rest-api-claims-exchange.md).

## <a name="prerequisites"></a>Prerequisiti

- Completare la procedura descritta in [Introduzione ai criteri personalizzati](custom-policy-get-started.md). È necessario disporre di un criterio personalizzato di lavoro per l'iscrizione e l'accesso con account locali.
- Informazioni su come [integrare scambi di attestazioni API REST nei criteri personalizzati di Azure AD B2C](custom-policy-rest-api-intro.md).

## <a name="prepare-a-rest-api-endpoint"></a>Preparare un endpoint dell'API REST

Per questa procedura dettagliata, è necessario disporre di un'API REST per verificare se un indirizzo di posta elettronica è registrato nel sistema back-end con un ID fedeltà. Se registrata, l'API REST deve restituire un codice promozionale per la registrazione, che può essere usato dal cliente per acquistare beni all'interno dell'applicazione. In caso contrario, l'API REST deve restituire un messaggio di errore HTTP 409: "ID fedeltà' {ID fedeltà}' non è associato all'indirizzo di posta elettronica ' {email}'".

Il codice JSON seguente illustra i dati che Azure AD B2C invierà all'endpoint dell'API REST. 

```json
{
    "email": "User email address",
    "language": "Current UI language",
    "loyaltyId": "User loyalty ID"
}
```

Quando l'API REST convalida i dati, deve restituire un codice HTTP 200 (Ok) con i dati JSON seguenti:

```json
{
    "promoCode": "24534"
}
```

Se la convalida ha esito negativo, l'API REST deve restituire un HTTP 409 (conflitto) con l' `userMessage` elemento JSON. Framework dell'esperienza prevede l' `userMessage` attestazione restituita dall'API REST. Questa attestazione verrà presentata come stringa all'utente in caso di esito negativo della convalida.

```json
{
    "version": "1.0.1",
    "status": 409,
    "userMessage": "LoyaltyId ID '1234' is not associated with 'david@contoso.com' email address."
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
<ClaimType Id="loyaltyId">
  <DisplayName>Your loyalty ID</DisplayName>
  <DataType>string</DataType>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
<ClaimType Id="promoCode">
  <DisplayName>Your promo code</DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
</ClaimType>
  <ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="configure-the-restful-api-technical-profile"></a>Configurare il profilo tecnico dell'API RESTful 

Un [profilo tecnico RESTful](restful-technical-profile.md) fornisce supporto per l'interazione con il servizio RESTful. Azure AD B2C invia dati al servizio RESTful in una raccolta `InputClaims` e riceve dati in una raccolta `OutputClaims`. Trovare l'elemento **ClaimsProviders** e aggiungere un nuovo provider di attestazioni come indicato di seguito:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-ValidateProfile">
      <DisplayName>Check loyaltyId Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/ValidateProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="loyaltyId" />
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="promoCode" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

In questo esempio `userLanguage` viene inviato al servizio REST come `lang` nel payload JSON. Il valore dell'attestazione `userLanguage` contiene l'ID della lingua utente corrente. Per altre informazioni, vedere i [resolver di attestazioni](claim-resolver-overview.md).

I commenti precedenti `AuthenticationType` e `AllowInsecureAuthInProduction` specificano le modifiche che è necessario apportare quando si passa a un ambiente di produzione. Per informazioni su come proteggere le API RESTful per la produzione, vedere [Proteggere i servizi RESTful](secure-rest-api.md).

## <a name="validate-the-user-input"></a>Convalidare l'input dell'utente

Per ottenere il numero di fedeltà dell'utente durante l'iscrizione, è necessario consentire all'utente di immettere questi dati sullo schermo. Aggiungere l'attestazione di output **loyaltyId** alla pagina di iscrizione aggiungendola all'elemento della sezione del profilo tecnico di iscrizione esistente `OutputClaims` . Specificare l'intero elenco di attestazioni di output per controllare l'ordine in cui le attestazioni vengono presentate sullo schermo.  

Aggiungere il riferimento al profilo tecnico di convalida al profilo tecnico di iscrizione, che chiama `REST-ValidateProfile` . Il nuovo profilo tecnico di convalida verrà aggiunto all'inizio della `<ValidationTechnicalProfiles>` raccolta definita nei criteri di base. Questo comportamento significa che, solo dopo la corretta convalida, Azure AD B2C passa a crea l'account nella directory.   

1. Trovare l'elemento **ClaimsProviders**. Aggiungere un nuovo provider di attestazioni come segue:

    ```xml
    <ClaimsProvider>
      <DisplayName>Local Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="newPassword" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="displayName"/>
            <OutputClaim ClaimTypeReferenceId="givenName"/>
            <OutputClaim ClaimTypeReferenceId="surName"/>
            <!-- Required to present the text box to collect the data from the user -->
            <OutputClaim ClaimTypeReferenceId="loyaltyId"/>
            <!-- Required to pass the promoCode returned from "REST-ValidateProfile" 
            to subsequent orchestration steps and token issuance-->
            <OutputClaim ClaimTypeReferenceId="promoCode" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="REST-ValidateProfile" />
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    <ClaimsProvider>
      <DisplayName>Self Asserted</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SelfAsserted-Social">
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="email" />
          </InputClaims>
            <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="displayName"/>
            <OutputClaim ClaimTypeReferenceId="givenName"/>
            <OutputClaim ClaimTypeReferenceId="surname"/>
            <!-- Required to present the text box to collect the data from the user -->
            <OutputClaim ClaimTypeReferenceId="loyaltyId"/>
            <!-- Required to pass the promoCode returned from "REST-ValidateProfile" 
            to subsequent orchestration steps and token issuance-->
            <OutputClaim ClaimTypeReferenceId="promoCode" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="REST-ValidateProfile"/>
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

## <a name="include-a-claim-in-the-token"></a>Includere un'attestazione nel token 

Per restituire l'attestazione del codice promozionale all'applicazione relying party, aggiungere un'attestazione di output al <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> file. L'attestazione di output consentirà di aggiungere l'attestazione al token dopo un percorso utente completato e verrà inviata all'applicazione. Modificare l'elemento profilo tecnico nella sezione relying party per aggiungere `promoCode` come attestazione di output.
 
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
      <OutputClaim ClaimTypeReferenceId="promoCode" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-the-custom-policy"></a>Testare i criteri personalizzati

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD. A tale scopo, selezionare il filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant di Azure AD.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Registrazioni per l'app**.
1. Fare clic su **Framework dell'esperienza di gestione delle identità**.
1. Selezionare **carica criteri personalizzati**, quindi caricare i file dei criteri modificati: *TrustFrameworkExtensions.xml*e *SignUpOrSignin.xml*. 
1. Selezionare il criterio di iscrizione o di accesso che è stato caricato e fare clic sul pulsante **Esegui adesso**.
1. Dovrebbe essere possibile iscriversi usando un indirizzo di posta elettronica.
1. Fare clic sul collegamento **Iscriviti ora** .
1. In **ID fedeltà**Digitare 1234, quindi fare clic su **continua**. A questo punto, verrà ricevuto un messaggio di errore di convalida.
1. Passare a un altro valore e fare clic su **continua**.
1. Il token inviato all'applicazione include l'attestazione `promoCode`.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584295703,
  "nbf": 1584292103,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584292103,
  "auth_time": 1584292103,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "promoCode": "84362"
  ...
}
```

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti per informazioni su come proteggere le API:

- [Procedura dettagliata: Integrare scambi di attestazioni API REST nei percorsi utente di Azure AD B2C come passaggio di orchestrazione](custom-policy-rest-api-claims-exchange.md)
- [Proteggere i servizi RESTful](secure-rest-api.md)
- [Informazioni di riferimento: Profilo tecnico RESTful](restful-technical-profile.md)
