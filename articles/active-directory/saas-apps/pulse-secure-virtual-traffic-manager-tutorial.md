---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Pulse Secure Virtual Traffic Manager | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Pulse Secure Virtual Traffic Manager.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/11/2020
ms.author: jeedes
ms.openlocfilehash: d487295e9aab1a56553dc9d31b0a8714688005c3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91338284"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pulse-secure-virtual-traffic-manager"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Pulse Secure Virtual Traffic Manager

Questa esercitazione descrive come integrare Pulse Secure Virtual Traffic Manager con Azure Active Directory (Azure AD). Integrando Pulse Secure Virtual Traffic Manager con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Pulse Secure Virtual Traffic Manager.
* Abilitare gli utenti per l'accesso automatico a Pulse Secure Virtual Traffic Manager con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Pulse Secure Virtual Traffic Manager abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Pulse Secure Virtual Traffic Manager supporta l'accesso SSO avviato da **SP**

## <a name="adding-pulse-secure-virtual-traffic-manager-from-the-gallery"></a>Aggiunta di Pulse Secure Virtual Traffic Manager dalla raccolta

Per configurare l'integrazione di Pulse Secure Virtual Traffic Manager in Azure AD, è necessario aggiungere Pulse Secure Virtual Traffic Manager dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Pulse Secure Virtual Traffic Manager** nella casella di ricerca.
1. Selezionare **Pulse Secure Virtual Traffic Manager** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-sso-for-pulse-secure-virtual-traffic-manager"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Pulse Secure Virtual Traffic Manager

Configurare e testare l'accesso SSO di Azure AD con Pulse Secure Virtual Traffic Manager usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Pulse Secure Virtual Traffic Manager.

