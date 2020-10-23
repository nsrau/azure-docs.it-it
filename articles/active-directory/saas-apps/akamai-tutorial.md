---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Akamai | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Akamai.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/03/2020
ms.author: jeedes
ms.openlocfilehash: bbea6a0aa31034cd1c04145fb50b72432c9f8520
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92319007"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-akamai"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Akamai

Questa esercitazione descrive come integrare Akamai con Azure Active Directory (Azure AD). Integrando Akamai con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere ad Akamai.
* Abilitare gli utenti per l'accesso automatico ad Akamai con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

Grazie all'integrazione di Azure Active Directory e Akamai Enterprise Application Access (EAA) è possibile accedere senza problemi alle applicazioni legacy ospitate nel cloud o in locale. La soluzione integrata sfrutta i vantaggi di tutte le moderne funzionalità di Azure Active Directory come l'[accesso condizionale di Azure AD](../conditional-access/overview.md), [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md) e [Azure AD Identity Governance](../governance/identity-governance-overview.md) per l'accesso ad applicazioni legacy senza modifiche delle app o installazione di agenti.

L'immagine seguente illustra la posizione occupata da Akamai EAA nel più ampio scenario di accesso sicuro ibrido

![Integrazione di Akamai EAA nel più ampio scenario di accesso sicuro ibrido](./media/header-akamai-tutorial/introduction01.png)

### <a name="key-authentication-scenarios"></a>Scenari di autenticazione principali

Oltre allo supporto dell'integrazione nativa di Azure Active Directory per i protocolli di autenticazione moderni, come Open ID Connect, SAML e WS-Fed, Akamai EAA estende l'accesso sicuro per le app di autenticazione basate su scenari legacy per l'accesso interno ed esterno con Azure AD, abilitando scenari moderni (ad esempio, l'accesso senza password) per queste applicazioni, ad esempio:

