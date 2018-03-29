---
title: 'Azure Active Directory B2C: aggiungere Twitter come provider di identità OAuth1 tramite criteri personalizzati'
description: Usare Twitter come provider di identità con il protocollo OAuth1
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 10/23/2017
ms.author: davidmu
ms.openlocfilehash: 4a7cc552f49877874dc173a47efe22873d5aeeed
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-add-twitter-as-an-oauth1-identity-provider-by-using-custom-policies"></a>Azure Active Directory B2C: aggiungere Twitter come provider di identità OAuth1 tramite criteri personalizzati
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Questo articolo illustra come consentire l'accesso agli utenti di un account Twitter usando [criteri personalizzati](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>prerequisiti
Completare la procedura descritta nell'articolo [Introduzione ai criteri personalizzati](active-directory-b2c-get-started-custom.md).

## <a name="step-1-create-a-twitter-account-application"></a>Passaggio 1: Creare un'applicazione dell'account Twitter
Per usare Twitter come provider di identità in Azure Active Directory B2C (Azure AD B2C), è necessario creare un'applicazione Twitter e inserire i parametri corretti. È possibile registrare un'applicazione Twitter visitando la [pagina di iscrizione a Twitter](https://twitter.com/signup).

1. Passare al [sito Web per sviluppatori Twitter](https://apps.twitter.com/), accedere con le credenziali dell'account Twitter e quindi selezionare **Create New App** (Crea nuova app).

    ![Account Twitter - Creazione di una nuova app](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app1.png)

2. Nella finestra **Create an application** (Crea applicazione) seguire questa procedura:
 
    a. Digitare i valori **Name** (Nome) e **Description** (Descrizione) per la nuova app. 

    b. Nella casella **Sito Web** incollare **https://login.microsoftonline.com**. 

    c. Nella casella **URL callback** incollare **https://login.microsoftonline.com/te/{tenant}.onmicrosoft.com/oauth2/authresp**. Sostituire {*tenant*} con il nome del tenant, ad esempio contosob2c.onmicrosoft.com. Assicurarsi che sia in uso lo schema HTTPS. 

    d. Nella parte inferiore della pagina leggere e accettare le condizioni, quindi selezionare **Create your Twitter application** (Crea applicazione Twitter).

    ![Account Twitter - Aggiungere una nuova app](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app2.png)

3. Nella finestra **B2C demo** selezionare **Settings** (Impostazioni), la casella di controllo **Allow this application to be used to sign in with Twitter** (Consenti l'uso dell'applicazione per l'accesso con Twitter) e quindi **Update Settings** (Aggiorna impostazioni).

4. Selezionare **Keys and Access Tokens** (Chiavi e token di accesso) e prendere nota dei valori riportati in **Consumer Key (API Key)** (Chiave utente - chiave API) e **Consumer Secret (API Secret)** (Segreto utente- chiave privata API).

    ![Account Twitter - Impostare le proprietà dell'applicazione](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app3.png)

    >[!NOTE]
    >Il segreto consumer è un'importante credenziale di sicurezza. Non condividere questo valore con altri né distribuirlo con l'app.

## <a name="step-2-add-your-twitter-account-application-key-to-azure-ad-b2c"></a>Passaggio 2: Aggiungere la chiave dell'applicazione dell'account Twitter ad Azure AD B2C
La federazione con account Twitter richiede un segreto utente per consentire all'account Twitter di considerare attendibile Azure AD B2C per conto dell'applicazione. Per archiviare il segreto utente dell'applicazione Twitter nel tenant di Azure AD B2C, seguire questa procedura: 

1. Nel tenant di Azure AD B2C selezionare **Impostazioni di Azure AD B2C** > **Framework dell'esperienza di gestione delle identità**.

2. Selezionare **Chiavi dei criteri** per visualizzare le chiavi disponibili nel tenant.

3. Selezionare **Aggiungi**.

4. Nella casella **Opzioni** selezionare **Manuale**.

5. Nella casella **Nome** selezionare **TwitterSecret**.  
    È possibile che il prefisso *B2C_1A_* venga aggiunto automaticamente.

6. Nella casella **Segreto** immettere il segreto dell'applicazione Microsoft indicato nel [portale di registrazione delle applicazioni](https://apps.dev.microsoft.com).

7. Per **Uso chiave** usare **Crittografia**.

8. Selezionare **Create**.

9. Verificare di avere creato la chiave `B2C_1A_TwitterSecret`.

## <a name="step-3-add-a-claims-provider-in-your-extension-policy"></a>Passaggio 3: Aggiungere un provider di attestazioni nei criteri di estensione

Per consentire agli utenti di accedere con l'account Twitter, è necessario definire Twitter come provider di attestazioni. In altre parole, è necessario specificare gli endpoint con cui comunica Azure AD B2C. Gli endpoint offriranno un set di attestazioni che verranno usate da Azure AD B2C per verificare se un utente specifico è stato autenticato.

Definire Twitter come provider di attestazioni aggiungendo il nodo `<ClaimsProvider>` nel file dei criteri di estensione:

1. Aprire il file dei criteri di estensione *TrustFrameworkExtensions.xml* nella directory di lavoro. 

2. Cercare la sezione `<ClaimsProviders>`.

3. Nel nodo `<ClaimsProviders>` aggiungere il frammento di codice XML seguente:  

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

4. Sostituire il valore di *client_id*` con la chiave utente dell'applicazione dell'account Twitter.

5. Salvare il file.

## <a name="step-4-register-the-twitter-account-claims-provider-to-your-sign-up-or-sign-in-user-journey"></a>Passaggio 4: Registrare il provider di attestazioni dell'account Twitter nel percorso utente di iscrizione o accesso
Il provider di identità è stato configurato, ma non è ancora disponibile in alcuna finestra di iscrizione o di accesso. È ora necessario aggiungere il provider di identità dell'account Twitter al percorso utente `SignUpOrSignIn` dell'utente.

### <a name="step-41-make-a-copy-of-the-user-journey"></a>Passaggio 4.1: Creare una copia del percorso utente
Per rendere disponibile il percorso utente, si crea un duplicato di un modello di percorso utente esistente e quindi si aggiunge il provider di identità Twitter:

>[!NOTE]
>Se l'elemento `<UserJourneys>` è stato copiato dal file di base dei criteri al file di estensione *TrustFrameworkExtensions.xml*, si può passare alla sezione successiva.

1. Aprire il file di base dei criteri, ad esempio TrustFrameworkBase.xml.

2. Cercare l'elemento `<UserJourneys>`, selezionare l'intero contenuto del nodo `<UserJourney>` e quindi selezionare **Taglia** per spostare il testo selezionato negli Appunti.

3. Aprire il file di estensione, ad esempio TrustFrameworkExtensions.xml, e quindi cercare l'elemento `<UserJourneys>`. Se l'elemento non esiste, aggiungerlo.

4. Incollare l'intero contenuto del nodo `<UserJourney>`, spostato negli Appunti nel passaggio 2, nell'elemento `<UserJourneys>`.

### <a name="step-42-display-the-button"></a>Passaggio 4.2: Visualizzare il "pulsante"
L'elemento `<ClaimsProviderSelections>` definisce l'elenco delle opzioni di selezione del provider di attestazioni e il relativo ordine. Il nodo `<ClaimsProviderSelection>` è analogo a un pulsante di provider di identità in una pagina di iscrizione o accesso. Se si aggiunge un nodo `<ClaimsProviderSelection>` per un account Twitter, viene visualizzato un nuovo pulsante quando un utente apre la pagina. Per aggiungere l'elemento seguire questa procedura:

1. Cercare il nodo `<UserJourney>` che contiene `Id="SignUpOrSignIn"` nel percorso utente copiato.

2. Individuare il nodo `<OrchestrationStep>` contenente `Order="1"`.

3. Aggiungere il frammento di codice XML seguente nell'elemento `<ClaimsProviderSelections>`:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    ```

### <a name="step-43-link-the-button-to-an-action"></a>Passaggio 4.3: Collegare il pulsante a un'azione
Ora che il pulsante è presente, è necessario collegarlo a un'azione. In questo caso, l'azione consiste nel far comunicare Azure AD B2C con l'account Twitter per ricevere un token. È possibile farlo collegando il profilo tecnico per il provider di attestazioni dell'account Twitter:

1. Cercare il nodo `<OrchestrationStep>` che contiene `Order="2"` nel nodo `<UserJourney>`.
2. Aggiungere il frammento di codice XML seguente nell'elemento `<ClaimsExchanges>`:

    ```xml
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
    ```

    >[!NOTE]
    >* Verificare che `Id` sia impostato sullo stesso valore di `TargetClaimsExchangeId` riportato nella sezione precedente.
    >* Verificare che l'ID `TechnicalProfileReferenceId` sia impostato sul profilo tecnico creato in precedenza (Twitter-OAUTH1).

## <a name="step-5-upload-the-policy-to-your-tenant"></a>Passaggio 5: Caricare i criteri nel tenant
1. Nel [portale di Azure](https://portal.azure.com) passare al [contesto del tenant Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) e quindi selezionare **Azure AD B2C**.

2. Fare clic su **Framework dell'esperienza di gestione delle identità**.

3. Selezionare **Tutti i criteri**.

4. Selezionare **Carica criteri**.

5. Selezionare la casella di controllo **Sovrascrivi il criterio se esistente**.

6. Caricare i file *TrustFrameworkBase.xml* e *TrustFrameworkExtensions.xml* e assicurarsi che superino la convalida.

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>Passaggio 6: Testare i criteri personalizzati tramite Esegui adesso

1. Selezionare **Impostazioni di Azure AD B2C** e quindi **Framework dell'esperienza di gestione delle identità**.

    >[!NOTE]
    >Il comando Esegui adesso richiede che nel tenant sia preregistrata almeno un'applicazione. Per informazioni su come registrare le applicazioni, vedere l'articolo di [introduzione](active-directory-b2c-get-started.md) ad Azure AD B2C o l'articolo relativo alla [registrazione delle applicazioni](active-directory-b2c-app-registration.md).

2. Aprire **B2C_1A_signup_signin**, i criteri personalizzati della relying party caricati in precedenza e quindi selezionare **Esegui adesso**.  
    Ora dovrebbe essere possibile accedere usando l'account Twitter.

## <a name="step-7-optional-register-the-twitter-account-claims-provider-to-the-profile-edit-user-journey"></a>Passaggio 7 (facoltativo): Registrare il provider di attestazioni dell'account Twitter nel percorso utente ProfileEdit
È anche possibile aggiungere il provider di identità dell'account Twitter al percorso utente `ProfileEdit`. Per rendere disponibile il percorso utente, ripetere il passaggio 4. Selezionare questa volta il nodo `<UserJourney>` che contiene `Id="ProfileEdit"`. Salvare, caricare e verificare i criteri.


## <a name="optional-download-the-complete-policy-files"></a>(Facoltativo) Scaricare i file dei criteri completi
Dopo aver completato la procedura [Introduzione ai criteri personalizzati](active-directory-b2c-get-started-custom.md), è consigliabile usare file di criteri personalizzati per definire scenari specifici. Per riferimento, sono disponibili [file di criteri di esempio](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-twitter-app).
