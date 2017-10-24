---
title: "Azure Active Directory B2C: Aggiungere LinkedIn come provider di identità OAuth2 tramite criteri personalizzati"
description: Articolo di procedure su come configurare l'applicazione LinkedIn con il protocollo OAuth2 e criteri personalizzati
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
ms.date: 10/03/2017
ms.author: yoelh
ms.openlocfilehash: ac4c0212ffc13b24b6035756f1210d62b7b840c8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-add-linkedin-as-an-identity-provider-using-custom-policies"></a>Azure Active Directory B2C: Aggiungere LinkedIn come provider di identità tramite criteri personalizzati
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Questo articolo illustra come consentire agli utenti di accedere da un account LinkedIn tramite l'uso di [criteri personalizzati](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Prerequisiti
Completare la procedura descritta nell'articolo [Introduzione ai criteri personalizzati](active-directory-b2c-get-started-custom.md).

La procedura include i passaggi seguenti:
1.  Creazione di un'applicazione di account LinkedIn.
2.  Aggiunta della chiave dell'applicazione di account LinkedIn in Azure AD B2C
3.  Aggiunta di un provider di attestazioni nei criteri
4.  Registrazione del provider di attestazioni dell'account LinkedIn in un percorso utente
5.  Caricamento dei criteri in un tenant di Azure AD B2C e test dei criteri

