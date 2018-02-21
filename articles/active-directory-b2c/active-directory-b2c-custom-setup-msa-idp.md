---
title: "Azure Active Directory B2C: Aggiungere un account Microsoft come provider di identità tramite criteri personalizzati"
description: "Esempio di utilizzo di Microsoft come provider di identità basato sul protocollo OpenID Connect (OIDC)"
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 08/04/2017
ms.author: yoelh
ms.openlocfilehash: cdc77d093358fa15bb1acbc9ba6b1867bae062f8
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-add-microsoft-account-msa-as-an-identity-provider-using-custom-policies"></a>Azure Active Directory B2C: Aggiungere un account Microsoft come provider di identità tramite criteri personalizzati

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Questo articolo illustra come consentire agli utenti di accedere da un account Microsoft tramite l'utilizzo di [criteri personalizzati](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>prerequisiti
Completare la procedura descritta nell'articolo [Introduzione ai criteri personalizzati](active-directory-b2c-get-started-custom.md).

La procedura include i passaggi seguenti:

1.  Creazione di un'applicazione di account Microsoft.
2.  Aggiunta della chiave dell'applicazione di account Microsoft in Azure AD B2C
3.  Aggiunta di un provider di attestazioni nei criteri
4.  Registrazione del provider di attestazioni dell'account Microsoft in un percorso utente
5.  Caricamento dei criteri in un tenant di Azure AD B2C e test dei criteri

## <a name="create-a-microsoft-account-application"></a>Creare un'applicazione di account Microsoft
Per usare un account Microsoft come provider di identità in Azure Active Directory (Azure AD) B2C, è necessario creare un'applicazione di account Microsoft e inserire i parametri corretti. È necessario un account Microsoft. Se non si ha un account, visitare il sito [https://www.live.com/](https://www.live.com/).

