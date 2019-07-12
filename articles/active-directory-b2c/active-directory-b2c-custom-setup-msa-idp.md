---
title: Aggiungere l'Account Microsoft (MSA) come provider di identità tramite criteri personalizzati in Azure Active Directory B2C
description: Esempio dell'uso di Microsoft come provider di identità tramite il protocollo OpenID Connect (OIDC).
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 9ae944a9d587e71c4be83bd524cf3875a7b52dd1
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67654159"
---
# <a name="set-up-sign-in-with-a-microsoft-account-using-custom-policies-in-azure-active-directory-b2c"></a>Configurare l'accesso con un account Microsoft usando criteri personalizzati in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Questo articolo illustra come consentire agli utenti l'accesso da un account Microsoft usando [criteri personalizzati](active-directory-b2c-overview-custom.md) in Azure Active Directory (Azure AD) B2C.

## <a name="prerequisites"></a>Prerequisiti

- Completare le procedure illustrate in [Introduzione ai criteri personalizzati in Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).
- Se non si ha già un account Microsoft, crearne uno all'indirizzo [https://www.live.com/](https://www.live.com/).

## <a name="add-an-application"></a>Aggiungere un'applicazione

Per abilitare l'accesso per gli utenti con un account Microsoft, è necessario registrare un'applicazione nel tenant di Azure AD. Il tenant di Azure AD non corrisponde al tenant di Azure AD B2C.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Assicurarsi di usare la directory contenente il tenant di Azure AD facendo clic sul **filtro per directory e sottoscrizione** nel menu in alto e scegliendo tale directory.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Registrazioni per l'app**.
1. Selezionare **Nuova registrazione**.
1. Immettere un **nome** per l'applicazione. Ad esempio, *MSAapp1*.
1. Sotto **tipi di account supportati**, selezionare **gli account in qualsiasi directory dell'organizzazione e gli account Microsoft personali (ad esempio Skype, Xbox, Outlook.com)** .
1. Sotto **URI di reindirizzamento (facoltativo)** , selezionare **Web** e immettere `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` nella casella di testo. Sostituire `your-tenant-name` con il nome del tenant Azure AD B2C.
1. Selezionare **registrare**
1. Record di **ID applicazione (client)** illustrato nella pagina di panoramica dell'applicazione. È necessario quando si configura il provider di attestazioni in una sezione successiva.
1. Selezionare **i segreti e certificati**
1. Fare clic su **nuovo segreto client**
1. Immettere un **Description** per la chiave privata, ad esempio *segreto Client dell'applicazione di account del servizio gestito*, quindi fare clic su **Add**.
1. Registrare la password dell'applicazione visualizzata nei **valore** colonna. Questo valore verrà usato nella sezione successiva.

## <a name="create-a-policy-key"></a>Creare una chiave dei criteri

Ora che è stato creato l'applicazione nel tenant di Azure AD, è necessario archiviare segreto client dell'applicazione nel tenant di Azure AD B2C.

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Assicurarsi che si usa la directory che contiene il tenant di Azure AD B2C. Selezionare il **Directory e sottoscrizione filtro** nel menu in alto e scegliere la directory che contiene il tenant.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
1. Nella pagina Panoramica selezionare **Framework dell'esperienza di gestione delle identità**.
1. Selezionare **Chiavi dei criteri** e quindi selezionare **Aggiungi**.
1. Per **Opzioni** scegliere `Manual`.
1. Immettere un **nome** per la chiave dei criteri. Ad esempio `MSASecret`. Verrà aggiunto automaticamente il prefisso `B2C_1A_` al nome della chiave.
1. Nelle **segreto**, immettere il segreto client registrato nella sezione precedente.
1. In **Uso chiave** selezionare `Signature`.
1. Fare clic su **Create**(Crea).

## <a name="add-a-claims-provider"></a>Aggiungere un provider di attestazioni

Per abilitare gli utenti di accedere usando un account Microsoft, è necessario definire l'account come un provider di attestazioni in grado di comunicare con Azure AD B2C tramite un endpoint. L'endpoint offre un set di attestazioni che vengono usate da Azure AD B2C per verificare se un utente specifico è stato autenticato.

È possibile definire Azure AD come provider di attestazioni aggiungendo l'elemento **ClaimsProvider** nel file di estensione dei criteri.

