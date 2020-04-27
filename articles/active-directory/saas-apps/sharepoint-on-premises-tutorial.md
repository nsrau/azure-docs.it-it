---
title: 'Esercitazione: Integrazione di Azure Active Directory con SharePoint locale | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SharePoint locale.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 85b8d4d0-3f6a-4913-b9d3-8cc327d8280d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/15/2020
ms.author: miguego
ms.openlocfilehash: 7e47891a74feb60b0f3e4594bd1621e8b62d64d1
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81867110"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sharepoint-on-premises"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con SharePoint locale

Questa esercitazione descrive come integrare SharePoint locale con Azure Active Directory (Azure AD). Integrando SharePoint locale con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a SharePoint locale.
* Abilitare gli utenti per l'accesso automatico a SharePoint locale con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure Active Directory con SharePoint locale, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure Active Directory. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Farm di SharePoint 2013 o versioni successive.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure Active Directory in un ambiente di test per consentire agli utenti di Azure Active Directory di accedere a SharePoint locale.

## <a name="create-the-enterprise-applications-in-azure-portal"></a>Creare le applicazioni aziendali nel portale di Azure

Per configurare l'integrazione di SharePoint locale in Azure AD, è necessario aggiungere SharePoint locale dalla raccolta all'elenco di app SaaS gestite.

Per aggiungere SharePoint locale dalla raccolta, seguire questa procedura:

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

 > [!NOTE]
 > Se l'elemento non è disponibile, può essere aperto anche tramite il collegamento **Tutti i servizi** corretto nella parte superiore del riquadro di spostamento sinistro. Nella panoramica seguente il collegamento **Azure Active Directory** si trova nella sezione **Identità** oppure può essere cercato usando la casella di testo di filtro.

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

4. Nella casella di ricerca digitare **SharePoint locale** e selezionare **SharePoint locale** nel pannello dei risultati.

    <kbd>SharePoint locale nell'elenco risultati![](./media/sharepoint-on-premises-tutorial/search-new-app.png)</kbd>

1. Specificare un nome per l'istanza di SharePoint locale e fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

