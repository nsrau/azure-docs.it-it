---
title: "Azure Active Directory B2C: Aggiungere Google+ come provider di identità OAuth2 tramite criteri personalizzati"
description: "Esempio di utilizzo di Google+ come provider di identità basato sul protocollo OAuth2"
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
ms.date: 08/04/2017
ms.author: yoelh
ms.openlocfilehash: 0d84dde1f70023abcfd0c15f5425d3cbaeb8c765
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2017
---
# <a name="azure-active-directory-b2c-add-google-as-an-oauth2-identity-provider-using-custom-policies"></a>Azure Active Directory B2C: Aggiungere Google+ come provider di identità OAuth2 tramite criteri personalizzati

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Questa guida illustra come consentire agli utenti di accedere da un account Google+ tramite l'utilizzo di [criteri personalizzati](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Prerequisiti

Completare la procedura descritta nell'articolo [Introduzione ai criteri personalizzati](active-directory-b2c-get-started-custom.md).

La procedura include i passaggi seguenti:

1.  Creazione di un'applicazione di account Google+.
2.  Aggiunta della chiave dell'applicazione di account Google+ in Azure AD B2C
3.  Aggiunta di un provider di attestazioni nei criteri
4.  Registrazione del provider di attestazioni dell'account Google+ in un percorso utente
5.  Caricamento dei criteri in un tenant di Azure AD B2C e test dei criteri

## <a name="create-a-google-account-application"></a>Creare un'applicazione di account Google+
Per usare Google+ come provider di identità in Azure Active Directory (Azure AD) B2C, è necessario creare un'applicazione Google+ e inserire i parametri corretti. È possibile registrare un'applicazione Google+ qui: [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp)

