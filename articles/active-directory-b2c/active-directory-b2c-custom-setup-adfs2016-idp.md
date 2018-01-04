---
title: "Azure Active Directory B2C: Aggiungere AD FS come provider di identità SAML tramite criteri personalizzati"
description: Articolo sulle procedure per configurare AD FS 2016 tramite il protocollo SAML e criteri personalizzati
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
ms.openlocfilehash: 22b360aec8878925ebe8d2c67c76d275a42ca7a8
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-add-adfs-as-a-saml-identity-provider-using-custom-policies"></a>Azure Active Directory B2C: Aggiungere AD FS come provider di identità SAML tramite criteri personalizzati

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Questo articolo illustra come consentire agli utenti di accedere da un account AD FS tramite l'utilizzo di [criteri personalizzati](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Prerequisiti

Completare la procedura descritta nell'articolo [Introduzione ai criteri personalizzati](active-directory-b2c-get-started-custom.md).

La procedura include i passaggi seguenti:

1.  Creazione di un trust della relying party di AD FS.
2.  Aggiunta del certificato di trust della relying party di AD FS in Azure AD B2C.
3.  Aggiunta di un provider di attestazioni nei criteri.
4.  Registrazione del provider di attestazioni dell'account AD FS in un percorso utente.
5.  Caricamento dei criteri in un tenant di Azure AD B2C e test dei criteri.

## <a name="to-create-a-claims-aware-relying-party-trust"></a>Per creare un trust della relying party in grado di riconoscere attestazioni

Per usare AD FS come provider di identità in Azure Active Directory (Azure AD) B2C, è necessario creare un trust della relying party di AD FS e inserire i parametri corretti.

Per aggiungere un nuovo trust della relying party usando lo snap-in di gestione di AD FS e configurare manualmente le impostazioni, eseguire la procedura seguente in un server federativo.