1.  Entrare nel [portale di registrazione delle applicazioni Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) e accedere con le credenziali del proprio account Microsoft.
2.  Fare clic su **Aggiungi un'app**.

    ![Account Microsoft - Aggiungere un'app](media/active-directory-b2c-custom-setup-ms-account-idp/msa-add-new-app.png)

3.  Specificare un **Nome** per l'applicazione e un **Indirizzo di posta elettronica di contatto**, deselezionare l'opzione **Informazioni per l'utilizzo iniziale** e fare clic su **Crea**.

    ![Account Microsoft - Registrare l'applicazione](media/active-directory-b2c-custom-setup-ms-account-idp/msa-app-name.png)

4.  Copiare il valore di **ID applicazione**. È necessario per configurare un account Microsoft come provider di identità nel tenant.

    ![Account Microsoft - Copiare il valore di ID applicazione](media/active-directory-b2c-custom-setup-ms-account-idp/msa-app-id.png)

5.  Fare clic su **Aggiungi piattaforma**

    ![Account Microsoft - Aggiungi piattaforma](media/active-directory-b2c-custom-setup-ms-account-idp/msa-add-platform.png)

6.  Scegliere **Web** dall'elenco di piattaforme.

    ![Account Microsoft - Scegliere Web dall'elenco di piattaforme](media/active-directory-b2c-custom-setup-ms-account-idp/msa-web.png)

7.  Immettere `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` nel campo **URI di reindirizzamento** . Sostituire **{tenant}** con il nome del tenant, ad esempio contosob2c.onmicrosoft.com.

    ![Account Microsoft - Impostare gli URL di reindirizzamento](media/active-directory-b2c-custom-setup-ms-account-idp/msa-redirect-url.png)

8.  Fare clic su **Genera nuova password** nella sezione **Segreti applicazione**. Copiare la nuova password visualizzata sullo schermo. È necessario per configurare un account Microsoft come provider di identità nel tenant. La password è una credenziale di sicurezza importante.

    ![Account Microsoft - Genera nuova password](media/active-directory-b2c-custom-setup-ms-account-idp/msa-generate-new-password.png)

    ![Account Microsoft - Copiare la nuova password](media/active-directory-b2c-custom-setup-ms-account-idp/msa-new-password.png)

9.  Selezionare la casella **Supporto Live SDK** nella sezione **Opzioni avanzate**. Fare clic su **Save**.

    ![Account Microsoft - Supporto Live SDK](media/active-directory-b2c-custom-setup-ms-account-idp/msa-live-sdk-support.png)

## <a name="add-the-microsoft-account-application-key-to-azure-ad-b2c"></a>Aggiungere la chiave dell'applicazione di account Microsoft in Azure AD B2C
La federazione con account Microsoft richiede un segreto client per consentire all'account Microsoft di considerare attendibile Azure AD B2C per conto dell'applicazione. È necessario archiviare il segreto dell'applicazione di account Microsoft nel tenant di Azure AD B2C:   

1.  Passare al tenant di Azure AD B2C e selezionare **B2C Settings** (Impostazioni B2C) > **Framework dell'esperienza di gestione delle identità**
2.  Selezionare **Chiavi dei criteri** per visualizzare le chiavi disponibili nel tenant.
3.  Fare clic su **+Aggiungi**.
4.  Per **Opzioni** usare **Manuale**.
5.  Per **Nome** usare `MSASecret`.  
    È possibile che il prefisso `B2C_1A_` venga aggiunto automaticamente.
6.  Nella casella **Segreto** immettere il segreto dell'applicazione Microsoft da https://apps.dev.microsoft.com
7.  Per **Uso chiave** usare **Firma**.
8.  Fare clic su **Crea**
9.  Confermare di avere creato la chiave `B2C_1A_MSASecret`.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>Aggiungere un provider di attestazioni nei criteri di estensione
Per consentire agli utenti di accedere con un account Microsoft, è necessario definire l'account Microsoft come provider di attestazioni. In altre parole, è necessario specificare un endpoint con cui comunichi Azure AD B2C. L'endpoint offre un set di attestazioni che vengono usate da Azure AD B2C per verificare se un utente specifico è stato autenticato.

Definire l'account Microsoft come provider di attestazioni aggiungendo il nodo `<ClaimsProvider>` nel file dei criteri di estensione:

1.  Aprire il file dei criteri di estensione (TrustFrameworkExtensions.xml) dalla directory di lavoro. Se occorre un editor XML, provare [Visual Studio Code](https://code.visualstudio.com/download), un editor multipiattaforma leggero.
2.  Trovare la sezione `<ClaimsProviders>`
3.  Aggiungere il frammento XML seguente nell'elemento `ClaimsProviders`:

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
        <Item Key="client_id">Your Microsoft application client id</Item>
        </Metadata>
    <CryptographicKeys>
        <Key Id="client_secret" StorageReferenceId="B2C_1A_MSASecret" />
    </CryptographicKeys>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
        <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="sub" />
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

4.  Sostituire il valore `client_id` con l'ID client dell'applicazione di account Microsoft

5.  Salvare il file.

## <a name="register-the-microsoft-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>Registrare il provider di attestazioni dell'account Microsoft in un percorso utente di registrazione o di accesso

Il provider di identità è a questo punto configurato, ma non è disponibile in alcuna delle schermate di iscrizione/accesso. È necessario ora aggiungere il provider di identità dell'account Microsoft al percorso utente `SignUpOrSignIn` dell'utente. Per renderlo disponibile, si crea un duplicato di un modello di processo utente esistente  e si aggiunge il provider di identità dell'account Microsoft:

> [!NOTE]
>
>Se in precedenza l'elemento `<UserJourneys>` è stato copiato dal file di base dei criteri al file di estensione `TrustFrameworkExtensions.xml`, è possibile ignorare questa sezione.

1.  Aprire il file di base dei criteri, ad esempio TrustFrameworkBase.xml.
2.  Trovare l'elemento `<UserJourneys>` e copiare l'intero contenuto del nodo `<UserJourneys>`.
3.  Aprire il file di estensione, ad esempio TrustFrameworkExtensions.xml, e trovare l'elemento `<UserJourneys>`. Se l'elemento non esiste, aggiungerne uno.
4.  Incollare l'intero contenuto del nodo `<UserJournesy>` copiato come figlio dell'elemento `<UserJourneys>`.

### <a name="display-the-button"></a>Visualizzare il pulsante
L'elemento `<ClaimsProviderSelections>` definisce l'elenco delle opzioni di selezione del provider di attestazioni e il relativo ordine.  L'elemento `<ClaimsProviderSelection>` è analogo a un pulsante del provider di identità in una pagina di registrazione/accesso. Se si aggiunge un elemento `<ClaimsProviderSelection>` per l'account Microsoft, viene visualizzato un nuovo pulsante quando un utente apre la pagina. Per aggiungere questo elemento:

1.  Trovare il nodo `<UserJourney>` che include `Id="SignUpOrSignIn"` nel percorso utente appena copiato.
2.  Passare al nodo `<OrchestrationStep>` che include `Order="1"`
3.  Aggiungere il frammento XML seguente nel nodo `<ClaimsProviderSelections>`:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="MSAExchange" />
```

### <a name="link-the-button-to-an-action"></a>Collegare il pulsante a un'azione
Ora che il pulsante è stato posizionato, è necessario collegarlo a un'azione. L'azione, in questo caso, consiste nel far comunicare Azure AD B2C con l'account Microsoft per ricevere un token. È possibile farlo collegando il profilo tecnico per il provider di attestazioni dell'account Microsoft:

1.  Trovare l'oggetto `<OrchestrationStep>` che include `Order="2"` nel nodo `<UserJourney>`.
2.  Aggiungere il frammento XML seguente nel nodo `<ClaimsExchanges>`:

```xml
<ClaimsExchange Id="MSAExchange" TechnicalProfileReferenceId="MSA-OIDC" />
```

> [!NOTE]
>
>   * Verificare che `Id` sia impostato sullo stesso valore di `TargetClaimsExchangeId` riportato nella sezione precedente
>   * Verificare che l'ID `TechnicalProfileReferenceId` sia impostato sul profilo tecnico creato in precedenza (MSA-OIDC).

## <a name="upload-the-policy-to-your-tenant"></a>Caricare i criteri nel tenant
1.  Nel [portale di Azure](https://portal.azure.com) passare al [contesto del tenant di Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) e aprire il pannello **Azure AD B2C**.
2.  Fare clic su **Framework dell'esperienza di gestione delle identità**.
3.  Aprire il pannello **Tutti i criteri**.
4.  Selezionare **Carica criteri**.
5.  Selezionare la casella **Sovrascrivi il criterio se esistente**.
6.  **Caricare** TrustFrameworkExtensions.xml e assicurarsi che non presenti errori di convalida

## <a name="test-the-custom-policy-by-using-run-now"></a>Testare i criteri personalizzati tramite Esegui adesso

1.  Aprire **Impostazioni di Azure AD B2C** e passare a **Framework dell'esperienza di gestione delle identità**.
> [!NOTE]
>
>Il comando **Esegui adesso** richiede che nel tenant sia preregistrata almeno un'applicazione. Per informazioni su come registrare le applicazioni, vedere l'articolo di [introduzione](active-directory-b2c-get-started.md) ad Azure AD B2C o l'articolo relativo alla [registrazione delle applicazioni](active-directory-b2c-app-registration.md).
2.  Aprire **B2C_1A_signup_signin**, i criteri personalizzati dalla relying party caricati in precedenza. Selezionare **Esegui adesso**.
3.  Dovrebbe essere possibile accedere usando un account Microsoft.

## <a name="optional-register-the-microsoft-account-claims-provider-to-profile-edit-user-journey"></a>[Facoltativo] Registrare il provider di attestazioni dell'account Microsoft nel percorso utente Profile-Edit
È possibile aggiungere il provider di identità dell'account Microsoft anche al percorso utente `ProfileEdit` dell'utente. Per renderlo disponibile, ripetere gli ultimi due passaggi:

### <a name="display-the-button"></a>Visualizzare il pulsante
1.  Aprire il file di estensione dei criteri, ad esempio TrustFrameworkExtensions.xml.
2.  Trovare il nodo `<UserJourney>` che include `Id="ProfileEdit"` nel percorso utente appena copiato.
3.  Passare al nodo `<OrchestrationStep>` che include `Order="1"`
4.  Aggiungere il frammento XML seguente nel nodo `<ClaimsProviderSelections>`:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="MSAExchange" />
```

### <a name="link-the-button-to-an-action"></a>Collegare il pulsante a un'azione
1.  Trovare l'oggetto `<OrchestrationStep>` che include `Order="2"` nel nodo `<UserJourney>`.
2.  Aggiungere il frammento XML seguente nel nodo `<ClaimsExchanges>`:

```xml
<ClaimsExchange Id="MSAExchange" TechnicalProfileReferenceId="MSA-OIDC" />
```

### <a name="test-the-custom-profile-edit-policy-by-using-run-now"></a>Testare i criteri Profile-Edit personalizzati tramite Esegui adesso
1.  Aprire **Impostazioni di Azure AD B2C** e passare a **Framework dell'esperienza di gestione delle identità**.
2.  Aprire **B2C_1A_ProfileEdit**, i criteri personalizzati dalla relying party caricati in precedenza. Selezionare **Esegui adesso**.
3.  Dovrebbe essere possibile accedere usando un account Microsoft.

## <a name="download-the-complete-policy-files"></a>Scaricare i file dei criteri completi
Facoltativo: anziché usare i file di esempio, per creare lo scenario è consigliabile usare file di criteri personalizzati dopo aver completato la procedura Introduzione ai criteri personalizzati.  [File dei criteri di esempio di riferimento](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-msa-app)
