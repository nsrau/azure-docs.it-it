---
title: 'Esercitazione: Integrazione di Azure Active Directory con SharePoint locale | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SharePoint locale.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: 1a52fc28d99e0f072323e5042257baec9bf98ecb
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518383"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-sharepoint-on-premises"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con SharePoint locale

Questa esercitazione descrive come integrare SharePoint locale con Azure Active Directory (Azure AD). Integrando SharePoint locale con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a SharePoint locale.
* Abilitare gli utenti per l'accesso automatico a SharePoint locale con gli account Azure AD personali.
* Gestire gli account nel portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con SharePoint locale, sono necessari questi elementi:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Farm di SharePoint 2013 o versioni successive.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On (SSO) di Azure AD in un ambiente di test. Gli utenti di Azure AD possono accedere a SharePoint locale.

## <a name="create-enterprise-applications-in-the-azure-portal"></a>Creare applicazioni aziendali nel portale di Azure

Per configurare l'integrazione di SharePoint locale in Azure AD, è necessario aggiungere SharePoint locale dalla raccolta all'elenco di app SaaS gestite.

Per aggiungere SharePoint locale dalla raccolta:

1. Nel portale di Azure selezionare **Azure Active Directory** nel riquadro all'estrema sinistra.

   > [!NOTE]
   > Se l'elemento non è disponibile, è possibile aprirlo anche tramite il collegamento **Tutti i servizi** nella parte superiore del riquadro all'estrema sinistra. Nella panoramica seguente il collegamento **Azure Active Directory** si trova nella sezione **Identità** . In alternativa, è possibile cercarlo usando la casella di testo di filtro.

1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni** .

1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione** nella parte superiore della finestra di dialogo.