* App di autenticazione basata su intestazione
* Desktop remoto
* SSH (Secure Shell)
* App di autenticazione Kerberos
* VNC (Virtual Network Computing)
* Autenticazione anonima oppure nessuna app di autenticazione incorporata
* App di autenticazione NTLM (protezione con doppio prompt per l'utente)
* Applicazione basata su moduli (protezione con doppio prompt per l'utente)

### <a name="integration-scenarios"></a>Scenari di integrazione

La partnership tra Microsoft e Akamai EAA offre la flessibilità necessaria per soddisfare i requisiti aziendali, grazie al supporto di più scenari di integrazione basati su requisiti aziendali. È possibile usare questi scenari per offrire una copertura da attacchi zero-day per tutte le applicazioni e per classificare e configurare gradualmente classificazioni dei criteri appropriate.

#### <a name="integration-scenario-1"></a>Scenario di integrazione 1

Akamai EAA è configurato come una singola applicazione in Azure AD. L'amministratore può configurare i criteri della CA nell'applicazione e, una volta soddisfatte le condizioni, gli utenti possono accedere al portale di Akamai EAA.

**Vantaggi**:

* È necessario configurare l'IDP una sola volta

**Svantaggi**:

* Gli utenti devono interagire con due portali di applicazioni

* La copertura dei criteri della CA è unica e comune per tutte le applicazioni.

![Scenario di integrazione 1](./media/header-akamai-tutorial/scenario1.png)

#### <a name="integration-scenario-2"></a>Scenario di integrazione 2

L'applicazione Akamai EAA viene configurata singolarmente nel portale di Azure AD. L'amministratore può configurare i singoli criteri della CA per le applicazioni e, una volta soddisfatte le condizioni, gli utenti possono essere reindirizzati direttamente all'applicazione specifica.

**Vantaggi**:

* È possibile definire singoli criteri della CA

* Tutte le app sono rappresentate nel menu a cialda di O365 e nel pannello di myApps.microsoft.com.


**Svantaggi**:

* È necessario configurare più IDP.

![Scenario di integrazione 2](./media/header-akamai-tutorial/scenario2.png)

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Akamai abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

- Akamai supporta l'accesso SSO avviato da IDP

#### <a name="important"></a>Importante

Tutte le impostazioni elencate di seguito sono le stesse sia per **Scenario di integrazione 1** che per lo **Scenario di integrazione 2**. Per lo **Scenario di integrazione 2** è necessario configurare un singolo IDP in Akamai EAA e modificare la proprietà URL in modo che punti all'URL dell'applicazione.

![Screenshot della scheda General per AZURESSO-SP in Akamai Enterprise Application Access. Il campo URL in Authentication configuration è evidenziato.](./media/header-akamai-tutorial/important.png)

## <a name="adding-akamai-from-the-gallery"></a>Aggiunta di Akamai dalla raccolta

Per configurare l'integrazione di Akamai in Azure AD, è necessario aggiungere Akamai dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Akamai** nella casella di ricerca.
1. Selezionare **Akamai** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-akamai"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Akamai

Configurare e testare l'accesso SSO di Azure AD con Akamai usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Akamai.

Per configurare e testare l'accesso SSO di Azure AD con Akamai, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    * **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    * **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Akamai](#configure-akamai-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    * **[Configurazione dell'IDP](#setting-up-idp)**
    * **[Autenticazione basata su intestazione](#header-based-authentication)**
    * **[Desktop remoto](#remote-desktop)**
    * **[SSH](#ssh)**
    * **[Autenticazione Kerberos](#kerberos-authentication)**
    * **[Creare l'utente di test di Akamai](#create-akamai-test-user)** : per avere una controparte di B.Simon in Akamai collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Akamai** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<Yourapp>.login.go.akamai-access.com/saml/sp/response`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https:// <Yourapp>.login.go.akamai-access.com/saml/sp/response`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di risposta effettivi. Per ottenere tali valori, contattare il [team di supporto clienti di Akamai](https://www.akamai.com/us/en/contact-us/). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il file **XML dei metadati della federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura Akamai** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad Akamai.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Akamai**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-akamai-sso"></a>Configurare l'accesso Single Sign-On di Akamai

### <a name="setting-up-idp"></a>Configurazione dell'IDP

**Configurazione dell'IDP in AKAMAI EAA**

1. Accedere alla console di **Akamai Enterprise Application Access**.
1. Nella **console di Akamai EAA** selezionare **Identity** (Identità)  > **Identity Providers** (Provider di identità) e fare clic su **Add Identity Provider** (Aggiungi provider di identità).

    ![Screenshot della finestra Identity Providers della console Akamai EAA. Selezionare Identity providers nel menu Identity e quindi Add Identity Provider.](./media/header-akamai-tutorial/configure01.png)

1. In **Create New Identity Provider** (Crea nuovo provider di identità) seguire questa procedura:

    ![Screenshot della finestra di dialogo Create New Identity Providers nella console Akamai EAA.](./media/header-akamai-tutorial/configure02.png)

    a. Specificare un valore in **Unique Name** (Nome univoco).

    b. Scegliere **Third Party SAML** (SAML di terze parti) e fare clic su **Create Identity Provider and Configure** (Crea provider di identità e configura).

### <a name="general-settings"></a>Impostazioni generali

1. **Identity Intercept** (Intercettazione identità): specificare il nome dell'URL di base del provider di servizi, che verrà usato per la configurazione di Azure AD.

    > [!NOTE]
    > È possibile scegliere di avere un dominio personalizzato, ma in tal caso saranno necessari una voce DNS e un certificato. In questo esempio verrà usato il dominio Akamai.

1. **Akamai Cloud Zone** (Zona cloud Akamai): selezionare la zona cloud appropriata.
1. **Certificate Validation** (Convalida del certificato): vedere la documentazione di Akamai (facoltativo).

    ![Screenshot della scheda General della console Akamai EAA che mostra le impostazioni per Identity Intercept, Akamai Cloud Zone e Certificate Validation.](./media/header-akamai-tutorial/configure03.png)

### <a name="authentication-configuration"></a>Configurazione dell'autenticazione

1. URL: specificare un URL identico a quello dell'intercettazione dell'identità, ovvero la posizione a cui gli utenti vengono reindirizzati dopo l'autenticazione.
2. Logout URL (URL di disconnessione): aggiornare l'URL di disconnessione.
3. Sign SAML Request (Firma richiesta SAML): opzione deselezionata per impostazione predefinita.
4. Per il file di metadati IDP, aggiungere l'applicazione nella console di Azure AD.

    ![Screenshot della schermata Authentication configuration della console Akamai EAA che mostra le impostazioni per URL, Logout URL, Sign SAML Request e IDP Metadata File.](./media/header-akamai-tutorial/configure04.png)

### <a name="session-settings"></a>Impostazioni sessione

Non modificare le impostazioni predefinite.

![Screenshot della finestra di dialogo Session settings della console Akamai EAA.](./media/header-akamai-tutorial/sessionsettings.png)

### <a name="directories"></a>Directory

Ignorare la configurazione della directory.

![Screenshot della scheda Directories della console Akamai EAA.](./media/header-akamai-tutorial/directories.png)

### <a name="customization-ui"></a>Interfaccia utente di personalizzazione

È possibile aggiungere la personalizzazione all'IDP.

![Screenshot della scheda Customization della console Akamai EAA che mostra le impostazioni per Customize UI, Language settings e Themes.](./media/header-akamai-tutorial/customizationui.png)

### <a name="advanced-settings"></a>Impostazioni avanzate

Ignorare le impostazioni avanzate. Per altri dettagli, vedere la documentazione di Akamai.

![Screenshot della scheda Advanced Settings della console Akamai EAA che mostra le impostazioni per EAA Client, Advanced e OIDC to SAML bridging.](./media/header-akamai-tutorial/advancesettings.png)

### <a name="deployment"></a>Distribuzione

1. Fare clic su Deploy Identity Provider (Distribuisci provider di identità).

    ![Screenshot della scheda Deployment della console Akamai EAA che mostra il pulsante Deploy identity provider.](./media/header-akamai-tutorial/deployment.png)

2. Verificare se la distribuzione è riuscita

### <a name="header-based-authentication"></a>Autenticazione basata su intestazione

Autenticazione basata su intestazione di Akamai

1. Scegliere **Custom HTTP** (HTTP personalizzato) in Add Applications Wizard (Aggiunta guidata applicazioni).

    ![Screenshot della procedura guidata Add Applications della console Akamai EAA che mostra l'opzione CustomHTTP nella sezione Access Apps.](./media/header-akamai-tutorial/configure05.png)

2. Immettere un valore in **Application Name** (Nome applicazione) e **Description** (Descrizione).

    ![Screenshot di una finestra di dialogo Custom HTTP App che mostra le impostazioni per Application Name e Description.](./media/header-akamai-tutorial/configure06.png)

    ![Screenshot della scheda General della console Akamai EAA che mostra le impostazioni generali per MYHEADERAPP.](./media/header-akamai-tutorial/configure07.png)

    ![Screenshot della console Akamai EAA che mostra le impostazioni per Certificate e Location.](./media/header-akamai-tutorial/configure08.png)

#### <a name="authentication"></a>Authentication

1. Selezionare la scheda **Authentication** (Autenticazione).

    ![Screenshot della console Akamai EAA con la scheda Authentication selezionata.](./media/header-akamai-tutorial/configure09.png)

2. Assegnare il **provider di identità**

    ![Screenshot della scheda Authentication della console Akamai EAA per MYHEADERAPP che mostra il provider di identità impostato su Azure AD SSO.](./media/header-akamai-tutorial/configure10.png)

#### <a name="services"></a>Servizi

Fare clic su Save and Go to Authentication (Salva e passa ad Autenticazione).

![Screenshot della scheda Services della console Akamai EAA per MYHEADERAPP che mostra il pulsante Save and go to Advanced Settings in basso a destra.](./media/header-akamai-tutorial/configure11.png)

#### <a name="advanced-settings"></a>Impostazioni avanzate

1. In **Customer HTTP Headers** (Intestazioni HTTP cliente) specificare un valore per **CustomerHeader** (Intestazione cliente) e **SAML Attribute** (Attributo SAML).

    ![Screenshot della scheda Advanced Settings della console Akamai EAA che mostra il campo SSO Logged URL evidenziato sotto Authentication.](./media/header-akamai-tutorial/configure12.png)

1. Fare clic sul pulsante **Save and go to Deployment** (Salva e passa a Distribuzione).

    ![Screenshot della scheda Advanced Settings della console Akamai EAA che mostra il pulsante Save and go to Deployment in basso a destra.](./media/header-akamai-tutorial/configure13.png)

#### <a name="deploy-the-application"></a>Distribuire l'applicazione

1. Fare clic sul pulsante **Deploy Application** (Distribuisci applicazione).

    ![Screenshot della scheda Deployment della console Akamai EAA che mostra il pulsante Deploy application.](./media/header-akamai-tutorial/configure14.png)

1. Verificare che l'applicazione sia stata distribuita correttamente.

    ![Screenshot della scheda Deployment della console Akamai EAA che mostra il messaggio di stato dell'applicazione: "Application Successfully Deployed".](./media/header-akamai-tutorial/configure15.png)

1. Esperienza dell'utente finale.

    ![Screenshot della schermata iniziale di myapps.microsoft.com con un'immagine di sfondo e una finestra di dialogo di accesso.](./media/header-akamai-tutorial/enduser01.png)

    ![Screenshot che mostra parte di una finestra Apps con le icone Add-in, HRWEB, Akamai - CorpApps, Expense, Groups e Access reviews. ](./media/header-akamai-tutorial/enduser02.png)

1. Accesso condizionale.

    ![Screenshot del messaggio: Approvare la richiesta di accesso. È stata inviata una notifica al dispositivo mobile. Per continuare, è necessario rispondere.](./media/header-akamai-tutorial/conditionalaccess01.png)

    ![Screenshot della schermata Applications che mostra un'icona per MyHeaderApp.](./media/header-akamai-tutorial/conditionalaccess02.png)

#### <a name="remote-desktop"></a>Desktop remoto

1. Scegliere **RDP** in Add Applications Wizard (Aggiunta guidata applicazioni).

    ![Screenshot della procedura guidata Add Applications della console Akamai EAA che mostra l'opzione RDP elencata tra le varie app della sezione Access Apps.](./media/header-akamai-tutorial/configure16.png)

1. Immettere un valore in **Application Name** (Nome applicazione) e **Description** (Descrizione).

    ![Screenshot di una finestra di dialogo RDP App che mostra le impostazioni per Application Name e Description.](./media/header-akamai-tutorial/configure17.png)

    ![Screenshot della scheda General della console Akamai EAA che mostra le impostazioni di identità dell'applicazione per SECRETRDPAPP.](./media/header-akamai-tutorial/configure18.png)

1. Specificare il connettore che verrà usato per questo servizio.

    ![Screenshot della console Akamai EAA che mostra le impostazioni per Certificate e Location. L'opzione Associated connectors è impostata su USWST-CON1.](./media/header-akamai-tutorial/configure19.png)

#### <a name="authentication"></a>Authentication

Fare clic su **Save and go to Services** (Salva e passa a Servizi).

![Screenshot della scheda Authentication della console Akamai EAA per SECRETRDPAPP che mostra il pulsante Save and go to Services in basso a destra.](./media/header-akamai-tutorial/configure20.png)

#### <a name="services"></a>Servizi

Fare clic su **Save and go to Advanced Settings** (Salva e passa a Impostazioni avanzate).

![Screenshot della scheda Services della console Akamai EAA per SECRETRDPAPP che mostra il pulsante Save and go to Advanced Settings in basso a destra.](./media/header-akamai-tutorial/configure21.png)

#### <a name="advanced-settings"></a>Impostazioni avanzate

1. Fare clic su **Save and go to Deployment** (Salva e passa a Distribuzione).

    ![Screenshot della scheda Advanced Settings della console Akamai EAA per SECRETRDPAPP che mostra le impostazioni per Remote desktop configuration.](./media/header-akamai-tutorial/configure22.png)

    ![Screenshot della scheda Advanced Settings della console Akamai EAA per SECRETRDPAPP che mostra le impostazioni per Authentication e Health check configuration.](./media/header-akamai-tutorial/configure23.png)

    ![Screenshot delle impostazioni di Custom HTTP headers della console Akamai EAA per SECRETRDPAPP con il pulsante Save and go to Deployment in basso a destra.](./media/header-akamai-tutorial/configure24.png)

1. Esperienza dell'utente finale

    ![Screenshot di una finestra myapps.microsoft.com con un'immagine di sfondo e una finestra di dialogo di accesso.](./media/header-akamai-tutorial/enduser03.png)

    ![Screenshot della finestra Apps di myapps.microsoft.com con le icone Add-in, HRWEB, Akamai - CorpApps, Expense, Groups e Access reviews.](./media/header-akamai-tutorial/enduser02.png)

1. Accesso condizionale

    ![Screenshot del messaggio di accesso condizionale: Approvare la richiesta di accesso. È stata inviata una notifica al dispositivo mobile. Per continuare, è necessario rispondere.](./media/header-akamai-tutorial/conditionalaccess04.png)

    ![Screenshot della schermata Applications che mostra le icone per MyHeaderApp e SecretRDPApp.](./media/header-akamai-tutorial/conditionalaccess05.png)

    ![Screenshot della schermata di Windows Server 2012 R2 che mostra icone utente generiche. Le icone per administrator, user0 e user1 indicano che gli utenti sono connessi.](./media/header-akamai-tutorial/conditionalaccess06.png)

1. In alternativa, è anche possibile digitare direttamente l'URL dell'applicazione RDP.

#### <a name="ssh"></a>SSH

1. Passare ad Add Applications (Aggiungi applicazioni) e scegliere **SSH**.

    ![Screenshot della procedura guidata Add Applications della console Akamai EAA che mostra l'opzione SSH elencata tra le varie app della sezione Access Apps.](./media/header-akamai-tutorial/configure25.png)

1. Immettere un valore in **Application Name** (Nome applicazione) e **Description** (Descrizione).

    ![Screenshot di una finestra di dialogo SSH App che mostra le impostazioni per Application Name e Description.](./media/header-akamai-tutorial/configure26.png)

1. Configurare l'identità dell'applicazione.

    ![Screenshot della scheda General della console Akamai EAA che mostra le impostazioni di identità dell'applicazione per SSH-SECURE.](./media/header-akamai-tutorial/configure27.png)

    a. Specificare nome/descrizione.

    b. Specificare IP/FQDN e porta del server applicazioni per SSH.

    c. Specificare nome utente/passphrase per SSH. *Controllare la sezione Akamai EAA.

    d. Specificare il nome host esterno.

    e. Specificare il percorso del connettore e scegliere il connettore.

#### <a name="authentication"></a>Authentication

Fare clic su **Save and go to Services** (Salva e passa a Servizi).

![Screenshot della scheda Authentication della console Akamai EAA per SSH-SECURE che mostra il pulsante Save and go to Services in basso a destra.](./media/header-akamai-tutorial/configure28.png)

#### <a name="services"></a>Servizi

Fare clic su **Save and go to Advanced Settings** (Salva e passa a Impostazioni avanzate).

![Screenshot della scheda Services della console Akamai EAA per SSH-SECURE che mostra il pulsante Save and go to Advanced Settings in basso a destra.](./media/header-akamai-tutorial/configure29.png)

#### <a name="advanced-settings"></a>Impostazioni avanzate

Fare clic su Save and go to Deployment (Salva e passa a Distribuzione).

![Screenshot della scheda Advanced Settings della console Akamai EAA per SSH-SECURE che mostra le impostazioni per Authentication e Health check configuration.](./media/header-akamai-tutorial/configure30.png)

![Screenshot delle impostazioni di Custom HTTP headers della console Akamai EAA per SSH-SECURE con il pulsante Save and go to Deployment in basso a destra.](./media/header-akamai-tutorial/configure31.png)

#### <a name="deployment"></a>Distribuzione

1. Fare clic su **Deploy Application** (Distribuisci applicazione).

    ![Screenshot della scheda Deployment della console Akamai EAA per SSH-SECURE che mostra il pulsante Deploy application.](./media/header-akamai-tutorial/configure32.png)

1. Esperienza dell'utente finale

    ![Screenshot della finestra di dialogo di accesso di una finestra myapps.microsoft.com.](./media/header-akamai-tutorial/enduser03.png)

    ![Screenshot della finestra Apps per myapps.microsoft.com che mostra le icone Add-in, HRWEB, Akamai - CorpApps, Expense, Groups e Access reviews.](./media/header-akamai-tutorial/enduser04.png)

1. Accesso condizionale

    ![Screenshot che mostra il messaggio: Approvare la richiesta di accesso. È stata inviata una notifica al dispositivo mobile. Per continuare, è necessario rispondere.](./media/header-akamai-tutorial/conditionalaccess04.png)

    ![Screenshot della schermata Applications che mostra le icone per MyHeaderApp, SSH Secure e SecretRDPApp.](./media/header-akamai-tutorial/conditionalaccess07.png)

    ![Screenshot di una finestra di comando per ssh-secure-go.akamai-access.com che visualizza un messaggio di richiesta della password.](./media/header-akamai-tutorial/conditionalaccess08.png)

    ![Screenshot di una finestra di comando per ssh-secure-go.akamai-access.com che mostra informazioni sull'applicazione e visualizza una richiesta di immissione comandi.](./media/header-akamai-tutorial/conditionalaccess09.png)

### <a name="kerberos-authentication"></a>Autenticazione Kerberos

Nell'esempio seguente si pubblicherà un server Web interno <code>http://frp-app1.superdemo.live</code> e si abiliterà l'accesso SSO con la delega vincolata Kerberos

#### <a name="general-tab"></a>Scheda Generale

![Screenshot della scheda General della console Akamai EAA per MYKERBOROSAPP.](./media/header-akamai-tutorial/generaltab.png)

#### <a name="authentication-tab"></a>Scheda Authentication

Assegnare il provider di identità

![Screenshot della scheda Authentication della console Akamai EAA per MYKERBOROSAPP che mostra il provider di identità impostato su Azure AD SSO.](./media/header-akamai-tutorial/authenticationtab.png)

#### <a name="services-tab"></a>Scheda Services

![Screenshot della scheda Services della console Akamai EAA per MYKERBOROSAPP.](./media/header-akamai-tutorial/servicestab.png)

#### <a name="advanced-settings"></a>Impostazioni avanzate

![Screenshot della scheda Advanced Settings della console Akamai EAA per MYKERBOROSAPP che mostra le impostazioni per Related Applications e Authentication.](./media/header-akamai-tutorial/advancesettings02.png)

> [!NOTE]
> Il nome SPN del server Web deve essere specificato nel formato SPN@Domain, ad esempio `HTTP/frp-app1.superdemo.live@SUPERDEMO.LIVE` per questa demo. Per le altre impostazioni, non modificare i valori predefiniti.

#### <a name="deployment-tab"></a>Scheda Deployment

![Screenshot della scheda Deployment della console Akamai EAA per MYKERBOROSAPP che mostra il pulsante Deploy application.](./media/header-akamai-tutorial/deploymenttab.png)

#### <a name="adding-directory"></a>Aggiunta della directory

1. Selezionare **AD** nell'elenco a discesa.

    ![Screenshot della finestra Directories della console Akamai EAA che mostra la finestra di dialogo Create New Directory con l'opzione AD nell'elenco a discesa per Directory Type.](./media/header-akamai-tutorial/configure33.png)

1. Fornire i dati necessari.

    ![Screenshot della finestra SUPERDEMOLIVE della console Akamai EAA con le impostazioni per Directory Name, Directory Service, Connector e Attribute mapping.](./media/header-akamai-tutorial/configure34.png)

1. Verificare la creazione della directory.

    ![Screenshot della finestra Directories della console Akamai EAA che mostra l'aggiunta della directory superdemo.live.](./media/header-akamai-tutorial/directorydomain.png)

1. Aggiungere i gruppi o le unità organizzative che richiedono l'accesso.

    ![Screenshot delle impostazioni per la directory superdemo.live. L'icona selezionata per l'aggiunta di gruppi o unità organizzative è evidenziata.](./media/header-akamai-tutorial/addgroup.png)

1. Nella figura seguente il gruppo è denominato EAAGroup e include un membro.

    ![Screenshot della finestra GROUPS ON SUPERDEMOLIVE DIRECTORY della console Akamai EAA. In Groups è elencato il gruppo EAAGroup con 1 utente.](./media/header-akamai-tutorial/eaagroup.png)

1. Per aggiungere la directory al provider di identità, fare clic su **Identity** > **Identity Providers** (Identità > Provider di identità), fare clic sulla scheda **Directories** (Directory) e quindi su **Assign directory** (Assegna directory).

    ![Screenshot della scheda Directories della console Akamai EAA per Azure AD SSO, che mostra superdemo.live nell'elenco Currently assigned directories.](./media/header-akamai-tutorial/assigndirectory.png)

### <a name="configure-kcd-delegation-for-eaa-walkthrough"></a>Procedura dettagliata per la configurazione della delega vincolata Kerberos per EAA

#### <a name="step-1-create-an-account"></a>Passaggio 1: Creare un account 

1. Nell'esempio si userà un account denominato **EAADelegation**. Per eseguire questa operazione, è possibile usare lo snap-in **Utenti e computer di Active Directory**.

    ![Screenshot della scheda Directories della console Akamai EAA per Azure AD SSO. La directory superdemo.live è elencata in Currently assigned directories.](./media/header-akamai-tutorial/assigndirectory.png)

    > [!NOTE]
    > Il nome utente deve essere specificato in un formato basato sul **nome di intercettazione identità**. Nella figura 1 tale nome è **corpapps.login.go.akamai-access.com**

1. Il nome di accesso utente sarà quindi:`HTTP/corpapps.login.go.akamai-access.com`

    ![Screenshot che mostra Proprietà di EAADelegation con l'opzione Nome impostata su "EAADelegation" e l'opzione Nome di accesso utente impostata su HTTP/corpapps.login.go.akamai-access.com.](./media/header-akamai-tutorial/eaadelegation.png)

#### <a name="step-2-configure-the-spn-for-this-account"></a>Passaggio 2: Configurare il nome dell'entità servizio per questo account

1. In base a questo esempio, il nome dell'entità servizio sarà come indicato di seguito.

2. setspn -s **Http/corpapps.login.go.akamai-access.com eaadelegation**

    ![Screenshot di un prompt dei comandi amministratore che mostra i risultati dell'esecuzione del comando setspn -s Http/corpapps.login.go.akamai-access.com eaadelegation.](./media/header-akamai-tutorial/spn.png)

#### <a name="step-3-configure-delegation"></a>Passaggio 3: Configurare la delega

1. Per l'account EAADelegation fare clic sulla scheda Delegation (Delega).

    ![Screenshot di un prompt dei comandi amministratore che mostra il comando per configurare il nome dell'entità servizio.](./media/header-akamai-tutorial/spn.png)

    * Specificare l'opzione per usare qualsiasi protocollo di autenticazione
    * Fare clic su Add (Aggiungi) e aggiungere l'account del pool di app per il sito Web Kerberos. Se opportunamente configurato, il nome dell'entità servizio corretto dovrebbe essere risolto automaticamente.

#### <a name="step-4-create-a-keytab-file-for-akamai-eaa"></a>Passaggio 4: Creare un file keytab per AKAMAI EAA

1. Ecco la sintassi generica.

1. ktpass /out ActiveDirectorydomain.keytab  /princ `HTTP/yourloginportalurl@ADDomain.com`  /mapuser serviceaccount@ADdomain.com /pass +rdnPass  /crypto All /ptype KRB5_NT_PRINCIPAL

1. Spiegazione dell'esempio

    | Frammento di codice | Spiegazione |
    | - | - |
    | Ktpass /out EAADemo.keytab | // Nome del file keytab di output |
    | /princ HTTP/corpapps.login.go.akamai-access.com@superdemo.live | // HTTP/yourIDPName@YourdomainName |
    | /mapuser eaadelegation@superdemo.live | // Account di delega EAA |
    | /pass RANDOMPASS | // Password dell'account di delega EAA |
    | /crypto All ptype KRB5_NT_PRINCIPAL | // vedere la documentazione di Akamai EAA |
    | | |

1. Ktpass /out EAADemo.keytab  /princ HTTP/corpapps.login.go.akamai-access.com@superdemo.live /mapuser eaadelegation@superdemo.live /pass RANDOMPASS /crypto All ptype KRB5_NT_PRINCIPAL

    ![Screenshot di un prompt dei comandi amministratore che mostra i risultati dell'esecuzione del comando per creare un file keytab per AKAMAI EAA.](./media/header-akamai-tutorial/administrator.png)

#### <a name="step-5-import-keytab-in-the-akamai-eaa-console"></a>Passaggio 5: Importare il file keytab nella console di AKAMAI EAA

1. Fare clic su **System** > **Keytabs** (Sistema > Keytab).

    ![Screenshot della console Akamai EAA che mostra la selezione di Keytabs nel menu System.](./media/header-akamai-tutorial/keytabs.png)

1. Per Keytab Type (Tipo di keytab) scegliere **Kerberos Delegation** (Delega Kerberos).

    ![Screenshot della schermata EAAKEYTAB della console Akamai EAA che mostra le impostazioni dei keytab. L'opzione Keytab Type è impostata su Kerberos Delegation.](./media/header-akamai-tutorial/keytabdelegation.png)

1. Verificare che il file keytab venga visualizzato come distribuito e verificato.

    ![Screenshot della schermata KEYTABS della console Akamai EAA in cui il keytab EAA è indicato come "Keytab deployed and verified".](./media/header-akamai-tutorial/keytabs02.png)

1. Esperienza dell'utente

    ![Screenshot della finestra di dialogo di accesso in myapps.microsoft.com. ](./media/header-akamai-tutorial/enduser03.png)

    ![Screenshot della finestra Apps per myapps.microsoft.com che mostra le icone delle app.](./media/header-akamai-tutorial/enduser04.png)

1. Accesso condizionale

    ![Screenshot che mostra un messaggio di approvazione della richiesta di accesso.](./media/header-akamai-tutorial/conditionalaccess04.png)

    ![Screenshot della schermata Applications che mostra le icone per MyHeaderApp, SSH Secure, SecretRDPApp e myKerberosApp.](./media/header-akamai-tutorial/conditionalaccess10.png)

    ![Screenshot della schermata iniziale di myKerberosApp. Il messaggio di benvenuto di superdemo\user1 è visualizzato su un'immagine di sfondo.](./media/header-akamai-tutorial/conditionalaccess11.png)

### <a name="create-akamai-test-user"></a>Creare l'utente di test di Akamai

In questa sezione viene creato un utente di nome B.Simon in Akamai. Collaborare con il [team di supporto clienti di Akamai](https://www.akamai.com/us/en/contact-us/) per aggiungere gli utenti alla piattaforma Akamai. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On. 

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Akamai nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Akamai per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](./tutorial-list.md)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)

- [Provare Akamai con Azure AD](https://aad.portal.azure.com/)