1. Nella nuova applicazione aziendale fare clic su Proprietà e controllare il valore per **Assegnazione utenti obbligatoria**.

   <kbd>![SharePoint locale nell'elenco risultati](./media/sharepoint-on-premises-tutorial/user-assignment-required.png)</kbd>

in questo scenario questo valore è impostato su **No**.

## <a name="configure-and-test-azure-ad"></a>Configurare e testare l'accesso SSO di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con SharePoint locale.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SharePoint locale.

Per configurare e testare l'accesso Single Sign-On di Azure Active Directory con SharePoint locale, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
1. **[Configurare l'accesso Single Sign-On di SharePoint locale](#configure-sharepoint-on-premises)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
1. **[Creare un utente di test di Azure AD nel portale di Azure](#create-an-azure-ad-test-user-in-the-azure-portal)** : per creare in Azure AD un nuovo utente per l'accesso Single Sign-On.
1. **[Creare un gruppo di sicurezza di Azure AD nel portale di Azure](#create-an-azure-ad-security-group-in-the-azure-portal)** : per creare un nuovo gruppo di sicurezza in Azure AD per l'accesso Single Sign-On.
1. **[Concedere le autorizzazioni all'account Azure Active Directory in SharePoint locale](#grant-permissions-to-azure-active-directory-account-in-sharepoint-on-premises)** : per concedere le autorizzazioni all'utente di Azure AD.
1. **[Concedere le autorizzazioni al gruppo di Azure AD in SharePoint locale](#grant-permissions-to-azure-ad-group-in-sharepoint-on-premises)** : per concedere le autorizzazioni al gruppo di Azure AD.
1. **[Concedere l'accesso a un account Guest per SharePoint locale nel portale di Azure](#grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal)** : per concedere le autorizzazioni all'account Guest in Azure AD per SharePoint locale.
1. **[Configurazione del provider di identità attendibili per più applicazioni Web](#configuring-the-trusted-identity-provider-for-multiple-web-applications)** : per usare lo stesso provider di identità attendibili per più applicazioni Web.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con SharePoint locale, seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com/) aprire la directory di Azure AD, fare clic su **Applicazioni aziendali**, quindi sul **nome dell'applicazione aziendale creata in precedenza** e infine su **Single Sign-On**.

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** fare clic sulla modalità **SAML** per abilitare l'accesso Single Sign-On.
 
3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di SharePoint locale](./media/sharepoint-on-premises-tutorial/sp-identifier-reply.png)

    1. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `urn:<sharepointFarmName>:<federationName>`

    1. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<YourSharePointSiteURL>/_trust/`

    1. Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<YourSharePointSiteURL>/`
    1. Fare clic su Salva.

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di accesso effettivi.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](./media/sharepoint-on-premises-tutorial/certificatebase64.png)

6. Nella sezione **Configura SharePoint locale** copiare gli URL appropriati in base alle esigenze.
    
    1. **URL di accesso**
    
        Copiare l'URL di accesso sostituendo **/saml2** alla fine con **/wsfed**, in modo che l'URL sia simile al seguente: **https://login.microsoftonline.com/2c4f1a9f-be5f-10ee-327d-a95dac567e4f/wsfed** (questo URL non è accurato)

    1. **Identificatore Azure AD**
    1. **URL di disconnessione**
    > [!NOTE]
    > Questo URL non può essere usato così com'è in SharePoint, ma è necessario sostituire **/saml2** con **/wsfed**. L'applicazione SharePoint locale usa il token SAML 1.1, quindi Azure AD attende la richiesta di WS Fed dal server di SharePoint e, dopo l'autenticazione, emette il token SAML 1.1.

### <a name="configure-sharepoint-on-premises"></a>Configurare l'accesso Single Sign-On di SharePoint locale

1. **Creare un nuovo provider di identità attendibili in SharePoint Server 2016**

    Accedere al server di SharePoint e aprire la shell di gestione SharePoint. Immettere i valori seguenti:
    1. **$realm** (valore dell'identificatore indicato nella sezione URL e dominio di SharePoint locale nel portale di Azure).
    1. **$wsfedurl** (URL del servizio Single Sign-On).
   1. **$filepath** (percorso in cui è stato scaricato il file del certificato dal portale di Azure).

    Eseguire i comandi seguenti per configurare un nuovo provider di identità attendibili.

    > [!TIP]
    > Se non si ha familiarità con l'uso di PowerShell o per altre informazioni sul funzionamento di PowerShell, vedere [SharePoint PowerShell](https://docs.microsoft.com/powershell/sharepoint/overview?view=sharepoint-ps).


    ```
    $realm = "urn:sharepoint:sps201x"
    $wsfedurl="https://login.microsoftonline.com/2c4f1a9f-be5f-10ee-327d-a95dac567e4f/wsfed"
    $filepath="C:\temp\SharePoint 2019 OnPrem.cer"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($filepath)
    New-SPTrustedRootAuthority -Name "AzureAD" -Certificate $cert
    $map1 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
    $map2 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.microsoft.com/ws/2008/06/identity/claims/role" -IncomingClaimTypeDisplayName "Role" -SameAsIncoming
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "Azure AD SharePoint server 201x" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map1,$map2 -SignInUrl $wsfedurl -IdentifierClaim $map1.InputClaimType
    ```
1. **Abilitare il provider di identità attendibili per l'applicazione**

    a. In Amministrazione centrale passare a **Gestione applicazione Web** e selezionare l'applicazione Web che si vuole proteggere con Azure AD.

    b. Nella barra multifunzione fare clic su **Provider di autenticazione** e selezionare la zona che si vuole usare.

    c. Selezionare **Provider di identità attendibili** e selezionare il provider di identità appena registrato denominato *AzureAD*.

    d. Fare clic su **OK**.

    ![Configurare il provider di autenticazione](./media/sharepoint-on-premises-tutorial/config-auth-provider.png)

### <a name="create-an-azure-ad-test-user-in-the-azure-portal"></a>Creare un utente di test di Azure AD nel portale di Azure

Questa sezione descrive come creare un utente di test nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory** e quindi nel riquadro **Gestione** selezionare **Utenti**.

2. Selezionare quindi **Tutti gli utenti** seguito da **Nuovo utente** nella parte superiore della schermata.

3. Selezionare l'opzione **Crea utente** e nelle proprietà utente seguire questa procedura.  
   Sarà possibile creare utenti in Azure AD usando il suffisso del tenant o qualsiasi dominio verificato. 

    a. Nel campo **Nome** immettere il nome utente; in questo caso è stato usato **TestUser**.
  
    b. Nel campo **Nome utente** digitare `TestUser@yourcompanydomain.extension`.  
    Ad esempio, usare TestUser@contoso.com

    ![Finestra di dialogo Utente](./media/sharepoint-on-premises-tutorial/user-properties.png)

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Crea**.

    e. È ora possibile condividere il sito con TestUser@contoso.com e consentire all'utente di accedervi.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Creare un gruppo di sicurezza di Azure AD nel portale di Azure

1. Fare clic su **Azure Active Directory > Gruppi**.

2. Fare clic su **Nuovo gruppo**:

3. Compilare **Tipo di gruppo**, **Nome gruppo**, **Descrizione gruppo**, **Tipo di appartenenza**. Fare clic sulla freccia per selezionare i membri e quindi cercare o fare clic sul membro che si desidera aggiungere al gruppo. Fare clic su **Seleziona** per aggiungere i membri selezionati e quindi fare clic su **Crea**.

![Creare un gruppo di sicurezza di Azure AD](./media/sharepoint-on-premises-tutorial/new-group.png)

### <a name="grant-permissions-to-azure-active-directory-account-in-sharepoint-on-premises"></a>Concedere le autorizzazioni all'account Azure Active Directory in SharePoint locale

Per concedere l'accesso all'utente di Azure Active Directory in SharePoint locale, è necessario condividere la raccolta siti o aggiungere l'utente di Azure Active Directory a uno dei gruppi della raccolta siti. Gli utenti possono ora accedere a SharePoint 201X usando le identità di Azure AD, ma l'esperienza utente non è ancora definitiva. Ad esempio, la ricerca di un utente presenta molteplici risultati della ricerca in Selezione persone. È disponibile un risultato di ricerca per ognuno dei tipi di attestazioni creati nel mapping delle attestazioni. Per scegliere un utente con Selezione persone, è necessario digitare esattamente il nome utente e scegliere il risultato dell'attestazione **name**.

![Risultati della ricerca di attestazioni](./media/sharepoint-on-premises-tutorial/claims-search-results.png)

Non essendo previste convalide per i valori cercati, possono verificarsi errori di ortografia o la scelta accidentale del tipo errato di attestazione. Questo può impedire agli utenti di accedere correttamente alle risorse.

**Per risolvere i problemi relativi a Selezione persone** in questo scenario, è disponibile una soluzione open source denominata [AzureCP](https://yvand.github.io/AzureCP/) che fornisce un provider di attestazioni personalizzate per SharePoint 2013, 2016 e 2019. Tale soluzione userà l'API Microsoft Graph per risolvere i valori immessi dagli utenti ed eseguire la convalida. Altre informazioni su [AzureCP](https://yvand.github.io/AzureCP/).

  > [!NOTE]
  > Senza AzureCP è possibile aggiungere gruppi aggiungendo l'ID del gruppo di Azure AD, ma questo approccio non è agevole né affidabile per l'utente. Ecco come funziona:  
  >   
  >![Aggiungere il gruppo di Azure AD al gruppo di SharePoint](./media/sharepoint-on-premises-tutorial/adding-group-by-id.png)
  
### <a name="grant-permissions-to-azure-ad-group-in-sharepoint-on-premises"></a>Concedere le autorizzazioni al gruppo di Azure AD in SharePoint locale

Per assegnare gruppi di sicurezza di Azure Active Directory a SharePoint in locale, sarà necessario usare un provider di attestazioni personalizzato per SharePoint Server. In questo esempio è stato usato AzureCP.

 > [!NOTE]
 > Si noti che AzureCP non è un prodotto Microsoft o supportato dal team di supporto Microsoft. Scaricare, installare e configurare AzureCP nella farm SharePoint locale secondo le istruzioni riportate in https://yvand.github.io/AzureCP/. 

1. Configurare AzureCP nella farm SharePoint locale o in una soluzione di provider di attestazioni personalizzate alternative. La procedura di configurazione di AzureCP è disponibile all'indirizzo https://yvand.github.io/AzureCP/Register-App-In-AAD.html.

1. Nel portale di Azure aprire la directory di Azure AD. Fare clic su **Applicazioni aziendali**, quindi sul **nome dell'applicazione aziendale creata in precedenza** e infine su **Single Sign-On**.

1. Nella pagina **Configura l'accesso Single Sign-On con SAML** modificare la sezione **Attributi utente e attestazioni**.

1. Fare clic su **Aggiungi un'attestazione basata su gruppo**.

1. Selezionare i gruppi associati all'utente che devono essere restituiti nell'attestazione. In questo caso selezionare **Tutti i gruppi** e nella sezione Attributo di origine selezionare **ID gruppo** e fare clic su **Salva**.

Per concedere l'accesso al gruppo di sicurezza di Azure Active Directory in SharePoint locale, è necessario condividere la raccolta siti o aggiungere il gruppo di sicurezza di Azure Active Directory a uno dei gruppi della raccolta siti.

1. Passare alla raccolta siti di SharePoint e in Impostazioni sito per la raccolta siti, fare clic su "Utenti e gruppi". Selezionare il gruppo di SharePoint, quindi fare clic su Nuovo, "Aggiungi utenti a questo gruppo" e iniziare a digitare il nome del gruppo in cui Selezione persone visualizzerà il gruppo di sicurezza di Azure Active Directory.

    ![Aggiungere il gruppo di Azure AD al gruppo di SharePoint](./media/sharepoint-on-premises-tutorial/permission-azure-ad-group.png)

### <a name="grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal"></a>Concedere l'accesso a un account Guest per SharePoint locale nel portale di Azure

È ora possibile concedere a un account Guest l'accesso al sito di SharePoint in modo coerente. Può capitare che il nome di accesso UPN venga modificato. L'utente con jdoe@outlook.com verrà rappresentato come `jdoe_outlook.com#ext#@TENANT.onmicrosoft.com`. Per garantire un'esperienza uniforme durante la condivisione del sito con utenti esterni, è quindi necessario apportare alcune modifiche alla sezione **Attributi utente e attestazioni** del portale di Azure.

1. Nel portale di Azure aprire la directory di Azure AD. Fare clic su **Applicazioni aziendali**, quindi sul **nome dell'applicazione aziendale creata in precedenza** e infine su **Single Sign-On**.

1. Nella pagina **Configura l'accesso Single Sign-On con SAML** modificare la sezione **Attributi utente e attestazioni**.

1. Nella zona **Attestazione obbligatoria** fare clic su **Identificatore utente univoco (ID nome)** .

1. Modificare il valore della proprietà **Attributo di origine** in quello di **user.localuserprincipalname** e fare clic su **Salva**.

    ![Attributi utente e attestazioni - Iniziale](./media/sharepoint-on-premises-tutorial/manage-claim.png)

1. Usare la barra multifunzione per passare a **Accesso basato su SAML**. A questo punto la sezione **Attributi utente e attestazioni** sarà simile alla seguente: 

    ![Attributi utente e attestazioni - Finale](./media/sharepoint-on-premises-tutorial/user-attributes-claims-final.png)  

    > [!NOTE]
    > Il cognome e il nome specificato non sono obbligatori in questa configurazione.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory** e quindi **Utenti**.

1. Fare clic su **Nuovo utente guest**.

1. Selezionare l'opzione **Invita l'utente**, completare le proprietà dell'utente e fare clic su **Invita**.

1. È ora possibile condividere il sito con MyGuestAccount@outlook.com e consentire all'utente di accedervi.

    ![Condivisione del sito con l'account Guest](./media/sharepoint-on-premises-tutorial/sharing-guest-account.png)

### <a name="configuring-the-trusted-identity-provider-for-multiple-web-applications"></a>Configurazione del provider di identità attendibili per più applicazioni Web

La configurazione funziona per una singola applicazione Web, ma necessita di una configurazione aggiuntiva se si prevede di usare lo stesso provider di identità attendibili per più applicazioni Web. Si supponga ad esempio di aver esteso un'applicazione Web per usare l'URL `https://sales.contoso.com` e di voler ora autenticare anche gli utenti `https://marketing.contoso.com`. A tale scopo, è necessario aggiornare il provider di identità per rispettare il parametro WReply e aggiornare la registrazione dell'applicazione in Azure AD per aggiungere un URL di risposta.

1. Nel portale di Azure aprire la directory di Azure AD. Fare clic su **Applicazioni aziendali**, quindi sul **nome dell'applicazione aziendale creata in precedenza** e infine su **Single Sign-On**.

2. Nella pagina **Configura l'accesso Single Sign-On con SAML** modificare la sezione **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](./media/sharepoint-on-premises-tutorial/add-reply-url.png)

3. In **URL di risposta (URL del servizio consumer di asserzione)** aggiungere l'URL delle applicazioni Web aggiuntive e fare clic su **Salva**.

    ![Modificare la configurazione SAML di base](./media/sharepoint-on-premises-tutorial/reply-url-for-web-application.png)

4. Nel server di SharePoint aprire la **shell di gestione SharePoint 201x** ed eseguire i comandi seguenti, usando il nome dell'autorità di certificazione del token di identità attendibili adoperato in precedenza.
    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
5. In Amministrazione centrale passare all'applicazione Web e abilitare il provider di identità attendibili esistente.

È possibile che in altri scenari si voglia concedere l'accesso a SharePoint locale per gli utenti interni. Per questo scenario è necessario distribuire Microsoft Azure Active Directory Connect che consentirà di sincronizzare gli utenti locali con Azure Active Directory. Questa configurazione potrebbe essere illustrata in un altro articolo. 

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Che cos'è l'identità ibrida con Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity)