1. Aprire il *trustframeworkextensions. XML* file dei criteri.
1. Trovare l'elemento **ClaimsProviders**. Se non esiste, aggiungerlo nell'elemento radice.
1. Aggiungere un nuovo **ClaimsProvider** come illustrato di seguito:

    ```xml
    <ClaimsProvider>
      <Domain>live.com</Domain>
      <DisplayName>Microsoft Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="MSA-OIDC">
          <DisplayName>Microsoft Account</DisplayName>
          <Protocol Name="OpenIdConnect" />
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
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_MSASecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="email" />
          </OutputClaims>
          <OutputClaimsTransformations>
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

1. Sostituire il valore di **client_id** con l'applicazione di Azure AD *ID applicazione (client)* annotato in precedenza.
1. Salvare il file.

I criteri sono ora configurati in modo che Azure AD B2C possa comunicare con l'applicazione di account Microsoft in Azure AD.

### <a name="upload-the-extension-file-for-verification"></a>Caricare il file di estensione per la verifica

Prima di continuare, caricare i criteri modificati da selezionare per confermare non siano presenti problemi fino a questo momento.

1. Passare al tenant di Azure AD B2C nel portale di Azure e seleziona **Framework di esperienza di identità**.
1. Nel **criteri personalizzati** pagina, selezionare **carica il criterio personalizzato**.
1. Abilitare **Sovrascrivi il criterio se esistente** e quindi cercare e selezionare il file *TrustFrameworkExtensions.xml*.
1. Fare clic su **Upload**.

Se non vengono visualizzati errori nel portale, passare alla sezione successiva.

## <a name="register-the-claims-provider"></a>Registrare il provider di attestazioni

A questo punto, aver configurato il provider di identità, ma non è ancora disponibile in tutte le schermate di iscrizione o accesso. Per renderlo disponibile, creare un duplicato di un percorso utente modello esistente e quindi modificarlo in modo che includa anche il provider di identità di account Microsoft.

1. Aprire il file *TrustFrameworkBase.xml* dallo starter pack.
1. Trovare e copiare l'intero contenuto dell'elemento **UserJourney** che include `Id="SignUpOrSignIn"`.
1. Aprire *TrustFrameworkExtensions.xml* e trovare l'elemento **UserJourneys**. Se l'elemento non esiste, aggiungerne uno.
1. Incollare l'intero contenuto dell'elemento **UserJourney** copiato come figlio dell'elemento **UserJourneys**.
1. Rinominare l'ID del percorso utente. Ad esempio `SignUpSignInMSA`.

### <a name="display-the-button"></a>Visualizzare il pulsante

L'elemento **ClaimsProviderSelection** è analogo a un pulsante per il provider di identità in una schermata di iscrizione o accesso. Se si aggiunge un **ClaimsProviderSelection** (elemento) per un account Microsoft, un nuovo pulsante viene visualizzata quando un utente apre la pagina.

1. Nel file *TrustFrameworkExtensions.xml* trovare l'elemento **OrchestrationStep** che include `Order="1"` nel percorso utente creato.
1. In **ClaimsProviderSelects** aggiungere l'elemento riportato di seguito. Impostare **TargetClaimsExchangeId** su un valore appropriato, ad esempio `MicrosoftAccountExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Collegare il pulsante a un'azione

Ora che il pulsante è stato posizionato, è necessario collegarlo a un'azione. L'azione, in questo caso, sia per Azure AD B2C comunicare con un account Microsoft per ricevere un token.

1. Trovare l'elemento **OrchestrationStep** che include `Order="2"` nel percorso utente.
1. Aggiungere l'elemento **ClaimsExchange** seguente assicurandosi di usare per ID lo stesso valore usato per **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    ```

    Aggiornare il valore della **TechnicalProfileReferenceId** corrispondere a quello delle `Id` valore nel **TechnicalProfile** elemento del provider di attestazioni aggiunti in precedenza. Ad esempio `MSA-OIDC`.

1. Salvare il file *TrustFrameworkExtensions.xml* e caricarlo di nuovo per la verifica.

## <a name="create-an-azure-ad-b2c-application"></a>Creare un'applicazione Azure AD B2C

La comunicazione con Azure AD B2C avviene tramite un'applicazione creata nel tenant di Azure AD B2C. Questa sezione elenca i passaggi facoltativi che è possibile completare per creare un'applicazione di test, se non è già stato fatto.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Assicurarsi che si usa la directory che contiene il tenant di Azure AD B2C. Selezionare il **Directory e sottoscrizione filtro** nel menu in alto e scegliere la directory che contiene il tenant.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **Applicazioni** e quindi **Aggiungi**.
1. Immettere un nome per l'applicazione, ad esempio *apptest1*.
1. Per **App Web/API Web** selezionare `Yes` e quindi immettere `https://jwt.ms` in **URL di risposta**.
1. Fare clic su **Create**(Crea).

## <a name="update-and-test-the-relying-party-file"></a>Aggiornare e testare il file di relying party

Aggiornare il file della relying party (RP) che avvierà il percorso utente appena creato.

1. Creare una copia di *SignUpOrSignIn.xml* nella directory di lavoro e rinominare la copia. Ad esempio, assegnare il nome *SignUpSignInMSA.xml*.
1. Aprire il nuovo file e aggiornare il valore dell'attributo **PolicyId** per **TrustFrameworkPolicy** con un valore univoco. Ad esempio `SignUpSignInMSA`.
1. Aggiornare il valore di **PublicPolicyUri** con l'URI dei criteri. Ad esempio, `http://contoso.com/B2C_1A_signup_signin_msa`
1. Aggiornare il valore della **ReferenceId** attributo **DefaultUserJourney** per corrispondere all'ID del percorso utente creato precedenti (SignUpSignInMSA).
1. Salvare le modifiche, caricare il file e quindi selezionare i nuovi criteri nell'elenco.
1. Assicurarsi che tale applicazione di Azure AD B2C che è stato creato nella sezione precedente (o, completare i prerequisiti, ad esempio *App Web 1* oppure *testapp1*) viene selezionato nel **selezionare Application** campo e quindi testarla facendo **Esegui adesso**.
1. Selezionare il **Account Microsoft** pulsante ed eseguire l'accesso.

    Se l'operazione di accesso ha esito positivo, si verrà reindirizzati al `jwt.ms` che consente di visualizzare la decodifica Token, simile a:

    ```json
    {
      "typ": "JWT",
      "alg": "RS256",
      "kid": "<key-ID>"
    }.{
      "exp": 1562365200,
      "nbf": 1562361600,
      "ver": "1.0",
      "iss": "https://your-b2c-tenant.b2clogin.com/10000000-0000-0000-0000-000000000000/v2.0/",
      "sub": "20000000-0000-0000-0000-000000000000",
      "aud": "30000000-0000-0000-0000-000000000000",
      "acr": "b2c_1a_signupsigninmsa",
      "nonce": "defaultNonce",
      "iat": 1562361600,
      "auth_time": 1562361600,
      "idp": "live.com",
      "name": "Azure User",
      "email": "azureuser@contoso.com",
      "tid": "6fc3b573-7b38-4c0c-b627-2e8684f6c575"
    }.[Signature]
    ```