1. Nella casella di ricerca immettere **SharePoint locale** . Selezionare **SharePoint locale** nel riquadro dei risultati.

    <kbd>![SharePoint locale nell'elenco risultati](./media/sharepoint-on-premises-tutorial/search-new-app.png)</kbd>

1. Specificare un nome per l'istanza di SharePoint locale e selezionare **Aggiungi** per aggiungere l'applicazione.

1. Nella nuova applicazione aziendale selezionare **Proprietà** e controllare il valore per **Assegnazione utenti obbligatoria** .

   <kbd>![Attivazione/Disattivazione di Assegnazione utenti obbligatoria](./media/sharepoint-on-premises-tutorial/user-assignment-required.png)</kbd>

   In questo scenario il valore è impostato su **No** .

## <a name="configure-and-test-azure-ad"></a>Configurare e testare l'accesso SSO di Azure AD

In questa sezione viene configurato l'accesso Single Sign-On di Azure AD con SharePoint locale. Per consentire il funzionamento dell'accesso Single Sign-On, stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SharePoint locale.

Per configurare e testare l'accesso SSO di Azure AD con SharePoint locale, completare le procedure di base seguenti:

- [Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso): per consentire agli utenti di usare questa funzionalità.
- [Configurare l'accesso Single Sign-On di SharePoint locale](#configure-sharepoint-on-premises): per configurare le impostazioni di Single Sign-On sul lato applicazione.
- [Creare un utente di test di Azure AD nel portale di Azure](#create-an-azure-ad-test-user-in-the-azure-portal): per creare in Azure AD un nuovo utente per l'accesso Single Sign-On.
- [Creare un gruppo di sicurezza di Azure AD nel portale di Azure](#create-an-azure-ad-security-group-in-the-azure-portal): per creare un nuovo gruppo di sicurezza in Azure AD per l'accesso Single Sign-On.
- [Concedere le autorizzazioni a un account Azure Active Directory in SharePoint locale](#grant-permissions-to-an-azure-ad-account-in-sharepoint-on-premises): per concedere le autorizzazioni a un utente di Azure AD.
- [Concedere le autorizzazioni a un gruppo di Azure AD in SharePoint locale](#grant-permissions-to-an-azure-ad-group-in-sharepoint-on-premises): per concedere le autorizzazioni a un gruppo di Azure AD.
- [Concedere l'accesso a un account guest per SharePoint locale nel portale di Azure](#grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal): per concedere le autorizzazioni a un account guest in Azure AD per SharePoint locale.
- [Configurare il provider di identità attendibili per più applicazioni Web](#configure-the-trusted-identity-provider-for-multiple-web-applications): per usare lo stesso provider di identità attendibili per più applicazioni Web.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

In questa sezione si abilita l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con SharePoint locale:

1. Nel portale di Azure selezionare **Azure Active Directory** > **Applicazioni aziendali** . Selezionare il nome dell'applicazione aziendale creato in precedenza e selezionare **Single Sign-On** .

1. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML** per abilitare l'accesso Single Sign-On.
 
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** selezionare l'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base** .

1. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di SharePoint locale](./media/sharepoint-on-premises-tutorial/sp-identifier-reply.png)

    1. Nella casella **Identificatore** immettere un URL nel formato seguente: `urn:<sharepointFarmName>:<federationName>`.

    1. Nella casella **URL di risposta** immettere un URL nel formato seguente: `https://<YourSharePointSiteURL>/_trust/`.

    1. Nella casella **URL di accesso** immettere un URL nel formato seguente: `https://<YourSharePointSiteURL>/`.
    1. Selezionare **Salva** .

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'URL di accesso, l'identificatore e l'URL di risposta effettivi.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** selezionare **Scarica** per scaricare il **certificato (Base64)** definito dalle opzioni specificate in base alle esigenze e salvarlo nel computer.

    ![Collegamento di download del certificato](./media/sharepoint-on-premises-tutorial/certificatebase64.png)

1. Nella sezione **Configura SharePoint locale** copiare gli URL appropriati in base alle esigenze:
    
    - **URL di accesso**
    
        Copiare l'URL di accesso e sostituire **/saml2** alla fine con **/wsfed** in modo che sia simile a https://login.microsoftonline.com/2c4f1a9f-be5f-10ee-327d-a95dac567e4f/wsfed. Questo URL non è accurato.

    - **Identificatore Azure AD**
    - **URL di disconnessione**

    > [!NOTE]
    > Questo URL non può essere usato così com'è in SharePoint. È necessario sostituire **/saml2** con **/wsfed** . L'applicazione SharePoint locale usa il token SAML 1.1, di conseguenza Azure AD attende una richiesta di WS Fed dal server SharePoint e, dopo l'autenticazione, rilascia il token SAML 1.1.

### <a name="configure-sharepoint-on-premises"></a>Configurare l'accesso Single Sign-On di SharePoint locale

1. Creare un nuovo provider di identità attendibili in SharePoint Server 2016.

    Accedere al server SharePoint e aprire la shell di gestione SharePoint. Immettere i valori seguenti:
    - **$realm** è il valore dell'identificatore indicato nella sezione URL e dominio di SharePoint locale nel portale di Azure.
    - **$wsfedurl** è l'URL del servizio di accesso Single Sign-On.
    - **$filepath** è il percorso in cui è stato scaricato il file del certificato dal portale di Azure.

    Eseguire i comandi seguenti per configurare un nuovo provider di identità attendibili.

    > [!TIP]
    > Se non si ha familiarità con l'uso di PowerShell o per altre informazioni sul funzionamento di PowerShell, vedere [SharePoint PowerShell](/powershell/sharepoint/overview?view=sharepoint-ps).


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
1. Abilitare il provider di identità attendibili per l'applicazione.

    1. In **Amministrazione centrale** passare a **Gestione applicazione Web** e selezionare l'applicazione Web che si vuole proteggere con Azure AD.

    1. Nella barra multifunzione selezionare **Provider di autenticazione** e selezionare la zona che si vuole usare.

    1. Selezionare **Provider di identità attendibili** e selezionare il provider di identità appena registrato denominato *AzureAD* .

    1. Selezionare **OK** .

    ![Configurare il provider di autenticazione](./media/sharepoint-on-premises-tutorial/config-auth-provider.png)

### <a name="create-an-azure-ad-test-user-in-the-azure-portal"></a>Creare un utente di test di Azure AD nel portale di Azure

Questa sezione descrive come creare un utente di test nel portale di Azure.

1. Nel portale di Azure selezionare **Azure Active Directory** nel riquadro all'estrema sinistra. Nel riquadro **Gestione** selezionare **Utenti** .

1. Selezionare **Tutti gli utenti** > **Nuovo utente** nella parte superiore della schermata.

1. Selezionare **Crea utente** e nelle proprietà utente seguire questa procedura. Sarà possibile creare utenti in Azure AD usando il suffisso del tenant o qualsiasi dominio verificato. 

    1. Nella casella **Nome** immettere il nome utente. In questo esempio è stato usato **TestUser** .
  
    1. Nella casella **Nome utente** immettere `TestUser@yourcompanydomain.extension`. In questo esempio viene visualizzato `TestUser@contoso.com`.

       ![Finestra di dialogo Utente](./media/sharepoint-on-premises-tutorial/user-properties.png)

    1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password** .

    1. Selezionare **Create** (Crea).

    1. È ora possibile condividere il sito con TestUser@contoso.com e consentire all'utente di accedervi.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Creare un gruppo di sicurezza di Azure AD nel portale di Azure

1. Selezionare **Azure Active Directory** > **Gruppi** .

1. Selezionare **Nuovo gruppo** .

1. Compilare le caselle **Tipo di gruppo** , **Nome gruppo** , **Descrizione gruppo** e **Tipo di appartenenza** . Selezionare le frecce per selezionare i membri e quindi cercare o selezionare i membri da aggiungere al gruppo. Scegliere **Seleziona** per aggiungere i membri selezionati e quindi selezionare **Crea** .

![Creare un gruppo di sicurezza di Azure AD](./media/sharepoint-on-premises-tutorial/new-group.png)

### <a name="grant-permissions-to-an-azure-ad-account-in-sharepoint-on-premises"></a>Concedere le autorizzazioni a un account Azure AD in SharePoint locale

Per concedere l'accesso a un utente Azure AD in SharePoint locale, condividere la raccolta siti o aggiungere l'utente Azure AD a uno dei gruppi della raccolta siti. Gli utenti possono ora accedere a SharePoint 201x con identità di Azure AD, ma l'esperienza utente può ancora migliorare. Ad esempio, la ricerca di un utente presenta molteplici risultati della ricerca nella selezione utenti. È disponibile un risultato di ricerca per ognuno dei tipi di attestazioni creati nel mapping delle attestazioni. Per scegliere un utente con la selezione utenti, è necessario immettere esattamente il nome utente e scegliere il risultato dell'attestazione **name** .

![Risultati della ricerca di attestazioni](./media/sharepoint-on-premises-tutorial/claims-search-results.png)

Non essendo previste convalide per i valori cercati, possono verificarsi errori di ortografia o la scelta accidentale del tipo errato di attestazione. Questa situazione può impedire agli utenti di accedere correttamente alle risorse.

Per risolvere i problemi relativi alla selezione utenti in questo scenario, è disponibile una soluzione open source denominata [AzureCP](https://yvand.github.io/AzureCP/) che fornisce un provider di attestazioni personalizzate per SharePoint 2013, 2016 e 2019. Tale soluzione usa l'API Microsoft Graph per risolvere i valori immessi dagli utenti ed eseguire la convalida. Per altre informazioni, vedere [AzureCP](https://yvand.github.io/AzureCP/).

  > [!NOTE]
  > Senza AzureCP è possibile aggiungere gruppi aggiungendo l'ID del gruppo di Azure AD, ma questo metodo non è agevole né affidabile per l'utente. Ecco come appare:
  > 
  >![Aggiungere un gruppo di Azure AD a un gruppo di SharePoint in base all'ID](./media/sharepoint-on-premises-tutorial/adding-group-by-id.png)
  
### <a name="grant-permissions-to-an-azure-ad-group-in-sharepoint-on-premises"></a>Concedere le autorizzazioni a un gruppo di Azure AD in SharePoint locale

Per assegnare gruppi di sicurezza di Azure Active Directory a SharePoint in locale, è necessario usare un provider di attestazioni personalizzato per il server SharePoint. In questo esempio si usa AzureCP.

 > [!NOTE]
 > AzureCP non è un prodotto Microsoft e non è supportato dal supporto tecnico Microsoft. Per scaricare, installare e configurare AzureCP nella farm SharePoint locale, visitare il sito Web [AzureCP](https://yvand.github.io/AzureCP/). 

1. Configurare AzureCP nella farm SharePoint locale o in una soluzione alternativa per provider di attestazioni personalizzato. Per configurare AzureCP, visitare il sito Web [AzureCP](https://yvand.github.io/AzureCP/Register-App-In-AAD.html).

1. Nel portale di Azure selezionare **Azure Active Directory** > **Applicazioni aziendali** . Selezionare il nome dell'applicazione aziendale creato in precedenza e selezionare **Single Sign-On** .

1. Nella pagina **Configura l'accesso Single Sign-On con SAML** modificare la sezione **Attributi utente e attestazioni** .

1. Selezionare **Aggiungi un'attestazione basata su gruppo** .

1. Selezionare i gruppi associati all'utente che devono essere restituiti nell'attestazione. In questo caso selezionare **Tutti i gruppi** . Nella sezione **Attributo di origine** selezionare **ID gruppo** e quindi **Salva** .

Per concedere l'accesso al gruppo di sicurezza di Azure AD in SharePoint locale, condividere la raccolta siti o aggiungere il gruppo di sicurezza di Azure AD a uno dei gruppi della raccolta siti.

1. Passare a **Raccolta siti di SharePoint** . In **Impostazioni sito** per la raccolta siti selezionare **Utenti e gruppi** . 

1. Selezionare il gruppo di SharePoint e quindi selezionare **Nuovo** > **Aggiungi utenti a questo gruppo** . Quando si digita il nome del gruppo, la selezione utenti visualizza il gruppo di sicurezza di Azure AD.

    ![Aggiungere un gruppo di Azure AD a un gruppo di SharePoint](./media/sharepoint-on-premises-tutorial/permission-azure-ad-group.png)

### <a name="grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal"></a>Concedere l'accesso a un account guest per SharePoint locale nel portale di Azure

È possibile concedere a un account guest l'accesso al sito di SharePoint in modo coerente perché il nome di accesso UPN ora viene modificato. Ad esempio, l'utente `jdoe@outlook.com` viene rappresentato come `jdoe_outlook.com#ext#@TENANT.onmicrosoft.com`. Per condividere il sito con utenti esterni, è necessario apportare alcune modifiche nella sezione **Attributi utente e attestazioni** del portale di Azure.

1. Nel portale di Azure selezionare **Azure Active Directory** > **Applicazioni aziendali** . Selezionare il nome dell'applicazione aziendale creato in precedenza e selezionare **Single Sign-On** .

1. Nella pagina **Configura l'accesso Single Sign-On con SAML** modificare la sezione **Attributi utente e attestazioni** .

1. Nella zona **Attestazione obbligatoria** selezionare **Identificatore utente univoco (ID nome)** .

1. Modificare il valore della proprietà **Attributo di origine** in quello di **user.localuserprincipalname** e selezionare **Salva** .

    ![Attributo di origine iniziale in Attributi utente e attestazioni](./media/sharepoint-on-premises-tutorial/manage-claim.png)

1. Usare la barra multifunzione per tornare a **Accesso basato su SAML** . A questo punto la sezione **Attributi utente e attestazioni** è simile a quella illustrata di seguito: 

    ![Attributi utente e attestazioni - Finale](./media/sharepoint-on-premises-tutorial/user-attributes-claims-final.png)

    > [!NOTE]
    > In questa configurazione non è obbligatorio specificare cognome e nome.

1. Nel portale di Azure selezionare **Azure Active Directory** nel riquadro all'estrema sinistra e quindi selezionare **Utenti** .

1. Selezionare **Nuovo utente guest** .

1. Selezionare l'opzione **Invita utente** . Compilare le proprietà utente e selezionare **Invita** .

1. È ora possibile condividere il sito con MyGuestAccount@outlook.com e consentire all'utente di accedervi.

    ![Condivisione di un sito con un account guest](./media/sharepoint-on-premises-tutorial/sharing-guest-account.png)

### <a name="configure-the-trusted-identity-provider-for-multiple-web-applications"></a>Configurare il provider di identità attendibili per più applicazioni Web

La configurazione funziona per una singola applicazione Web, ma deve essere modificata ulteriormente se si prevede di usare lo stesso provider di identità attendibili per più applicazioni Web. Si supponga ad esempio di aver esteso un'applicazione Web in modo che usi l'URL `https://sales.contoso.com` e di voler ora autenticare anche gli utenti di `https://marketing.contoso.com`. A questo scopo, aggiornare il provider di identità in modo che rispetti il parametro WReply e aggiorni la registrazione dell'applicazione in Azure AD per aggiungere un URL di risposta.

1. Nel portale di Azure selezionare **Azure Active Directory** > **Applicazioni aziendali** . Selezionare il nome dell'applicazione aziendale creato in precedenza e selezionare **Single Sign-On** .

1. Nella pagina **Configura l'accesso Single Sign-On con SAML** modificare la sezione **Configurazione SAML di base** .

    ![Configurazione SAML di base](./media/sharepoint-on-premises-tutorial/add-reply-url.png)

1. In **URL di risposta (URL del servizio consumer di asserzione)** aggiungere l'URL delle applicazioni Web aggiuntive e selezionare **Salva** .

    ![Modificare la configurazione SAML di base](./media/sharepoint-on-premises-tutorial/reply-url-for-web-application.png)

1. Nel server SharePoint aprire la shell di gestione di SharePoint 201x ed eseguire i comandi seguenti. Usare il nome dell'autorità emittente di token di identità attendibili usato in precedenza.
    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
1. In **Amministrazione centrale** passare all'applicazione Web e abilitare il provider di identità attendibili esistente.

In altri scenari si potrebbe voler concedere l'accesso all'istanza di SharePoint locale per gli utenti interni. In questo caso è necessario distribuire Microsoft Azure Active Directory Connect per consentire la sincronizzazione degli utenti locali con Azure AD. Questa configurazione è descritta in un altro articolo.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato SharePoint locale, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)