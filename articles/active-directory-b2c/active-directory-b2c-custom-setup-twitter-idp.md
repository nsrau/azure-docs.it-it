---
title: "Azure Active Directory B2C: Aggiungere Twitter come provider di identità OAuth1 tramite criteri personalizzati"
description: "Esempio di uso di Twitter come provider di identità basato sul protocollo OAuth1"
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 10/02/2017
ms.author: yoelh
ms.openlocfilehash: 8d7bd2ed1cba62677a7eff19ba7018cd25a4345c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-add-twitter-as-an-oauth1-identity-provider-using-custom-policies"></a>Azure Active Directory B2C: Aggiungere Twitter come provider di identità OAuth1 tramite criteri personalizzati
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Questo articolo illustra come consentire agli utenti di accedere da un account Twitter tramite l'uso di [criteri personalizzati](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Prerequisiti
Completare la procedura descritta nell'articolo [Introduzione ai criteri personalizzati](active-directory-b2c-get-started-custom.md).

La procedura include i passaggi seguenti:

1.  Creazione di un'applicazione di account Twitter.
2.  Aggiunta della chiave dell'applicazione di account Twitter in Azure AD B2C
3.  Aggiunta di un provider di attestazioni nei criteri
4.  Registrazione del provider di attestazioni dell'account Twitter in un percorso utente
5.  Caricamento dei criteri in un tenant di Azure AD B2C e test dei criteri

