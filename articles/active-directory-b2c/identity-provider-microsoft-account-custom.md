---
title: Configurare l'accesso con un account Microsoft usando criteri personalizzati
titleSuffix: Azure AD B2C
description: Informazioni su come usare i criteri personalizzati per abilitare l'account Microsoft (MSA) come provider di identità usando il protocollo OpenID Connect (OIDC).
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 282d60b1894ffa186a6b5b6b5630aefa9e961572
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96345134"
---
# <a name="set-up-sign-in-with-a-microsoft-account-using-custom-policies-in-azure-active-directory-b2c"></a>Configurare l'accesso con un account Microsoft usando criteri personalizzati in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Questo articolo illustra come consentire agli utenti l'accesso da un account Microsoft usando [criteri personalizzati](custom-policy-overview.md) in Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Prerequisiti

- Completare le procedure illustrate in [Introduzione ai criteri personalizzati in Azure Active Directory B2C](custom-policy-get-started.md).
- Se non si ha già un account Microsoft, crearne uno all'indirizzo [https://www.live.com/](https://www.live.com/).

## <a name="register-an-application"></a>Registrare un'applicazione

Per consentire agli utenti di accedere con un account Microsoft, è necessario registrare un'applicazione dall'interno del tenant di Azure AD. Il tenant di Azure AD non corrisponde al tenant di Azure AD B2C.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD. A tale scopo, selezionare il filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant di Azure AD.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Registrazioni per l'app**.
1. Selezionare **Nuova registrazione**.
1. Immettere un **nome** per l'applicazione. Ad esempio, *MSAapp1*.
1. In **Tipi di account supportati** selezionare **Account in qualsiasi directory dell'organizzazione (qualsiasi directory di Azure AD - Multi-tenant) e account Microsoft personali (ad esempio, Skype, Xbox)** .
1. In **URI di reindirizzamento (facoltativo)** selezionare **Web** e immettere `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/oauth2/authresp` nella casella di testo. Sostituire `<tenant-name>` con il nome del tenant di Azure AD B2C.
1. Selezionare **Registra**
1. Annotare **l'ID applicazione (client)** visualizzato nella pagina Panoramica dell'applicazione. Questa operazione è necessaria quando si configura il provider di attestazioni nella sezione successiva.
1. Selezionare **Certificati e segreti**
1. Fare clic su **Nuovo segreto client**
1. Immettere una **Descrizione** per il segreto, ad esempio *Segreto client dell'applicazione MSA*, quindi fare clic su **Aggiungi**.
1. Annotare la password dell'applicazione visualizzata nella colonna **Valore**. Questo valore verrà usato nella sezione successiva.

## <a name="configuring-optional-claims"></a>Configurazione di attestazioni facoltative

Se si desidera ottenere le attestazioni `family_name` e `given_name` da Azure AD, è possibile configurare attestazioni facoltative per l'applicazione nell'interfaccia utente del portale di Azure o nel manifesto dell'applicazione. Per altre informazioni vedere [Procedura: fornire attestazioni facoltative all'app Azure AD](../active-directory/develop/active-directory-optional-claims.md).