1.  Visitare [Google Developers Console](https://console.developers.google.com/) e accedere con le credenziali dell'account Google+.
2.  Fare clic su **Crea progetto**, immettere un nome in **Nome progetto**e fare clic su **Crea**.

3.  Fare clic sul **menu Progetti**.

    ![Account Google+ - Selezionare il progetto](media/active-directory-b2c-custom-setup-goog-idp/goog-add-new-app1.png)

4.  Fare clic sul pulsante **+**.

    ![Account Google+ - Creare un nuovo progetto](media/active-directory-b2c-custom-setup-goog-idp//goog-add-new-app2.png)

5.  Fare clic su un **Nome progetto** e quindi su **Crea**.

    ![Account Google+ - Nuovo progetto](media/active-directory-b2c-custom-setup-goog-idp//goog-app-name.png)

6.  Attendere che il progetto sia pronto e fare clic sul **menu Progetti**.

    ![Account Google+ - Attendere che il nuovo progetto sia pronto per l'utilizzo](media/active-directory-b2c-custom-setup-goog-idp//goog-select-app1.png)

7.  Fare clic sul nome del progetto.

    ![Account Google+ - Selezionare il nuovo progetto](media/active-directory-b2c-custom-setup-goog-idp//goog-select-app2.png)

8.  Fare clic su **Gestione API** e su **Credenziali** nel riquadro di spostamento sinistro.
9.  Fare clic sulla scheda **Schermata consenso OAuth** nella parte superiore.

    ![Account Google+ - Impostare la schermata del consenso OAuth](media/active-directory-b2c-custom-setup-goog-idp/goog-add-cred.png)

10.  Selezionare o specificare un **Indirizzo e-mail** valido, fornire un **Nome prodotto** e fare clic su **Salva**.

    ![Google+ - Credenziali dell'applicazione](media/active-directory-b2c-custom-setup-goog-idp/goog-consent-screen.png)

11.  Fare clic su **Nuove credenziali** e scegliere **ID client OAuth**.

    ![Google + - Creare nuove credenziali dell'applicazione](media/active-directory-b2c-custom-setup-goog-idp/goog-add-oauth2-client-id.png)

12.  In **Tipo di applicazione** selezionare **Applicazione Web**.

    ![Google+ - Selezionare il tipo di applicazione](media/active-directory-b2c-custom-setup-goog-idp/goog-web-app.png)

13.  Fornire un **Nome** per l'applicazione, immettere `https://login.microsoftonline.com` nel campo **Origini JavaScript Autorizzate** e `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` nel campo **URI di reindirizzamento autorizzati**. Sostituire **{tenant}** con il nome del tenant, ad esempio contosob2c.onmicrosoft.com. Il valore **{tenant}** distingue tra maiuscole e minuscole. Fare clic su **Crea**.

    ![Google+ - Specificare le origini JavaScript autorizzate e gli URI di reindirizzamento](media/active-directory-b2c-custom-setup-goog-idp/goog-create-client-id.png)

14.  Copiare i valori **ID client** e **Segreto client**. Sono entrambi necessari per configurare Google+ come provider di identità nel tenant. **Segreto client** è una credenziale di sicurezza importante.

    ![Google+ - Copiare i valori di ID client e Segreto client](media/active-directory-b2c-custom-setup-goog-idp/goog-client-secret.png)

## <a name="add-the-google-account-application-key-to-azure-ad-b2c"></a>Aggiungere la chiave dell'applicazione di account Google+ in Azure AD B2C
La federazione con account Google+ richiede un segreto client per consentire all'account Google+ di considerare attendibile Azure AD B2C per conto dell'applicazione. È necessario archiviare il segreto dell'applicazione Google+ nel tenant di Azure AD B2C:  

1.  Passare al tenant di Azure AD B2C e selezionare **B2C Settings** (Impostazioni B2C) > **Framework dell'esperienza di gestione delle identità**
2.  Selezionare **Chiavi dei criteri** per visualizzare le chiavi disponibili nel tenant.
3.  Fare clic su **+Aggiungi**.
4.  Per **Opzioni** usare **Manuale**.
5.  Per **Nome** usare `GoogleSecret`.  
    È possibile che il prefisso `B2C_1A_` venga aggiunto automaticamente.
6.  Nella casella **Segreto** immettere il segreto dell'applicazione Google da [Google Developers Console](https://console.developers.google.com/) copiato sopra.
7.  Per **Uso chiave** usare **Firma**.
8.  Fare clic su **Crea**
9.  Confermare di avere creato la chiave `B2C_1A_GoogleSecret`.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>Aggiungere un provider di attestazioni nei criteri di estensione

Per consentire agli utenti di accedere con un account Google+, è necessario definire l'account Google+ come provider di attestazioni. In altre parole, è necessario specificare un endpoint con cui comunichi Azure AD B2C. L'endpoint offre un set di attestazioni che vengono usate da Azure AD B2C per verificare se un utente specifico è stato autenticato.

Definire l'account Google+ come provider di attestazioni aggiungendo il nodo `<ClaimsProvider>` nel file dei criteri di estensione:

1.  Aprire il file dei criteri di estensione (TrustFrameworkExtensions.xml) dalla directory di lavoro. Se occorre un editor XML, provare [Visual Studio Code](https://code.visualstudio.com/download), un editor multipiattaforma leggero.
2.  Trovare la sezione `<ClaimsProviders>`
3.  Aggiungere il seguente frammento XML nell'elemento `ClaimsProviders` e sostituire il valore `client_id` con l'ID client dell'applicazione di account Google+ prima di salvare il file.  

```xml
<ClaimsProvider>
    <Domain>google.com</Domain>
    <DisplayName>Google</DisplayName>
    <TechnicalProfiles>
    <TechnicalProfile Id="Google-OAUTH">
        <DisplayName>Google</DisplayName>
        <Protocol Name="OAuth2" />
        <Metadata>
        <Item Key="ProviderName">google</Item>
        <Item Key="authorization_endpoint">https://accounts.google.com/o/oauth2/auth</Item>
        <Item Key="AccessTokenEndpoint">https://accounts.google.com/o/oauth2/token</Item>
        <Item Key="ClaimsEndpoint">https://www.googleapis.com/oauth2/v1/userinfo</Item>
        <Item Key="scope">email</Item>
        <Item Key="HttpBinding">POST</Item>
        <Item Key="UsePolicyInRedirectUri">0</Item>
        <Item Key="client_id">Your Google+ application ID</Item>
        </Metadata>
        <CryptographicKeys>
        <Key Id="client_secret" StorageReferenceId="B2C_1A_GoogleSecret" />
        </CryptographicKeys>
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="id" />
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
        <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
        <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
        <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="google.com" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
        </OutputClaims>
        <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
        <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
        <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
        <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
        </OutputClaimsTransformations>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        <ErrorHandlers>
        <ErrorHandler>
            <ErrorResponseFormat>json</ErrorResponseFormat>
            <ResponseMatch>$[?(@@.error == 'invalid_grant')]</ResponseMatch>
            <Action>Reauthenticate</Action>
            <!--In case of authorization code used error, we don't want the user to select his account again.-->
            <!--AdditionalRequestParameters Key="prompt">select_account</AdditionalRequestParameters-->
        </ErrorHandler>
        </ErrorHandlers>
    </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="register-the-google-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>Registrare il provider di attestazioni dell'account Google+ in un percorso utente di registrazione o di accesso

Il provider di identità è stato configurato,  ma non è disponibile in nessuna delle schermate di registrazione o di accesso. Aggiungere il provider di identità dell'account Google+ al percorso utente `SignUpOrSignIn` dell'utente. Per renderlo disponibile, si crea un duplicato di un modello di processo utente esistente  e si aggiunge il provider di identità dell'account Google+:

>[!NOTE]
>
>Se l'elemento `<UserJourneys>` è stato copiato dal file di base dei criteri al file di estensione (TrustFrameworkExtensions.xml), è possibile ignorare questa sezione.

1.  Aprire il file di base dei criteri, ad esempio TrustFrameworkBase.xml.
2.  Trovare l'elemento `<UserJourneys>` e copiare l'intero contenuto del nodo `<UserJourneys>`.
3.  Aprire il file di estensione, ad esempio TrustFrameworkExtensions.xml, e trovare l'elemento `<UserJourneys>`. Se l'elemento non esiste, aggiungerne uno.
4.  Incollare l'intero contenuto del nodo `<UserJournesy>` copiato come figlio dell'elemento `<UserJourneys>`.

### <a name="display-the-button"></a>Visualizzare il pulsante
L'elemento `<ClaimsProviderSelections>` definisce l'elenco delle opzioni di selezione del provider di attestazioni e il relativo ordine.  L'elemento `<ClaimsProviderSelection>` è analogo a un pulsante del provider di identità in una pagina di registrazione/accesso. Se si aggiunge un elemento `<ClaimsProviderSelection>` per l'account Google+, viene visualizzato un nuovo pulsante quando un utente apre la pagina. Per aggiungere questo elemento:

1.  Trovare il nodo `<UserJourney>` che include `Id="SignUpOrSignIn"` nel percorso utente appena copiato.
2.  Passare al nodo `<OrchestrationStep>` che include `Order="1"`
3.  Aggiungere il frammento XML seguente nel nodo `<ClaimsProviderSelections>`:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
```

### <a name="link-the-button-to-an-action"></a>Collegare il pulsante a un'azione
Ora che il pulsante è stato posizionato, è necessario collegarlo a un'azione. L'azione, in questo caso, consiste nel far comunicare Azure AD B2C con l'account Google+ per ricevere un token.

1.  Trovare l'oggetto `<OrchestrationStep>` che include `Order="2"` nel nodo `<UserJourney>`.
2.  Aggiungere il frammento XML seguente nel nodo `<ClaimsExchanges>`:

```xml
<ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
```

>[!NOTE]
>
> * Verificare che `Id` sia impostato sullo stesso valore di `TargetClaimsExchangeId` riportato nella sezione precedente
> * Verificare che l'ID `TechnicalProfileReferenceId` sia impostato sul profilo tecnico creato in precedenza (Google-OAUTH).

## <a name="upload-the-policy-to-your-tenant"></a>Caricare i criteri nel tenant
1.  Nel [portale di Azure](https://portal.azure.com) passare al [contesto del tenant di Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) e aprire il pannello **Azure AD B2C**.
2.  Fare clic su **Framework dell'esperienza di gestione delle identità**.
3.  Aprire il pannello **Tutti i criteri**.
4.  Selezionare **Carica criteri**.
5.  Selezionare la casella **Sovrascrivi il criterio se esistente**.
6.  **Caricare** TrustFrameworkExtensions.xml e assicurarsi che non presenti errori di convalida

## <a name="test-the-custom-policy-by-using-run-now"></a>Testare i criteri personalizzati tramite Esegui adesso
1.  Aprire **Impostazioni di Azure AD B2C** e passare a **Framework dell'esperienza di gestione delle identità**.

    >[!NOTE]
    >
    >    Il comando **Esegui adesso** richiede che nel tenant sia preregistrata almeno un'applicazione. 
    >    Per informazioni su come registrare le applicazioni, vedere l'articolo di [introduzione](active-directory-b2c-get-started.md) ad Azure AD B2C o l'articolo relativo alla [registrazione delle applicazioni](active-directory-b2c-app-registration.md).


2.  Aprire **B2C_1A_signup_signin**, i criteri personalizzati dalla relying party caricati in precedenza. Selezionare **Esegui adesso**.
3.  Dovrebbe essere possibile accedere usando un account Google+.

## <a name="optional-register-the-google-account-claims-provider-to-profile-edit-user-journey"></a>[Facoltativo] Registrare il provider di attestazioni dell'account Google+ nel percorso utente Profile-Edit
È possibile aggiungere il provider di identità dell'account Google+ anche al percorso utente `ProfileEdit` dell'utente. Per renderlo disponibile, ripetere gli ultimi due passaggi:

### <a name="display-the-button"></a>Visualizzare il pulsante
1.  Aprire il file di estensione dei criteri, ad esempio TrustFrameworkExtensions.xml.
2.  Trovare il nodo `<UserJourney>` che include `Id="ProfileEdit"` nel percorso utente appena copiato.
3.  Passare al nodo `<OrchestrationStep>` che include `Order="1"`
4.  Aggiungere il frammento XML seguente nel nodo `<ClaimsProviderSelections>`:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
```

### <a name="link-the-button-to-an-action"></a>Collegare il pulsante a un'azione
1.  Trovare l'oggetto `<OrchestrationStep>` che include `Order="2"` nel nodo `<UserJourney>`.
2.  Aggiungere il frammento XML seguente nel nodo `<ClaimsExchanges>`:

```xml
<ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
```

### <a name="test-the-custom-profile-edit-policy-by-using-run-now"></a>Testare i criteri Profile-Edit personalizzati tramite Esegui adesso

1.  Aprire **Impostazioni di Azure AD B2C** e passare a **Framework dell'esperienza di gestione delle identità**.
2.  Aprire **B2C_1A_ProfileEdit**, i criteri personalizzati dalla relying party caricati in precedenza. Selezionare **Esegui adesso**.
3.  Dovrebbe essere possibile accedere usando un account Google+.

## <a name="download-the-complete-policy-files"></a>Scaricare i file dei criteri completi
Facoltativo: anziché usare i file di esempio, per creare lo scenario è consigliabile usare file di criteri personalizzati dopo aver completato la procedura Introduzione ai criteri personalizzati.  [File dei criteri di esempio di riferimento](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-goog-app)