Per configurare e testare l'accesso SSO di Azure AD con Pulse Secure Virtual Traffic Manager, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Pulse Secure Virtual Traffic Manager](#configure-pulse-secure-virtual-traffic-manager-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Pulse Secure Virtual Traffic Manager](#create-pulse-secure-virtual-traffic-manager-test-user)** : per avere una controparte di B.Simon in Pulse Secure Virtual Traffic Manager collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Pulse Secure Virtual Traffic Manager** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<published virtual server FQDN>/saml/consume`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `https://<published virtual server FQDN>/saml/metadata`

    c. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<published virtual server FQDN>/saml/consume`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Pulse Secure Virtual Traffic Manager](mailto:support@pulsesecure.net). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura Pulse Secure Virtual Traffic Manager** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Pulse Secure Virtual Traffic Manager.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Pulse Secure Virtual Traffic Manager**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-pulse-secure-virtual-traffic-manager-sso"></a>Configurare l'accesso Single Sign-On di Pulse Secure Virtual Traffic Manager

Questa sezione illustra le operazioni di configurazione necessarie per abilitare l'autenticazione SAML di Azure AD in Pulse Virtual Traffic Manager. Tutte le modifiche alla configurazione vengono apportate nell'interfaccia utente Web di amministrazione di Pulse Virtual Traffic Manager. 

#### <a name="create-a-saml-trusted-identity-provider"></a>Creare un provider di identità attendibile SAML

a. Passare alla pagina **Pulse Virtual Traffic Manager Appliance Admin UI > Catalog > SAML > Trusted Identity Providers Catalog** (Interfaccia utente di amministrazione appliance di Pulse Virtual Traffic Manager > Catalogo > SAML > Catalogo provider di identità attendibili) e fare clic su **Edit** (Modifica).

![Pagina dei cataloghi SAML](./media/pulse-secure-virtual-traffic-manager-tutorial/saml-catalogs.png)

b. Aggiungere i dettagli per il nuovo provider di identità attendibile SAML, copiando le informazioni dall'applicazione Azure AD Enterprise nella pagina delle impostazioni dell'accesso Single Sign-On e quindi fare clic su **Create New Trusted Identity Provider** (Crea nuovo provider di identità attendibile).

![Opzione Create New Trusted Identity Provider](./media/pulse-secure-virtual-traffic-manager-tutorial/create-trusted-identity-provider.png)

* Nella casella di testo **Name** (Nome) immettere un nome per il provider di identità attendibile. 

* Nella casella di testo **Entity_id** (ID entità) immettere il valore di **Identificatore Azure AD** copiato dal portale di Azure.  

* Nella casella di testo **Url** immettere il valore di **URL di accesso** copiato dal portale di Azure. 

* Aprire nel Blocco note il **certificato** scaricato dal portale di Azure e incollarne il contenuto nella casella di testo **Certificate** (Certificato).

c. Verificare che il nuovo provider di identità SAML sia stato creato correttamente. 

![Verifica della creazione del provider di identità attendibile](./media/pulse-secure-virtual-traffic-manager-tutorial/verify-saml-identity-provider.png)

#### <a name="configure-the-virtual-server-to-use-azure-ad-authentication"></a>Configurare il server virtuale per l'uso di Autenticazione di Azure AD

a. Passare alla pagina **Pulse Virtual Traffic Manager Appliance Admin UI > Services > Virtual Servers** (Interfaccia utente di amministrazione appliance di Pulse Virtual Traffic Manager > Servizi > Server virtuali) e fare clic su **Edit** (Modifica) accanto al server virtuale creato in precedenza.

![Modifica del server virtuale](./media/pulse-secure-virtual-traffic-manager-tutorial/virtual-servers.png)

b. Nella sezione **Authentication** (Autenticazione) fare clic su **Edit** (Modifica). 

![Sezione Authentication](./media/pulse-secure-virtual-traffic-manager-tutorial/authentication.png)

c. Configurare le impostazioni di autenticazione seguenti per il server virtuale: 

1. Authentication (Autenticazione)

    ![Impostazioni di autenticazione per il server virtuale](./media/pulse-secure-virtual-traffic-manager-tutorial/authentication-1.png)

    a. Nella casella **auth!type** selezionare **SAML Service Provider** (Provider di servizi SAML) 

    b. Impostare **auth!verbose** su "Yes" (Sì) per risolvere eventuali problemi di autenticazione. In caso contrario, non modificare l'impostazione predefinita "No" 

2. Authentication Session Management (Gestione sessione di autenticazione)

    ![Schermata Authentication Session Management](./media/pulse-secure-virtual-traffic-manager-tutorial/authentication-session.png)

    a. In **auth!session!cookie_name** non modificare l'impostazione predefinita "VS_SamlSP_Auth" 

    b. In **auth!session!timeout** non modificare l'impostazione predefinita "7200" 

    c. Impostare **auth!session!log_external_state** su "Yes" (Sì) per risolvere eventuali problemi di autenticazione. In caso contrario, non modificare l'impostazione predefinita "No" 

    d. Impostare **auth!session!cookie_attributes** su "HTTPOnly" 

3. SAML Service Provider (Provider di servizi SAML)

    ![Schermata SAML Service Provider](./media/pulse-secure-virtual-traffic-manager-tutorial/saml-service-provider.png)

    a. Nella casella di testo **auth!saml!sp_entity_id** specificare lo stesso URL usato come identificatore per la configurazione dell'accesso Single Sign-On di Azure AD (ID entità), ad esempio `https://pulseweb.labb.info/saml/metadata`. 

    b. Nella casella di testo **auth!saml!sp_acs_url** specificare lo stesso URL usato come URL di risposta per la configurazione dell'accesso Single Sign-On di Azure AD (URL del servizio consumer di asserzione), ad esempio `https://pulseweb.labb.info/saml/consume`. 

    c. In **auth!saml!idp** selezionare il **provider di identità attendibile** creato nel passaggio precedente. 

    d. In auth!saml!time_tolerance non modificare l'impostazione predefinita pari a "5" secondi. 

    e. In auth!saml!nameid_format selezionare **unspecified** (non specificato).

    f. Per applicare le modifiche, fare clic su **Update** (Aggiorna) nella parte inferiore della pagina.
    
### <a name="create-pulse-secure-virtual-traffic-manager-test-user"></a>Creare l'utente di test di Pulse Secure Virtual Traffic Manager

In questa sezione viene creato un utente di nome Britta Simon in Pulse Secure Virtual Traffic Manager. Collaborare con il [team di supporto di Pulse Secure Virtual Traffic Manager](mailto:support@pulsesecure.net) per aggiungere gli utenti alla piattaforma Pulse Secure Virtual Traffic Manager. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

1. Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di Pulse Secure Virtual Traffic Manager, in cui è possibile avviare il flusso di accesso. 

2. Passare direttamente all'URL di accesso di Pulse Secure Virtual Traffic Manager e avviare il flusso di accesso da questa posizione.

3. È possibile usare il Pannello di accesso Microsoft. Quando si fa clic sul riquadro di Pulse Secure Virtual Traffic Manager nel pannello di accesso, si verrà reindirizzati all'URL di accesso di Pulse Secure Virtual Traffic Manager. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Pulse Secure Virtual Traffic Manager, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).