1. Accedere al [portale di Azure](https://portal.azure.com). Cercare e selezionare **Azure Active Directory**.
1. Nella sezione **Gestisci** selezionare **Registrazioni app**.
1. Selezionare l'applicazione per cui si vogliono configurare le attestazioni facoltative nell'elenco.
1. Nella sezione **Gestisci** selezionare **Configurazione del token (anteprima)** .
1. Selezionare **Aggiungi un'attestazione facoltativa**.
1. Selezionare il tipo di token da configurare.
1. Selezionare le attestazioni facoltative da aggiungere.
1. Scegliere **Aggiungi**.

## <a name="create-a-policy-key"></a>Creare una chiave dei criteri

Ora che l'applicazione è stata creata nel tenant di Azure AD, è necessario archiviare il segreto client dell'applicazione nel tenant di Azure AD B2C.

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e selezionare la directory che contiene il tenant.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
1. Nella pagina Panoramica selezionare **Framework dell'esperienza di gestione delle identità**.
1. Selezionare **Chiavi dei criteri** e quindi selezionare **Aggiungi**.
1. Per **Opzioni** scegliere `Manual`.
1. Immettere un **nome** per la chiave dei criteri. Ad esempio: `MSASecret`. Verrà aggiunto automaticamente il prefisso `B2C_1A_` al nome della chiave.
1. In **Segreto**, immettere il segreto client annotato nella sezione precedente.
1. In **Uso chiave** selezionare `Signature`.
1. Fare clic su **Crea**.

## <a name="add-a-claims-provider"></a>Aggiungere un provider di attestazioni

Per consentire agli utenti di accedere con un account Microsoft, è necessario definire l'account come provider di attestazioni con cui Azure AD B2C possa comunicare tramite un endpoint. L'endpoint offre un set di attestazioni che vengono usate da Azure AD B2C per verificare se un utente specifico è stato autenticato.

È possibile definire Azure AD come provider di attestazioni aggiungendo l'elemento **ClaimsProvider** nel file di estensione dei criteri.

1. Aprire il file dei criteri *TrustFrameworkExtensions.xml*.
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
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="client_id">Your Microsoft application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_MSASecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
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

1. Sostituire il valore di **client_id** con *l'ID applicazione (client)* dell'applicazione Azure AD annotato in precedenza.
1. Salvare il file.

A questo punto, i criteri sono stati configurati in modo che Azure AD B2C possa comunicare con l'applicazione account Microsoft in Azure AD.

### <a name="upload-the-extension-file-for-verification"></a>Caricare il file di estensione per la verifica

Prima di continuare, caricare i criteri modificati per verificare che finora non siano stati riscontrati problemi.

1. Passare al tenant di Azure AD B2C nel portale di Azure e selezionare **Framework dell'esperienza di gestione delle identità**.
1. Nella pagina **Criteri personalizzati** selezionare **Carica i criteri personalizzati**.
1. Abilitare **Sovrascrivi il criterio se esistente** e quindi cercare e selezionare il file *TrustFrameworkExtensions.xml*.
1. Fare clic su **Carica**.

Se nel portale non viene visualizzato alcun errore, passare alla sezione successiva.

## <a name="register-the-claims-provider"></a>Registrare il provider di attestazioni

A questo punto, il provider di identità è stato configurato, ma non è ancora disponibile nelle schermate di iscrizione o accesso. Per renderlo disponibile, creare un duplicato di un percorso utente modello esistente, quindi modificarlo in modo che includa anche il provider di identità dell'account Microsoft.

1. Aprire il file *TrustFrameworkBase.xml* dallo starter pack.
1. Trovare e copiare l'intero contenuto dell'elemento **UserJourney** che include `Id="SignUpOrSignIn"`.
1. Aprire *TrustFrameworkExtensions.xml* e trovare l'elemento **UserJourneys**. Se l'elemento non esiste, aggiungerne uno.
1. Incollare l'intero contenuto dell'elemento **UserJourney** copiato come figlio dell'elemento **UserJourneys**.
1. Rinominare l'ID del percorso utente. Ad esempio: `SignUpSignInMSA`.

### <a name="display-the-button"></a>Visualizzare il pulsante

L'elemento **ClaimsProviderSelection** è analogo a un pulsante per il provider di identità in una schermata di iscrizione o accesso. Se si aggiunge un elemento **ClaimsProviderSelection** per un account Microsoft, quando un utente apre la pagina viene visualizzato un nuovo pulsante.

1. Nel file *TrustFrameworkExtensions.xml* trovare l'elemento **OrchestrationStep** che include `Order="1"` nel percorso utente creato.
1. In **ClaimsProviderSelects** aggiungere l'elemento riportato di seguito. Impostare **TargetClaimsExchangeId** su un valore appropriato, ad esempio `MicrosoftAccountExchange`:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Collegare il pulsante a un'azione

Ora che il pulsante è stato posizionato, è necessario collegarlo a un'azione. L'azione, in questo caso, consiste nel far comunicare Azure AD B2C con un account Microsoft per ricevere un token.

1. Trovare l'elemento **OrchestrationStep** che include `Order="2"` nel percorso utente.
1. Aggiungere l'elemento **ClaimsExchange** seguente assicurandosi di usare per ID lo stesso valore usato per **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    ```

    Aggiornare il valore di **TechnicalProfileReferenceId** in modo che corrisponda a quello del valore `Id` nell'elemento **TechnicalProfile** del provider di attestazioni aggiunto in precedenza. Ad esempio: `MSA-OIDC`.

1. Salvare il file *TrustFrameworkExtensions.xml* e caricarlo di nuovo per la verifica.

## <a name="create-an-azure-ad-b2c-application"></a>Creare un'applicazione Azure AD B2C

La comunicazione con Azure AD B2C avviene tramite un'applicazione registrata nel tenant B2C. Questa sezione elenca i passaggi facoltativi che è possibile completare per creare un'applicazione di test, se non è già stato fatto.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Aggiornare e testare il file di relying party

Aggiornare il file della relying party (RP) che avvierà il percorso utente appena creato.

1. Creare una copia di *SignUpOrSignIn.xml* nella directory di lavoro e rinominare la copia. Ad esempio, assegnare il nome *SignUpSignInMSA.xml*.
1. Aprire il nuovo file e aggiornare il valore dell'attributo **PolicyId** per **TrustFrameworkPolicy** con un valore univoco. Ad esempio: `SignUpSignInMSA`.
1. Aggiornare il valore di **PublicPolicyUri** con l'URI dei criteri. Ad esempio, `http://contoso.com/B2C_1A_signup_signin_msa`
1. Aggiornare il valore dell'attributo **ReferenceId** in **DefaultUserJourney** in modo che corrisponda all'ID del percorso utente che è stato creato in precedenza (SignUpSignInMSA).
1. Salvare le modifiche, caricare il file e quindi selezionare i nuovi criteri nell'elenco.
1. Assicurarsi che sia selezionata l'applicazione Azure AD B2C creata nella sezione precedente (oppure completando i prerequisiti, ad esempio *webapp1* o *testapp1*) nel campo **Selezione applicazione**, quindi verificarla facendo clic su **Esegui**.
1. Selezionare il pulsante **account Microsoft** e accedere.

    Se l'operazione di accesso ha esito positivo, si viene reindirizzati a `jwt.ms`, che visualizza il token decodificato, simile a:

    ```json
    {
      "typ": "JWT",
      "alg": "RS256",
      "kid": "<key-ID>"
    }.{
      "exp": 1562365200,
      "nbf": 1562361600,
      "ver": "1.0",
      "iss": "https://<tenant-name>.b2clogin.com/10000000-0000-0000-0000-000000000000/v2.0/",
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