## <a name="step-1-create-a-twitter-account-application"></a>Passaggio 1: Creare un'applicazione dell'account Twitter
Per usare Twitter come provider di identità in Azure Active Directory (Azure AD) B2C, si deve creare un'applicazione Twitter e inserire i parametri corretti. È possibile registrare un'applicazione Twitter qui: [https://twitter.com/signup](https://twitter.com/signup)

1.  Passare al sito Web [Twitter Developers](https://apps.twitter.com/) , accedere con le credenziali dell'account Twitter e quindi fare clic su **Create New App**(Crea nuova app).

    ![Account Twitter - Creazione di una nuova app](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app1.png)

2.  Digitare i valori per **Name** (Nome) e **Description** (Descrizione) per la nuova app. Incollare in `https://login.microsoftonline.com` come valore per **sito Web**. Quindi per **Callback URL** (URL callback) incollare `https://login.microsoftonline.com/te/{tenant}.onmicrosoft.com/oauth2/authresp`. Sostituire {tenant} con il nome del tenant, ad esempio contosob2c.onmicrosoft.com, e assicurarsi che sia in uso lo schema HTTPS. Nella parte inferiore della pagina, leggere e accettare le condizioni di utilizzo. Fare clic su **Create your Twitter application**.

    ![Account Twitter - Aggiungere una nuova app](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app2.png)

3.  Fare clic sulla scheda **Settings** (Impostazioni), selezionare **Allow this application to be used to sign in with Twitter** (Consenti l'uso dell'applicazione per l'accesso con Twitter) e quindi fare clic su **Update Settings** (Aggiorna impostazioni).
4.  Selezionare la scheda **Keys and Access Tokens** . Prendere nota dei valori di **Consumer Key (API Key)** (Chiave utente (chiave API)) e **Consumer secret (API Secret)** (Segreto utente (chiave privata API)).

    ![Account Twitter - Impostare le proprietà dell'applicazione](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app3.png)

> [!NOTE]
>
>Il Segreto consumer è un'importante credenziale di sicurezza. Non condividere questo valore con altri né distribuirlo con l'app.

## <a name="step-2-add-the-twitter-account-application-key-to-azure-ad-b2c"></a>Passaggio 2: Aggiungere la chiave dell'applicazione di account Twitter in Azure AD B2C
La federazione con account Twitter richiede un segreto client per consentire all'account Twitter di considerare attendibile Azure AD B2C per conto dell'applicazione. È necessario archiviare il segreto consumer dell'applicazione Twitter nel tenant di Azure AD B2C:  

1.  Passare al tenant di Azure AD B2C e selezionare **B2C Settings** (Impostazioni B2C) > **Framework dell'esperienza di gestione delle identità**
2.  Selezionare **Chiavi dei criteri** per visualizzare le chiavi disponibili nel tenant.
3.  Fare clic su **+Aggiungi**.
4.  Per **Opzioni** usare **Manuale**.
5.  Per **Nome** usare `TwitterSecret`.  
    È possibile che il prefisso `B2C_1A_` venga aggiunto automaticamente.
6.  Nella casella **Segreto** immettere il segreto dell'applicazione Microsoft da https://apps.dev.microsoft.com
7.  Per **Uso chiave** usare **Crittografia**.
8.  Fare clic su **Crea**
9.  Confermare di avere creato la chiave `B2C_1A_TwitterSecret`.

## <a name="step-3-add-a-claims-provider-in-your-extension-policy"></a>Passaggio 3: Aggiungere un provider di attestazioni nei criteri di estensione

Per consentire agli utenti di accedere con un account Twitter, è necessario definire l'account Twitter come provider di attestazioni. In altre parole, è necessario specificare degli endpoint con i quali Azure AD B2C comunichi. Gli endpoint offriranno un set di attestazioni che verranno usate da Azure AD B2C per verificare se un utente specifico è stato autenticato.

Definire Twitter come provider di attestazioni aggiungendo il nodo `<ClaimsProvider>` nel file dei criteri di estensione:

1.  Aprire il file dei criteri di estensione (TrustFrameworkExtensions.xml) dalla directory di lavoro. 
2.  Trovare la sezione `<ClaimsProviders>`
3.  Aggiungere il frammento XML seguente nel nodo `<ClaimsProviders>`:  

    ```xml
    <ClaimsProvider>
        <Domain>twitter.com</Domain>
        <DisplayName>Twitter</DisplayName>
        <TechnicalProfiles>
        <TechnicalProfile Id="Twitter-OAUTH1">
            <DisplayName>Twitter</DisplayName>
            <Protocol Name="OAuth1" />
            <Metadata>
            <Item Key="ProviderName">Twitter</Item>
            <Item Key="authorization_endpoint">https://api.twitter.com/oauth/authenticate</Item>
            <Item Key="access_token_endpoint">https://api.twitter.com/oauth/access_token</Item>
            <Item Key="request_token_endpoint">https://api.twitter.com/oauth/request_token</Item>
            <Item Key="ClaimsEndpoint">https://api.twitter.com/1.1/account/verify_credentials.json?include_email=true</Item>
            <Item Key="ClaimsResponseFormat">json</Item>
            <Item Key="client_id">Your Twitter application consumer key</Item>
            </Metadata>
            <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_TwitterSecret" />
            </CryptographicKeys>
            <InputClaims />
            <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="user_id" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
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

4.  Sostituire il valore `client_id` con la chiave consumer dell'applicazione account Twitter

5.  Salvare il file.

## <a name="step-4-register-the-twitter-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>Passaggio 4: Registrare il provider di attestazioni dell'account Twitter in un percorso utente di registrazione o di accesso
A questo punto, il provider di identità è stato configurato, ma non è disponibile in nessuna delle schermate di registrazione o di accesso. È necessario ora aggiungere il provider di identità dell'account Twitter al percorso utente `SignUpOrSignIn` dell'utente.

### <a name="step-41-make-a-copy-of-the-user-journey"></a>Passaggio 4.1: Fare una copia del percorso utente
Per renderlo disponibile, creare un duplicato di un modello di percorso utente esistente. Aggiungere quindi il provider di identità di Twitter:

> [!NOTE]
>
>Se l'elemento `<UserJourneys>` è stato copiato dal file di base dei criteri al file di estensione (TrustFrameworkExtensions.xml), è possibile ignorare la sezione successiva.

1.  Aprire il file di base dei criteri, ad esempio TrustFrameworkBase.xml.
2.  Trovare l'elemento `<UserJourneys>` e copiare l'intero contenuto del nodo `<UserJourneys>`.
3.  Aprire il file di estensione, ad esempio TrustFrameworkExtensions.xml, e trovare l'elemento `<UserJourneys>`. Se l'elemento non esiste, aggiungerne uno.
4.  Incollare l'intero contenuto del nodo `<UserJournesy>` copiato come figlio dell'elemento `<UserJourneys>`.

### <a name="step-42-display-the-button"></a>Passaggio 4.2: Visualizzare il "pulsante"
L'elemento `<ClaimsProviderSelections>` definisce l'elenco delle opzioni di selezione del provider di attestazioni e il relativo ordine.  L'elemento `<ClaimsProviderSelection>` è analogo a un pulsante del provider di identità in una pagina di registrazione/accesso. Se si aggiunge un elemento `<ClaimsProviderSelection>` per l'account Twitter, viene visualizzato un nuovo pulsante quando un utente apre la pagina. Per aggiungere questo elemento:

1.  Trovare il nodo `<UserJourney>` che include `Id="SignUpOrSignIn"` nel percorso utente appena copiato.
2.  Passare al nodo `<OrchestrationStep>` che include `Order="1"`
3.  Aggiungere il frammento XML seguente nel nodo `<ClaimsProviderSelections>`:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    ```

### <a name="step-43-link-the-button-to-an-action"></a>Passaggio 4.3: Collegare il pulsante a un'azione
Ora che il pulsante è stato posizionato, è necessario collegarlo a un'azione. L'azione in questo caso consiste nel far comunicare Azure AD B2C con l'account Twitter per ricevere un token. È possibile farlo collegando il profilo tecnico per il provider di attestazioni dell'account Twitter:

1.  Trovare l'oggetto `<OrchestrationStep>` che include `Order="2"` nel nodo `<UserJourney>`.
2.  Aggiungere il frammento XML seguente nel nodo `<ClaimsExchanges>`:

    ```xml
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
    ```

    > [!NOTE]
    >
    > * Verificare che `Id` sia impostato sullo stesso valore di `TargetClaimsExchangeId` riportato nella sezione precedente
    > * Verificare che l'ID `TechnicalProfileReferenceId` sia impostato sul profilo tecnico creato in precedenza (Twitter-OAUTH1).

## <a name="step-5-upload-the-policy-to-your-tenant"></a>Passaggio 5: Caricare i criteri nel tenant
1.  Nel [portale di Azure](https://portal.azure.com) passare al [contesto del tenant di Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) e fare clic su **Azure AD B2C**.
2.  Fare clic su **Framework dell'esperienza di gestione delle identità**.
3.  Fare clic su **Tutti i criteri**.
4.  Selezionare **Carica il criterio**.
5.  Selezionare la casella **Sovrascrivi il criterio se esistente**.
6.  **Caricare** TrustFrameworkExtensions.xml e assicurarsi che non presenti errori di convalida

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>Passaggio 6: Testare i criteri personalizzati tramite Esegui adesso

1.  Aprire **Impostazioni di Azure AD B2C** e passare a **Framework dell'esperienza di gestione delle identità**.

    > [!NOTE]
    >
    >Il comando **Esegui adesso** richiede che nel tenant sia preregistrata almeno un'applicazione. Per informazioni su come registrare le applicazioni, vedere l'articolo di [introduzione](active-directory-b2c-get-started.md) ad Azure AD B2C o l'articolo relativo alla [registrazione delle applicazioni](active-directory-b2c-app-registration.md).

2.  Aprire **B2C_1A_signup_signin**, i criteri personalizzati dalla relying party caricati in precedenza. Selezionare **Esegui adesso**.
3.  Dovrebbe essere possibile accedere usando un account Twitter.

## <a name="step-7-optional-register-the-twitter-account-claims-provider-to-profile-edit-user-journey"></a>Passaggio 7: [Facoltativo] Registrare il provider di attestazioni dell'account Twitter nel percorso utente Profile-Edit
È possibile aggiungere il provider di identità dell'account Twitter anche al percorso utente `ProfileEdit` dell'utente. Per renderlo disponibile, ripetere il passaggio 4. Selezionare questa volta il nodo `<UserJourney>` che include `Id="ProfileEdit"`. Salvare, caricare e verificare i criteri.


## <a name="download-the-complete-policy-files"></a>Scaricare i file dei criteri completi
Facoltativo: anziché usare i file di esempio, per creare lo scenario è consigliabile usare file di criteri personalizzati dopo aver completato la procedura Introduzione ai criteri personalizzati.  [File dei criteri di esempio di riferimento](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-twitter-app)