## <a name="step-1-create-a-linkedin-account-application"></a>Passaggio 1: Creare un'applicazione di account LinkedIn
Per utilizzare LinkedIn come provider di identità in Azure Active Directory (Azure AD) B2C, è necessario creare un'applicazione LinkedIn e inserire i parametri corretti. È possibile registrare un'applicazione di LinkedIn qui: [https://LinkedIn.com/signup](https://LinkedIn.com/signup)

1.  Passare al sito Web per la [gestione delle applicazioni di LinkedIn](https://www.linkedin.com/secure/developer?newapp=), accedere con le credenziali dell'account LinkedIn personale e fare clic su **Crea applicazione**.

    ![Account LinkedIn - Crea applicazione](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app1.png)

2.  1.  Digitare il **Nome azienda**, quindi specificare un **Nome** descrittivo e una **Descrizione** per la nuova app.
    2.  Caricare il **Logo dell'applicazione**
    3.  Selezionare un **Utilizzo dell'applicazione**
    4.  Incollare `https://login.microsoftonline.com` per il valore **Indirizzo del sito Web**.
    5.  Digitare **Email aziendale** e **Telefono aziendale**
    6.  Nella parte inferiore della pagina leggere e accettare le condizioni d'uso. Fare clic su **Invia**.

    ![Account LinkedIn - Configurare le proprietà dell'applicazione](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app2.png)

3.  Dal menu fare clic su **Autenticazione**. Prendere nota dei valori di **ID client** e **Segreto client**.

    In **URL reindirizzamento autorizzati**  incollare `https://login.microsoftonline.com/te/{tenant}.onmicrosoft.com/oauth2/authresp`. Sostituire {tenant} con il nome del tenant, ad esempio contosob2c.onmicrosoft.com. Assicurarsi che sia in uso lo schema HTTPS e fare clic su **Aggiungi**.

    ![Account di LinkedIn - Impostare gli URL di reindirizzamento autorizzati](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app3.png)

    > [!NOTE]
    >
    >Il segreto client è un'importante credenziale di sicurezza. Non condividere questo valore con altri né distribuirlo con l'app.

4.  Fare clic sulla scheda **Impostazioni**, impostare **Stato dell'applicazione** su **Live** e quindi fare clic su **Aggiorna**.

    ![Account di LinkedIn - Impostare lo stato dell'applicazione](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app4.png)

## <a name="step-2-add-your-linkedin-application-key-to-azure-ad-b2c"></a>Passaggio 2: Aggiungere la chiave dell'applicazione di LinkedIn in Azure AD B2C
La federazione con account LinkedIn richiede un segreto client per consentire all'account LinkedIn di considerare attendibile Azure AD B2C per conto dell'applicazione. È necessario archiviare la chiave dell'applicazione di LinkedIn nel tenant di Azure AD B2C:  

1.  Passare al tenant di Azure AD B2C e selezionare **B2C Settings** (Impostazioni B2C) > **Framework dell'esperienza di gestione delle identità**
2.  Selezionare **Chiavi dei criteri** per visualizzare le chiavi disponibili nel tenant.
3.  Fare clic su **+Aggiungi**.
4.  Per **Opzioni** usare **Manuale**.
5.  Per **Nome** usare `LinkedInSecret`.  
    È possibile che il prefisso `B2C_1A_` venga aggiunto automaticamente.
6.  Nella casella **Segreto** immettere il segreto dell'applicazione LinkedIn da https://apps.dev.microsoft.com
7.  Per **Uso chiave** usare **Crittografia**.
8.  Fare clic su **Crea** 
9.  Confermare di avere creato la chiave `B2C_1A_LinkedInSecret`.

## <a name="step-3-add-a-claims-provider-in-your-extension-policy"></a>Passaggio 3: Aggiungere un provider di attestazioni nei criteri di estensione
Per consentire agli utenti di accedere con un account LinkedIn, è necessario definire LinkedIn come provider di attestazioni. In altre parole, è necessario specificare degli endpoint con i quali Azure AD B2C comunichi. Gli endpoint offriranno un set di attestazioni che verranno usate da Azure AD B2C per verificare se un utente specifico è stato autenticato.

Definire LinkedIn come provider di attestazioni aggiungendo il nodo `<ClaimsProvider>` nel file dei criteri di estensione:

1.  Aprire il file dei criteri di estensione (TrustFrameworkExtensions.xml) dalla directory di lavoro. 
2.  Trovare la sezione `<ClaimsProviders>`
3.  Aggiungere il frammento XML seguente nel nodo `<ClaimsProviders>`:  

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
          <Item Key="ClaimsEndpoint">https://api.linkedin.com/v1/people/~:(id,first-name,last-name,email-address,headline)</Item>
          <Item Key="ClaimsEndpointAccessTokenName">oauth2_access_token</Item>
          <Item Key="ClaimsEndpointFormatName">format</Item>
          <Item Key="ClaimsEndpointFormat">json</Item>
          <Item Key="scope">r_emailaddress r_basicprofile</Item>
          <Item Key="HttpBinding">POST</Item>
          <Item Key="UsePolicyInRedirectUri">0</Item>
          <Item Key="client_id">Your LinkedIn application client ID</Item>
        </Metadata>
        <CryptographicKeys>
          <Key Id="client_secret" StorageReferenceId="B2C_1A_LinkedInSecret" />
        </CryptographicKeys>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="id" />
          <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
          <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
          <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress" />
          <!--<OutputClaim ClaimTypeReferenceId="jobTitle" PartnerClaimType="headline" />-->
          <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="linkedin.com" />
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

4.  Sostituire il valore `client_id` con l'ID client dell'applicazione di LinkedIn
5.  Salvare il file.

## <a name="step-4-register-the-linkedin-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>Passaggio 4: Registrare il provider di attestazioni dell'account LinkedIn in un percorso utente di registrazione o di accesso
A questo punto, il provider di identità è stato configurato, ma non è disponibile in nessuna delle schermate di registrazione o di accesso. È ora necessario aggiungere il provider di identità dell'account LinkedIn al percorso utente `SignUpOrSignIn` dell'utente.

### <a name="step-41-make-a-copy-of-the-user-journey"></a>Passaggio 4.1: Creare una copia del percorso utente
Per renderlo disponibile, creare un duplicato di un modello di percorso utente esistente. Aggiungere quindi il provider di identità di LinkedIn:

> [!NOTE]
>
>Se l'elemento `<UserJourneys>` è stato copiato dal file di base dei criteri al file di estensione (TrustFrameworkExtensions.xml), è possibile ignorare questa sezione.

1.  Aprire il file di base dei criteri, ad esempio TrustFrameworkBase.xml.
2.  Trovare l'elemento `<UserJourneys>` e copiare l'intero contenuto del nodo `<UserJourneys>`.
3.  Aprire il file di estensione, ad esempio TrustFrameworkExtensions.xml, e trovare l'elemento `<UserJourneys>`. Se l'elemento non esiste, aggiungerne uno.
4.  Incollare l'intero contenuto del nodo `<UserJournesy>` copiato come figlio dell'elemento `<UserJourneys>`.

### <a name="step-42-display-the-button"></a>Passaggio 4.2: Visualizzare il "pulsante"
L'elemento `<ClaimsProviderSelections>` definisce l'elenco delle opzioni di selezione del provider di attestazioni e il relativo ordine.  L'elemento `<ClaimsProviderSelection>` è analogo a un pulsante del provider di identità in una pagina di registrazione/accesso. Se si aggiunge un elemento `<ClaimsProviderSelection>` per l'account LinkedIn, viene visualizzato un nuovo pulsante quando un utente apre la pagina. Per aggiungere questo elemento:

1.  Trovare il nodo `<UserJourney>` che include `Id="SignUpOrSignIn"` nel percorso utente appena copiato.
2.  Passare al nodo `<OrchestrationStep>` che include `Order="1"`
3.  Aggiungere il frammento XML seguente nel nodo `<ClaimsProviderSelections>`:
```xml
<ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
```

### <a name="step-43-link-the-button-to-an-action"></a>Passaggio 4.3: Collegare il pulsante a un'azione
Ora che il pulsante è stato posizionato, è necessario collegarlo a un'azione. L'azione, in questo caso, consiste nel far comunicare Azure AD B2C con l'account LinkedIn per ricevere un token. È possibile farlo collegando il profilo tecnico per il provider di attestazioni dell'account LinkedIn:

1.  Trovare l'oggetto `<OrchestrationStep>` che include `Order="2"` nel nodo `<UserJourney>`.
2.  Aggiungere il frammento XML seguente nel nodo `<ClaimsExchanges>`:

    ```xml
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAuth" />
    ```

    > [!NOTE]
    >
    > * Verificare che `Id` sia impostato sullo stesso valore di `TargetClaimsExchangeId` riportato nella sezione precedente
    > * Verificare che l'ID `TechnicalProfileReferenceId` sia impostato sul profilo tecnico creato in precedenza (LinkedIn-OAuth).

## <a name="step-5-upload-the-policy-to-your-tenant"></a>Passaggio 5: Caricare i criteri nel tenant
1.  Nel [portale di Azure](https://portal.azure.com) passare al [contesto del tenant di Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) e fare clic su **Azure AD B2C**.
2.  Fare clic su **Framework dell'esperienza di gestione delle identità**.
3.  Fare clic su **Tutti i criteri**.
4.  Selezionare **Carica criteri**.
5.  Selezionare la casella **Sovrascrivi il criterio se esistente**.
6.  **Caricare** TrustFrameworkExtensions.xml e assicurarsi che non presenti errori di convalida

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>Passaggio 6: Testare i criteri personalizzati tramite Esegui adesso
1.  Aprire **Impostazioni di Azure AD B2C** e passare a **Framework dell'esperienza di gestione delle identità**.

    > [!NOTE]
    >
    >Il comando **Esegui adesso** richiede che nel tenant sia preregistrata almeno un'applicazione. Per informazioni su come registrare le applicazioni, vedere l'articolo di [introduzione](active-directory-b2c-get-started.md) ad Azure AD B2C o l'articolo relativo alla [registrazione delle applicazioni](active-directory-b2c-app-registration.md).

2.  Aprire **B2C_1A_signup_signin**, i criteri personalizzati dalla relying party caricati in precedenza. Selezionare **Esegui adesso**.
3.  Dovrebbe essere possibile accedere usando un account LinkedIn.

## <a name="step-7-optional-register-the-linkedin-account-claims-provider-to-profile-edit-user-journey"></a>Passaggio 7: [Facoltativo] Registrare il provider di attestazioni dell'account LinkedIn nel percorso utente ProfileEdit
È possibile aggiungere il provider di identità dell'account LinkedIn anche al percorso utente `ProfileEdit` dell'utente. Per renderlo disponibile, ripetere il passaggio 4. Selezionare questa volta il nodo `<UserJourney>` che include `Id="ProfileEdit"`. Salvare, caricare e verificare i criteri.

## <a name="download-the-complete-policy-files"></a>Scaricare i file dei criteri completi
Facoltativo: anziché usare i file di esempio, per creare lo scenario è consigliabile usare file di criteri personalizzati dopo aver completato la procedura Introduzione ai criteri personalizzati.  [File dei criteri di esempio di riferimento](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-li-app)