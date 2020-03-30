---
title: Configurare l'accesso con un account Microsoft usando criteri personalizzati
titleSuffix: Azure AD B2C
description: Come utilizzare criteri personalizzati per abilitare l'account Microsoft (MSA) come provider di identità utilizzando il protocollo OpenID Connect (OIDC).
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b7d8fbddc86c0d05d7b0d4ce46cb06c5fc92a2cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188118"
---
# <a name="set-up-sign-in-with-a-microsoft-account-using-custom-policies-in-azure-active-directory-b2c"></a>Configurare l'accesso con un account Microsoft usando criteri personalizzati in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Questo articolo illustra come abilitare l'accesso per gli utenti da un account Microsoft usando [criteri personalizzati](custom-policy-overview.md) in Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Prerequisiti

- Completare le procedure illustrate in [Introduzione ai criteri personalizzati in Azure Active Directory B2C](custom-policy-get-started.md).
- Se non si dispone già di un [https://www.live.com/](https://www.live.com/)account Microsoft, crearne uno all'indirizzo .

## <a name="register-an-application"></a>Registrare un'applicazione

Per abilitare l'accesso per gli utenti con un account Microsoft, è necessario registrare un'applicazione all'interno del tenant di Azure AD. Il tenant di Azure AD non corrisponde al tenant di Azure AD B2C.

1. Accedere al [portale](https://portal.azure.com)di Azure .
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD selezionando il filtro **Directory e sottoscrizione** nel menu in alto e scegliendo la directory che contiene il tenant di Azure AD.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Registrazioni per l'app**.
1. Selezionare **Nuova registrazione**.
1. Immettere un **nome** per l'applicazione. Ad esempio, *MSAapp1*.
1. In **Tipi di account supportati**selezionare Account in qualsiasi directory **dell'organizzazione e account Microsoft personali (ad esempio Skype, Xbox, Outlook.com).**
1. In **URI di reindirizzamento (facoltativo)** selezionare **Web** e immettere `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` nella casella di testo. Sostituire `your-tenant-name` con il nome del tenant B2C di Azure AD.
1. Seleziona **Registrazione**
1. Registrare **l'ID applicazione (client)** visualizzato nella pagina Panoramica dell'applicazione. Questa operazione è necessaria quando si configura il provider di attestazioni in una sezione successiva.
1. Selezionare **Certificati & segreti**
1. Fare clic su **Nuovo segreto client**
1. Immettere una **Descrizione** per il segreto, ad esempio *MSA Application Client Secret*, quindi fare clic su **Aggiungi**.
1. Registrare la password dell'applicazione visualizzata nella colonna **Valore.** Questo valore viene utilizzato nella sezione successiva.

## <a name="configuring-optional-claims"></a>Configurazione di attestazioni facoltative

Se si vuole `family_name` ottenere `given_name` le attestazioni e di Azure AD, è possibile configurare attestazioni facoltative per l'applicazione nell'interfaccia utente del portale di Azure o nel manifesto dell'applicazione. Per altre informazioni, vedere [Come fornire attestazioni facoltative all'app Azure AD.](../active-directory/develop/active-directory-optional-claims.md)

1. Accedere al [portale](https://portal.azure.com)di Azure . Cercare e selezionare **Azure Active Directory**.
1. Nella sezione **Gestisci** selezionare **Registrazioni app**.
1. Selezionare l'applicazione per cui si desidera configurare attestazioni facoltative nell'elenco.
1. Nella sezione **Gestisci** selezionare **Configurazione token (anteprima).**
1. Selezionare **Aggiungi attestazione facoltativa**.
1. Selezionare il tipo di token che si desidera configurare.
1. Selezionare le attestazioni facoltative da aggiungere.
1. Fare clic su **Aggiungi**.

## <a name="create-a-policy-key"></a>Creare una chiave dei criteri

Dopo aver creato l'applicazione nel tenant di Azure AD, è necessario archiviare il segreto client dell'applicazione nel tenant B2C di Azure AD.

1. Accedere al [portale](https://portal.azure.com/)di Azure .
1. Assicurarsi di usare la directory che contiene il tenant B2C di Azure AD. Nel menu superiore, selezionare il filtro **Directory e sottoscrizione** e scegliere la directory che contiene il tenant.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
1. Nella pagina Panoramica selezionare **Framework dell'esperienza di gestione delle identità**.
1. Selezionare **Chiavi dei criteri** e quindi selezionare **Aggiungi**.
1. Per **Opzioni** scegliere `Manual`.
1. Immettere un **nome** per la chiave dei criteri. Ad esempio: `MSASecret`. Verrà aggiunto automaticamente il prefisso `B2C_1A_` al nome della chiave.
1. In **Segreto**immettere il segreto client registrato nella sezione precedente.
1. In **Uso chiave** selezionare `Signature`.
1. Fare clic su **Crea**.

## <a name="add-a-claims-provider"></a>Aggiungere un provider di attestazioni

Per consentire agli utenti di accedere usando un account Microsoft, è necessario definire l'account come provider di attestazioni con cui Azure AD B2C può comunicare tramite un endpoint. L'endpoint offre un set di attestazioni che vengono usate da Azure AD B2C per verificare se un utente specifico è stato autenticato.

È possibile definire Azure AD come provider di attestazioni aggiungendo l'elemento **ClaimsProvider** nel file di estensione dei criteri.

1. Aprire il file dei criteri *TrustFrameworkExtensions.xml.*
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

1. Sostituire il valore di **client_id** con *l'ID applicazione (client)* dell'applicazione Azure AD registrato in precedenza.
1. Salvare il file.

I criteri sono stati configurati in modo che Azure AD B2C sappia comunicare con l'applicazione dell'account Microsoft in Azure AD.

### <a name="upload-the-extension-file-for-verification"></a>Caricare il file di estensione per la verifica

Prima di continuare, carica i criteri modificati per confermare che finora non ha problemi.

1. Passare al tenant di Azure AD B2C nel portale di Azure e selezionare **Identity Experience Framework**.
1. Nella pagina **Criteri personalizzati** selezionare Carica **criteri personalizzati.**
1. Abilitare **Sovrascrivi il criterio se esistente** e quindi cercare e selezionare il file *TrustFrameworkExtensions.xml*.
1. Fare clic su **Carica**.

Se nel portale non vengono visualizzati errori, passare alla sezione successiva.

## <a name="register-the-claims-provider"></a>Registrare il provider di attestazioni

A questo punto, hai configurato il provider di identità, ma non è ancora disponibile in nessuna delle schermate di iscrizione o di accesso. Per renderlo disponibile, creare un duplicato di un percorso utente del modello esistente, quindi modificarlo in modo che disponga anche del provider di identità dell'account Microsoft.To make it available, create a duplicate of an existing template user journey, then modify it so that it also has the Microsoft account identity provider.

1. Aprire il file *TrustFrameworkBase.xml* dallo starter pack.
1. Trovare e copiare l'intero contenuto dell'elemento **UserJourney** che include `Id="SignUpOrSignIn"`.
1. Aprire *TrustFrameworkExtensions.xml* e trovare l'elemento **UserJourneys**. Se l'elemento non esiste, aggiungerne uno.
1. Incollare l'intero contenuto dell'elemento **UserJourney** copiato come figlio dell'elemento **UserJourneys**.
1. Rinominare l'ID del percorso utente. Ad esempio: `SignUpSignInMSA`.

### <a name="display-the-button"></a>Visualizzare il pulsante

L'elemento **ClaimsProviderSelection** è analogo a un pulsante per il provider di identità in una schermata di iscrizione o accesso. Se si aggiunge un **ClaimsProviderSelection** elemento per un account Microsoft, viene visualizzato un nuovo pulsante quando un utente atterra nella pagina.

1. Nel file *TrustFrameworkExtensions.xml* trovare l'elemento **OrchestrationStep **che include `Order="1"` nel percorso utente creato.
1. In **ClaimsProviderSelects** aggiungere l'elemento riportato di seguito. Impostare **TargetClaimsExchangeId** su un valore appropriato, ad esempio `MicrosoftAccountExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Collegare il pulsante a un'azione

Ora che il pulsante è stato posizionato, è necessario collegarlo a un'azione. L'azione, in questo caso, prevede che Azure AD B2C comunichi con un account Microsoft per ricevere un token.

1. Trovare l'elemento **OrchestrationStep** che include `Order="2"` nel percorso utente.
1. Aggiungere l'elemento **ClaimsExchange** seguente assicurandosi di usare per ID lo stesso valore usato per **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    ```

    Aggiornare il valore di **TechnicalProfileReferenceId** in modo che corrisponda a quello del `Id` valore nell'elemento **TechnicalProfile** del provider di attestazioni aggiunto in precedenza. Ad esempio: `MSA-OIDC`.

1. Salvare il file *TrustFrameworkExtensions.xml* e caricarlo di nuovo per la verifica.

## <a name="create-an-azure-ad-b2c-application"></a>Creare un'applicazione Azure AD B2C

La comunicazione con Azure AD B2C avviene tramite un'applicazione che viene eseguita nella registrazione nel tenant B2C. Questa sezione elenca i passaggi facoltativi che è possibile completare per creare un'applicazione di test, se non è già stato fatto.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Aggiornare e testare il file di relying party

Aggiornare il file della relying party (RP) che avvierà il percorso utente appena creato.

1. Creare una copia di *SignUpOrSignIn.xml* nella directory di lavoro e rinominare la copia. Ad esempio, assegnare il nome *SignUpSignInMSA.xml*.
1. Aprire il nuovo file e aggiornare il valore dell'attributo **PolicyId** per **TrustFrameworkPolicy** con un valore univoco. Ad esempio: `SignUpSignInMSA`.
1. Aggiornare il valore di **PublicPolicyUri** con l'URI dei criteri. Ad esempio, `http://contoso.com/B2C_1A_signup_signin_msa`
1. Aggiornare il valore dell'attributo **ReferenceId** in **DefaultUserJourney** in modo che corrisponda all'ID del percorso utente creato in precedenza (SignUpSignInMSA).
1. Salvare le modifiche, caricare il file e quindi selezionare i nuovi criteri nell'elenco.
1. Assicurarsi che l'applicazione Azure AD B2C creata nella sezione precedente (o completando i prerequisiti, ad esempio *webapp1* o *testapp1*) sia selezionata nel campo **Seleziona applicazione** e quindi testarla facendo clic su **Esegui ora**.
1. Seleziona il pulsante **Account Microsoft** e accedi.

    Se l'operazione di accesso ha esito `jwt.ms` positivo, si viene reindirizzati a cui viene visualizzato il token decodificato, simile al seguente:

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
