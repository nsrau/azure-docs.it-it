---
title: Configurare l'accesso con un account LinkedIn utilizzando criteri personalizzati
titleSuffix: Azure AD B2C
description: Configurare l'accesso con un account LinkedIn in Azure Active Directory B2C usando criteri personalizzati.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 80bd1b65d04ea49fc742033e1850d95a85021c9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188172"
---
# <a name="set-up-sign-in-with-a-linkedin-account-using-custom-policies-in-azure-active-directory-b2c"></a>Configurare l'accesso con un account LinkedIn usando criteri personalizzati in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Questo articolo illustra come abilitare l'accesso per gli utenti da un account LinkedIn usando [criteri personalizzati](custom-policy-overview.md) in Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Prerequisiti

- Completare le procedure illustrate in [Introduzione ai criteri personalizzati in Azure Active Directory B2C](custom-policy-get-started.md).
- Account LinkedIn - Se non ne hai già uno, [crea un account.](https://www.linkedin.com/start/join)
- Pagina LinkedIn: è necessaria una [pagina LinkedIn](https://www.linkedin.com/help/linkedin/answer/710/creating-a-linkedin-company-page) da associare all'applicazione LinkedIn creata nella sezione successiva.

## <a name="create-an-application"></a>Creare un'applicazione

Per usare LinkedIn come provider di identità in Azure AD B2C, è necessario creare un'applicazione LinkedIn.

### <a name="create-app"></a>Creare l'app

1. Accedere al sito Web per la [gestione delle applicazioni LinkedIn](https://www.linkedin.com/secure/developer?newapp=) con le credenziali dell'account LinkedIn.
1. Selezionare **Crea app**.
1. Immettere un **nome app**.
1. Inserisci un nome **di società** corrispondente al nome di una pagina LinkedIn. Crea una pagina LinkedIn se non ne hai già una.
1. (Facoltativo) Immettere un **URL dell'informativa sulla privacy**. Deve essere un URL valido, ma non deve essere un endpoint raggiungibile.
1. Inserisci **un'email aziendale**.
1. Caricare un'immagine **del logo dell'app.** L'immagine del logo deve essere quadrata e le sue dimensioni devono essere di almeno 100x100 pixel.
1. Lasciare le impostazioni predefinite nella sezione **Prodotti.**
1. Rivedere le informazioni presentate in **termini giuridici**. Se accetti i termini, seleziona la casella.
1. Selezionare **Crea app**.

### <a name="configure-auth"></a>Configurare l'autenticazione e l'autorizzazione

1. Selezionare la scheda **Autenticazione.**
1. Registrare **l'ID client**.
1. Rivelare e registrare il **segreto client**.
1. In **Impostazioni OAuth 2.0**aggiungere il seguente URL di **reindirizzamento.** Sostituire `your-tenant` con il nome del tenant. Usare **tutte le lettere minuscole** per il nome del tenant anche se è definito con lettere maiuscole in Azure AD B2C.

    `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/oauth2/authresp`

## <a name="create-a-policy-key"></a>Creare una chiave dei criteri

È necessario archiviare il segreto client registrato in precedenza nel tenant di Azure AD B2C.

1. Accedere al [portale](https://portal.azure.com/)di Azure .
2. Assicurarsi di usare la directory che contiene il tenant B2C di Azure AD. Nel menu superiore, selezionare il filtro **Directory e sottoscrizione** e scegliere la directory che contiene il tenant.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
4. Nella pagina Panoramica selezionare **Framework dell'esperienza di gestione delle identità**.
5. Selezionare **Chiavi dei criteri** e quindi **Aggiungi**.
6. Per **Opzioni** scegliere `Manual`.
7. Immettere un **nome** per la chiave dei criteri. Ad esempio: `LinkedInSecret`. Il prefisso *B2C_1A_* viene aggiunto automaticamente al nome della chiave.
8. In **Segreto**immettere il segreto client registrato in precedenza.
9. In **Uso chiave** selezionare `Signature`.
10. Fare clic su **Crea**.

## <a name="add-a-claims-provider"></a>Aggiungere un provider di attestazioni

Per consentire agli utenti di accedere con un account LinkedIn, è necessario definire l'account come provider di attestazioni con cui Azure AD B2C possa comunicare tramite un endpoint. L'endpoint offre un set di attestazioni che vengono usate da Azure AD B2C per verificare se un utente specifico è stato autenticato.

Definire un account LinkedIn come provider di attestazioni aggiungendolo all'elemento **ClaimsProviders** nel file di estensione dei criteri.

1. Aprire il file *SocialAndLocalAccounts, ovvero trustFrameworkExtensions.xml*nell'editor. Questo file si trova nello [starter pack dei criteri personalizzati][starter-pack] scaricato come parte di uno dei prerequisiti.
1. Trovare l'elemento **ClaimsProviders**. Se non esiste, aggiungerlo nell'elemento radice.
1. Aggiungere un nuovo **ClaimsProvider** come illustrato di seguito:

    ```xml
    <ClaimsProvider>
      <Domain>linkedin.com</Domain>
      <DisplayName>LinkedIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LinkedIn-OAUTH">
          <DisplayName>LinkedIn</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">linkedin</Item>
            <Item Key="authorization_endpoint">https://www.linkedin.com/oauth/v2/authorization</Item>
            <Item Key="AccessTokenEndpoint">https://www.linkedin.com/oauth/v2/accessToken</Item>
            <Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
            <Item Key="scope">r_emailaddress r_liteprofile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="external_user_identity_claim_id">id</Item>
            <Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
            <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="client_id">Your LinkedIn application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_LinkedInSecret" />
          </CryptographicKeys>
          <InputClaims />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="linkedin.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
            <OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Sostituire il valore di **client_id** con l'ID client dell'applicazione LinkedIn registrata in precedenza.
1. Salvare il file.

### <a name="add-the-claims-transformations"></a>Aggiungere le trasformazioni delle attestazioniAdd the claims transformations

Il profilo tecnico LinkedIn richiede che le trasformazioni delle attestazioni **ExtractGivenNameFromLinkedInResponse** e **ExtractSurNameFromLinkedInResponse** vengano aggiunte all'elenco di ClaimsTransformations. Se nel file non è stato definito un elemento **ClaimsTransformations,** aggiungere gli elementi XML padre come illustrato di seguito. Le trasformazioni delle attestazioni richiedono inoltre un nuovo tipo di attestazione definito denominato **nullStringClaim**.

Aggiungere l'elemento **BuildingBlocks** nella parte superiore del file *TrustFrameworkExtensions.xml.* Per un esempio, vedere *TrustFrameworkBase.xml.See TrustFrameworkBase.xml* for an example.

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

### <a name="upload-the-extension-file-for-verification"></a>Caricare il file di estensione per la verifica

Si dispone ora di un criterio configurato in modo che Azure AD B2C sappia come comunicare con l'account LinkedIn. Prova a caricare il file di estensione del tuo criterio per verificare che non presenti problemi finora.

1. Nella pagina **Criteri personalizzati** del tenant di Azure AD B2C selezionare **Carica il criterio**.
2. Abilitare **Sovrascrivi il criterio se esistente** e quindi cercare e selezionare il file *TrustFrameworkExtensions.xml*.
3. Fare clic su **Carica**.

## <a name="register-the-claims-provider"></a>Registrare il provider di attestazioni

A questo punto, il provider di identità è stato configurato, ma non è disponibile in nessuna delle schermate di iscrizione o di accesso. Per renderlo disponibile, si crea un duplicato di un percorso utente modello esistente e quindi si modifica tale duplicato in modo che includa anche il provider di identità LinkedIn.

1. Aprire il file *TrustFrameworkBase.xml* nello starter pack.
2. Trovare e copiare l'intero contenuto dell'elemento **UserJourney** che include `Id="SignUpOrSignIn"`.
3. Aprire *TrustFrameworkExtensions.xml* e trovare l'elemento **UserJourneys**. Se l'elemento non esiste, aggiungerne uno.
4. Incollare l'intero contenuto dell'elemento **UserJourney** copiato come figlio dell'elemento **UserJourneys**.
5. Rinominare l'ID del percorso utente. Ad esempio: `SignUpSignInLinkedIn`.

### <a name="display-the-button"></a>Visualizzare il pulsante

L'elemento **ClaimsProviderSelection** è analogo a un pulsante per il provider di identità in una schermata di iscrizione o accesso. Se si aggiunge un elemento **ClaimsProviderSelection** per un account LinkedIn, quando un utente apre la pagina viene visualizzato un nuovo pulsante.

1. Trovare l'elemento **OrchestrationStep** che include `Order="1"` nel percorso utente creato.
2. In **ClaimsProviderSelections** aggiungere l'elemento riportato di seguito. Impostare **TargetClaimsExchangeId** su un valore appropriato, ad esempio `LinkedInExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Collegare il pulsante a un'azione

Ora che il pulsante è stato posizionato, è necessario collegarlo a un'azione. In questo caso, l'azione consiste nel far comunicare Azure AD B2C con un account LinkedIn per ricevere un token.

1. Trovare l'elemento **OrchestrationStep** che include `Order="2"` nel percorso utente.
2. Aggiungere l'elemento **ClaimsExchange** seguente assicurandosi di usare per ID lo stesso valore usato per **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    ```

    Aggiornare il valore di **TechnicalProfileReferenceId** all'ID del profilo tecnico creato in precedenza. Ad esempio: `LinkedIn-OAUTH`.

3. Salvare il file *TrustFrameworkExtensions.xml* e caricarlo di nuovo per la verifica.

## <a name="create-an-azure-ad-b2c-application"></a>Creare un'applicazione Azure AD B2C

La comunicazione con Azure AD B2C avviene tramite un'applicazione che viene eseguita nella registrazione nel tenant B2C. Questa sezione elenca i passaggi facoltativi che è possibile completare per creare un'applicazione di test, se non è già stato fatto.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Aggiornare e testare il file di relying party

Aggiornare il file della relying party (RP) che avvierà il percorso utente appena creato.

1. Creare una copia di *SignUpOrSignIn.xml* nella directory di lavoro e rinominare la copia. Ad esempio, assegnare il nome *SignUpSignInLinkedIn.xml*.
2. Aprire il nuovo file e aggiornare il valore dell'attributo **PolicyId** per **TrustFrameworkPolicy** con un valore univoco. Ad esempio: `SignUpSignInLinkedIn`.
3. Aggiornare il valore di **PublicPolicyUri** con l'URI dei criteri. Ad esempio, `http://contoso.com/B2C_1A_signup_signin_linkedin`
4. Aggiornare il valore dell'attributo **ReferenceId** in **DefaultUserJourney** in modo che corrisponda all'ID del nuovo percorso utente che è stato creato (SignUpSignLinkedIn).
5. Salvare le modifiche, caricare il file e quindi selezionare i nuovi criteri nell'elenco.
6. Verificare che nel campo **Selezionare l'applicazione** sia selezionata l'applicazione Azure AD B2C creata e quindi testarla facendo clic su **Esegui adesso**.

## <a name="migration-from-v10-to-v20"></a>Migrazione da v1.0 a v2.0

LinkedIn [ha recentemente aggiornato le API da v1.0 a v2.0.](https://engineering.linkedin.com/blog/2018/12/developer-program-updates) Per eseguire la migrazione della configurazione esistente alla nuova configurazione, utilizzare le informazioni nelle sezioni seguenti per aggiornare gli elementi nel profilo tecnico.

### <a name="replace-items-in-the-metadata"></a>Sostituire gli elementi nei metadati

Nell'elemento **Metadata** esistente di **TechnicalProfile**, aggiornare i seguenti elementi **Item** da:

```XML
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v1/people/~:(id,first-name,last-name,email-address,headline)</Item>
<Item Key="scope">r_emailaddress r_basicprofile</Item>
```

Con:

```XML
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
<Item Key="scope">r_emailaddress r_liteprofile</Item>
```

### <a name="add-items-to-the-metadata"></a>Aggiungere elementi ai metadati

In **Metadati** del **ProfiloTecnico**aggiungere i seguenti elementi **Item:**

```XML
<Item Key="external_user_identity_claim_id">id</Item>
<Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
<Item Key="ResolveJsonPathsInJsonTokens">true</Item>
```

### <a name="update-the-outputclaims"></a>Aggiornare OutputClaimsUpdate the OutputClaims

**Nell'oggetto OutputClaims** esistente di **TechnicalProfile**, aggiornare i seguenti elementi **OutputClaim** da:

```XML
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
```

Con:

```XML
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
```

### <a name="add-new-outputclaimstransformation-elements"></a>Aggiungere nuovi elementi OutputClaimsTransformationAdd new OutputClaimsTransformation elements

In **OutputClaimsTransformations** di **TechnicalProfile**aggiungere i seguenti elementi **OutputClaimsTransformation:**

```XML
<OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
<OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
```

### <a name="define-the-new-claims-transformations-and-claim-type"></a>Definire le nuove trasformazioni attestazioni e il tipo di attestazioneDefine the new claims transformations and claim type

Nell'ultimo passaggio sono state aggiunte nuove trasformazioni delle attestazioni che devono essere definite. Per definire le trasformazioni delle attestazioni, aggiungerle all'elenco di **ClaimsTransformations**. Se nel file non è stato definito un elemento **ClaimsTransformations,** aggiungere gli elementi XML padre come illustrato di seguito. Le trasformazioni delle attestazioni richiedono inoltre un nuovo tipo di attestazione definito denominato **nullStringClaim**.

L'elemento **BuildingBlocks** deve essere aggiunto nella parte superiore del file. Vedere *TrustframeworkBase.xml* come esempio.

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

### <a name="obtain-an-email-address"></a>Ottenere un indirizzo e-mail

Come parte della migrazione di LinkedIn dalla versione 1.0 alla versione 2.0, è necessaria una chiamata aggiuntiva a un'altra API per ottenere l'indirizzo di posta elettronica. Se è necessario ottenere l'indirizzo di posta elettronica durante l'iscrizione, eseguire le operazioni seguenti:

1. Completare i passaggi precedenti per consentire ad Azure AD B2C di eseguire la federazione con LinkedIn per consentire all'utente di accedere. Come parte della federazione, Azure AD B2C riceve il token di accesso per LinkedIn.
2. Salvare il token di accesso LinkedIn in un'attestazione. [Vedere le istruzioni qui](idp-pass-through-custom.md).
3. Aggiungere il provider di attestazioni seguente che `/emailAddress` effettua la richiesta all'API di LinkedIn.Add the following claims provider that makes the request to LinkedIn's API. Per autorizzare questa richiesta, è necessario il token di accesso LinkedIn.

    ```XML
    <ClaimsProvider>
      <DisplayName>REST APIs</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="API-LinkedInEmail">
          <DisplayName>Get LinkedIn email</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
              <Item Key="ServiceUrl">https://api.linkedin.com/v2/emailAddress?q=members&amp;projection=(elements*(handle~))</Item>
              <Item Key="AuthenticationType">Bearer</Item>
              <Item Key="UseClaimAsBearerToken">identityProviderAccessToken</Item>
              <Item Key="SendClaimsIn">Url</Item>
              <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
          </Metadata>
          <InputClaims>
              <InputClaim ClaimTypeReferenceId="identityProviderAccessToken" />
          </InputClaims>
          <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="elements[0].handle~.emailAddress" />
          </OutputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Aggiungere il passaggio dell'orchestrazione seguente nel percorso utente, in modo che il provider di attestazioni API venga attivato quando un utente accede utilizzando LinkedIn.Add the following orchestration step into your user journey, so that the API claims provider is triggered when a user signs in using LinkedIn. Assicurarsi di `Order` aggiornare il numero in modo appropriato. Aggiungere questo passaggio immediatamente dopo il passaggio di orchestrazione che attiva il profilo tecnico LinkedIn.Add this step immediately after the orchestration step that triggers the LinkedIn technical profile.

    ```XML
    <!-- Extra step for LinkedIn to get the email -->
    <OrchestrationStep Order="3" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Value>linkedin.com</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="GetEmail" TechnicalProfileReferenceId="API-LinkedInEmail" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

Ottenere l'indirizzo e-mail da LinkedIn durante l'iscrizione è facoltativo. Se si sceglie di non ottenere l'e-mail da LinkedIn ma richiederne una durante l'iscrizione, l'utente deve immettere manualmente l'indirizzo e-mail e convalidarlo.

Per un esempio completo di criteri che utilizzano il provider di identità LinkedIn, vedere Custom [Policy Starter Pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/linkedin-identity-provider).

<!-- Links - EXTERNAL -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