L'appartenenza al gruppo **Amministratori** o a un gruppo equivalente nel computer locale è il requisito minimo necessario per completare questa procedura. Informazioni dettagliate sull'utilizzo delle appartenenze ai gruppi e degli account appropriati sono disponibili in [Local and Domain Default Groups](http://go.microsoft.com/fwlink/?LinkId=83477) (Gruppi predefiniti locali e di dominio).

1.  In Server Manager selezionare **Strumenti** e quindi **Gestione AD FS**.

2.  Fare clic su **Aggiungi attendibilità componente**.
    ![Aggiungi attendibilità componente](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-1.png)

3.  Nella pagina **iniziale** scegliere **In grado di riconoscere attestazioni** e fare clic su **Avvia**.
    ![Nella pagina iniziale scegliere In grado di riconoscere attestazioni](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-2.png)
4.  Nella pagina **Seleziona origine dati** fare clic su **Immetti dati sul componente manualmente** e quindi su **Avanti**.
    ![Immetti dati sul componente manualmente](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-3.png)

5.  Nella pagina **Specifica nome visualizzato** digitare un nome in **Nome visualizzato**, in **Note** digitare una descrizione del trust della relying party e quindi fare clic su **Avanti**.
    ![Specificare il nome visualizzato e le note](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-4.png)
6.  facoltativo. Se si ha un certificato di crittografia di token facoltativo, nella pagina **Configura certificato** fare clic su **Sfoglia** per trovare il file del certificato e quindi fare clic su **Avanti**.
    ![Configura certificato](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-5.png)
7.  Nella pagina **Configura URL** selezionare la casella di controllo **Abilita supporto del protocollo SAML 2.0 WebSSO**. In **Componente URL del servizio SAML 2.0 SSO** digitare l'URL dell'endpoint di servizio SALM (Security Assertion Markup Language) per questo trust della relying party e quindi fare clic su **Avanti**.  In **Componente URL del servizio SAML 2.0 SSO** incollare `https://login.microsoftonline.com/te/{tenant}.onmicrosoft.com/{policy}`. Sostituire {tenant} con il nome del tenant (ad esempio, contosob2c.onmicrosoft.com) e sostituire i {criteri} con il nome dei criteri di estensione (ad esempio, B2C_1A_TrustFrameworkExtensions).
    > [!IMPORTANT]
    >Il nome dei criteri è quello da cui ereditano i criteri signup_or_signin, in questo caso: `B2C_1A_TrustFrameworkExtensions`.
    >L'URL, ad esempio, può essere: https://login.microsoftonline.com/te/**contosob2c**.onmicrosoft.com/**B2C_1A_TrustFrameworkBase**.

    ![Componente URL del servizio SAML 2.0 SSO](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-6.png)
8. Nella pagina **Configura identificatori** specificare lo stesso URL del passaggio precedente, fare clic su **Aggiungi** per aggiungerlo all'elenco e quindi fare clic su **Avanti**.
    ![Identificatori dell'attendibilità componente](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-7.png)
9.  Nella pagina **Scegli criteri di controllo di accesso** scegliere i criteri desiderati e fare clic su **Avanti**.
    ![Scegli criteri di controllo di accesso](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-8.png)
10.  Nella pagina **Aggiunta attendibilità** rivedere le impostazioni e quindi fare clic su **Avanti** per salvare le informazioni sul trust della relying party.
    ![Salvare le informazioni sul trust della relying party](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-9.png)
11.  Nella pagina **Fine** fare clic su **Chiudi**. Viene automaticamente visualizzata la finestra di dialogo **Modifica regole attestazione**.
    Selezionare ![Modifica regole attestazione](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-10.png)
12. Fare clic su **Aggiungi regola**.  
      ![Aggiungi nuova regola](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-1.png)
13.  In **Modello di regola attestazione** selezionare **Inviare attributi LDAP come attestazioni**.
    ![Selezionare la regola modello Inviare attributi LDAP come attestazioni](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-2.png)
14.  Specificare **Nome regola attestazione**. Per **Archivio attributi** selezionare **Select Active Directory** (Seleziona Active Directory), aggiungere le attestazioni seguenti e quindi fare clic su **Fine** e su **OK**.
    ![Impostare le proprietà di regola](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-3.png)
15.  In Server Manager selezionare **Attendibilità componente**, selezionare il trust della relying party creato e quindi fare clic su **Proprietà**.
    ![Proprietà di modifica della relying party](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-1.png)
16.  Nella finestra delle proprietà del trust della relying party (B2C Demo) fare clic sulla scheda **Firma** e quindi su **Aggiungi**.  
    ![Imposta firma](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-2.png)
17.  Aggiungere il certificato di firma (file con estensione CERT, senza chiave privata).  
    ![Aggiungere il certificato di firma](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-3.png)
18.  Nella finestra delle proprietà del trust della relying party (B2C Demo) fare clic sulla scheda **Avanzate** e impostare **Algoritmo hash sicuro** su **SHA-1**. Fare clic su **OK**.  
    ![Impostare l'algoritmo hash sicuro su SHA-1](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-4.png)

## <a name="add-the-adfs-account-application-key-to-azure-ad-b2c"></a>Aggiungere la chiave dell'applicazione di account AD FS in Azure AD B2C
La federazione con account AD FS richiede un segreto client per consentire all'account AD FS di considerare attendibile Azure AD B2C per conto dell'applicazione. È necessario quindi archiviare il certificato AD FS nel tenant di Azure AD B2C. 

1.  Passare al tenant di Azure AD B2C e selezionare **B2C Settings** (Impostazioni B2C) > **Framework dell'esperienza di gestione delle identità**
2.  Selezionare **Chiavi dei criteri** per visualizzare le chiavi disponibili nel tenant.
3.  Fare clic su **+Aggiungi**.
4.  Per **Opzioni** usare **Carica**.
5.  Per **Nome** usare `ADFSSamlCert`.  
    È possibile che il prefisso `B2C_1A_` venga aggiunto automaticamente.
6.  In Caricamento file selezionare il file del certificato con estensione PFX, con la chiave privata. Nota: questo certificato (con la chiave privata) deve essere lo stesso rilasciato e usato per la relying party di AD FS.
![Carica chiave criteri](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-cert.png)
7.  Fare clic su **Crea**
8.  Confermare di avere creato la chiave `B2C_1A_ADFSSamlCert`.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>Aggiungere un provider di attestazioni nei criteri di estensione
Per consentire agli utenti di accedere con un account AD FS, è necessario definire l'account AD FS come provider di attestazioni. In altre parole, è necessario specificare un endpoint con cui comunichi Azure AD B2C. L'endpoint offre un set di attestazioni che vengono usate da Azure AD B2C per verificare se un utente specifico è stato autenticato.

Definire AD FS come provider di attestazioni aggiungendo il nodo `<ClaimsProvider>` nel file dei criteri di estensione:

1. Aprire il file dei criteri di estensione (TrustFrameworkExtensions.xml) dalla directory di lavoro. Se occorre un editor XML, provare [Visual Studio Code](https://code.visualstudio.com/download), un editor multipiattaforma leggero.
2. Trovare la sezione `<ClaimsProviders>`
3. Aggiungere il seguente frammento XML nell'elemento `ClaimsProviders` e sostituire `identityProvider` con il DNS (valore arbitrario che indica il dominio personale) e quindi salvare il file. 

```xml
<ClaimsProvider>
    <Domain>contoso.com</Domain>
    <DisplayName>Contoso ADFS</DisplayName>
    <TechnicalProfiles>
    <TechnicalProfile Id="Contoso-SAML2">
        <DisplayName>Contoso ADFS</DisplayName>
        <Description>Login with your Contoso account</Description>
        <Protocol Name="SAML2"/>
        <Metadata>
        <Item Key="RequestsSigned">false</Item>
        <Item Key="WantsEncryptedAssertions">false</Item>
        <Item Key="PartnerEntity">https://{your_ADFS_domain}/federationmetadata/2007-06/federationmetadata.xml</Item>
        </Metadata>
        <CryptographicKeys>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
        </CryptographicKeys>
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="userPrincipalName" />
        <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
        <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
        <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
        </OutputClaims>
        <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
        <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
        <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
        <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
        </OutputClaimsTransformations>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop"/>
    </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="register-the-adfs-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>Registrare il provider di attestazioni dell'account AD FS in un percorso utente di registrazione o di accesso
A questo punto, il provider di identità è stato configurato,  ma non è disponibile in nessuna delle schermate di registrazione o di accesso. È necessario ora aggiungere il provider di identità dell'account AD FS al percorso utente `SignUpOrSignIn` dell'utente. Per renderlo disponibile, si crea un duplicato di un modello di processo utente esistente  e lo si modifica in modo che contenga il provider di identità AD FS:

>[!NOTE]
>Se l'elemento `<UserJourneys>` è stato copiato in precedenza dal file di base dei criteri al file di estensione (TrustFrameworkExtensions.xml) è possibile ignorare questa sezione.

1.  Aprire il file di base dei criteri, ad esempio TrustFrameworkBase.xml.
2.  Trovare l'elemento `<UserJourneys>` e copiare l'intero contenuto del nodo `<UserJourneys>`.
3.  Aprire il file di estensione, ad esempio TrustFrameworkExtensions.xml, e trovare l'elemento `<UserJourneys>`. Se l'elemento non esiste, aggiungerne uno.
4.  Incollare l'intero contenuto del nodo `<UserJournesy>` copiato come figlio dell'elemento `<UserJourneys>`.

### <a name="display-the-button"></a>Visualizzare il pulsante
L'elemento `<ClaimsProviderSelections>` definisce l'elenco delle opzioni di selezione del provider di attestazioni e il relativo ordine.  L'elemento `<ClaimsProviderSelection>` è analogo a un pulsante del provider di identità in una pagina di registrazione/accesso. Se si aggiunge un elemento `<ClaimsProviderSelection>` per l'account AD FS, viene visualizzato un nuovo pulsante quando un utente apre la pagina. Per aggiungere questo elemento:

1.  Trovare il nodo `<UserJourney>` che include `Id="SignUpOrSignIn"` nel percorso utente appena copiato.
2.  Passare al nodo `<OrchestrationStep>` che include `Order="1"`
3.  Aggiungere il frammento XML seguente nel nodo `<ClaimsProviderSelections>`:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
```
### <a name="link-the-button-to-an-action"></a>Collegare il pulsante a un'azione

Ora che il pulsante è stato posizionato, è necessario collegarlo a un'azione. L'azione, in questo caso, consiste nel far comunicare Azure AD B2C con l'account AD FS per ricevere un token. È possibile farlo collegando il profilo tecnico per il provider di attestazioni dell'account AD FS:

1.  Trovare l'oggetto `<OrchestrationStep>` che include `Order="2"` nel nodo `<UserJourney>`.
2.  Aggiungere il frammento XML seguente nel nodo `<ClaimsExchanges>`:

```xml
<ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
```

> [!NOTE]
> * Verificare che `Id` sia impostato sullo stesso valore di `TargetClaimsExchangeId` riportato nella sezione precedente.
> * Verificare che `TechnicalProfileReferenceId` sia impostato sul profilo tecnico creato in precedenza (Contoso-SAML2).

## <a name="upload-the-policy-to-your-tenant"></a>Caricare i criteri nel tenant
1.  Nel [portale di Azure](https://portal.azure.com) passare al [contesto del tenant di Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) e aprire il pannello **Azure AD B2C**.
2.  Fare clic su **Framework dell'esperienza di gestione delle identità**.
3.  Aprire il pannello **Tutti i criteri**.
4.  Selezionare **Carica criteri**.
5.  Selezionare la casella **Sovrascrivi il criterio se esistente**.
6.  **Caricare** TrustFrameworkExtensions.xml e assicurarsi che non presenti errori di convalida

## <a name="test-the-custom-policy-by-using-run-now"></a>Testare i criteri personalizzati tramite Esegui adesso
1.  Aprire **Impostazioni di Azure AD B2C** e passare a **Framework dell'esperienza di gestione delle identità**.
2.  Aprire **B2C_1A_signup_signin**, i criteri personalizzati dalla relying party caricati in precedenza. Selezionare **Esegui adesso**.
3.  Dovrebbe essere possibile accedere usando un account AD FS.

## <a name="optional-register-the-adfs-account-claims-provider-to-profile-edit-user-journey"></a>[Facoltativo] Registrare il provider di attestazioni dell'account AD FS nel percorso utente Profile-Edit
È possibile aggiungere il provider di identità dell'account AD FS anche al percorso utente `ProfileEdit` dell'utente. Per renderlo disponibile, ripetere gli ultimi due passaggi:

### <a name="display-the-button"></a>Visualizzare il pulsante
1.  Aprire il file di estensione dei criteri, ad esempio TrustFrameworkExtensions.xml.
2.  Trovare il nodo `<UserJourney>` che include `Id="ProfileEdit"` nel percorso utente appena copiato.
3.  Passare al nodo `<OrchestrationStep>` che include `Order="1"`
4.  Aggiungere il frammento XML seguente nel nodo `<ClaimsProviderSelections>`:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
```

### <a name="link-the-button-to-an-action"></a>Collegare il pulsante a un'azione
1.  Trovare l'oggetto `<OrchestrationStep>` che include `Order="2"` nel nodo `<UserJourney>`.
2.  Aggiungere il frammento XML seguente nel nodo `<ClaimsExchanges>`:

```xml
<ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
```

### <a name="test-the-custom-profile-edit-policy-by-using-run-now"></a>Testare i criteri Profile-Edit personalizzati tramite Esegui adesso
1.  Aprire **Impostazioni di Azure AD B2C** e passare a **Framework dell'esperienza di gestione delle identità**.
2.  Aprire **B2C_1A_ProfileEdit**, i criteri personalizzati dalla relying party caricati in precedenza. Selezionare **Esegui adesso**.
3.  Dovrebbe essere possibile accedere usando un account AD FS.

## <a name="download-the-complete-policy-files"></a>Scaricare i file dei criteri completi
Facoltativo: per creare lo scenario è consigliabile usare file di criteri personalizzati dopo aver completato la procedura Introduzione ai criteri personalizzati. [File dei criteri di esempio solo per riferimento](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-adfs2016-app)
