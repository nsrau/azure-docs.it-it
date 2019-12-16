---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Akamai | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Akamai.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1b7e0d7a-e78f-43a5-af93-b626186e2376
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 042dd242285081001ca48c9f17e4d42c2294c0ff
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74979137"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-akamai"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Akamai

Questa esercitazione descrive come integrare Akamai con Azure Active Directory (Azure AD). Integrando Akamai con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere ad Akamai.
* Abilitare gli utenti per l'accesso automatico ad Akamai con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Akamai abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

- Slack supporta l'accesso SSO avviato da IDP

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
    * **[Creare l'utente di test di Akamai](#create-akamai-test-user)** : per avere una controparte di B.Simon in Akamai collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Akamai** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<Yourapp>.login.go.akamai-access.com/sp/response`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https:// <Yourapp>.login.go.akamai-access.com/sp/response`

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

1. Accedere alla console di **Akamai Enterprise Application Access** (Accesso applicazioni aziendali Akamai).
1. Nella console di **Akamai EAA** selezionare **Identity** (Identità)  > **Identity Providers** (Provider di identità).

    ![Configurazione di Akamai](./media/header-akamai-tutorial/configure01.png)

1. Fare clic su **Add Identity Provider** (Aggiungi provider di identità).

    ![Configurazione di Akamai](./media/header-akamai-tutorial/configure02.png)

    a. Specificare un valore in **Unique Name** (Nome univoco).
    
    b. Scegliere **Third Party SAML** (SAML di terze parti) e fare clic su **Create Identity Provider and Configure** (Crea provider di identità e configura).

### <a name="general-settings"></a>Impostazioni generali

1. **Identity Intercept** (Intercettazione identità): specificare il nome dell'URL di base del provider di servizi, che verrà usato per la configurazione di Azure AD.

    > [!NOTE]
    > È possibile scegliere di avere un dominio personalizzato, ma in tal caso saranno necessari una voce DNS e un certificato. In questo esempio verrà usato il dominio Akamai.

1. **Akamai Cloud Zone** (Zona cloud Akamai): selezionare la zona cloud appropriata.
1. **Certificate Validation** (Convalida del certificato): vedere la documentazione di Akamai (facoltativo).

    ![Configurazione di Akamai](./media/header-akamai-tutorial/configure03.png)

### <a name="authentication-configuration"></a>Configurazione dell'autenticazione

1. URL: specificare un URL identico a quello dell'intercettazione dell'identità, ovvero la posizione a cui gli utenti vengono reindirizzati dopo l'autenticazione.
2. Logout URL (URL di disconnessione): aggiornare l'URL di disconnessione.
3. Sign SAML Request (Firma richiesta SAML): opzione deselezionata per impostazione predefinita.
4. Per il file di metadati IDP, aggiungere l'applicazione nella console di Azure AD.

    ![Configurazione di Akamai](./media/header-akamai-tutorial/configure04.png)

### <a name="header-based-authentication"></a>Autenticazione basata su intestazione

Autenticazione basata su intestazione di Akamai

1. Scegliere **Custom HTTP** (HTTP personalizzato) in Add Applications Wizard (Aggiunta guidata applicazioni).

    ![Configurazione di Akamai](./media/header-akamai-tutorial/configure05.png)

    ![Configurazione di Akamai](./media/header-akamai-tutorial/configure06.png)

    ![Configurazione di Akamai](./media/header-akamai-tutorial/configure07.png)

    ![Configurazione di Akamai](./media/header-akamai-tutorial/configure08.png)

#### <a name="authentication"></a>Authentication

![Configurazione di Akamai](./media/header-akamai-tutorial/configure09.png)

![Configurazione di Akamai](./media/header-akamai-tutorial/configure10.png)

#### <a name="services"></a>Servizi

1. Fare clic su Save and Go to Authentication (Salva e passa ad Autenticazione).

![Configurazione di Akamai](./media/header-akamai-tutorial/configure11.png)

#### <a name="advanced-settings"></a>Impostazioni avanzate

1. In **Customer HTTP Headers** (Intestazioni HTTP cliente) specificare un valore per **CustomerHeader** (Intestazione cliente) e **SAML Attribute** (Attributo SAML).

    ![Configurazione di Akamai](./media/header-akamai-tutorial/configure12.png)

1. Fare clic sul pulsante **Save and go to Deployment** (Salva e passa a Distribuzione).

    ![Configurazione di Akamai](./media/header-akamai-tutorial/configure13.png)

#### <a name="deploy-the-application"></a>Distribuire l'applicazione

1. Fare clic sul pulsante **Deploy Application** (Distribuisci applicazione).

    ![Configurazione di Akamai](./media/header-akamai-tutorial/configure14.png)

1. Verificare che l'applicazione sia stata distribuita correttamente.

    ![Configurazione di Akamai](./media/header-akamai-tutorial/configure15.png)

### <a name="kerberos-authentication"></a>Autenticazione Kerberos

#### <a name="remote-desktop"></a>Desktop remoto

1. Scegliere **RDP** in Add Applications Wizard (Aggiunta guidata applicazioni).

    ![Configurazione di Akamai](./media/header-akamai-tutorial/configure16.png)

    ![Configurazione di Akamai](./media/header-akamai-tutorial/configure17.png)

    ![Configurazione di Akamai](./media/header-akamai-tutorial/configure18.png)

1. Specificare il connettore che verrà usato per questo servizio.

    ![Configurazione di Akamai](./media/header-akamai-tutorial/configure19.png)

#### <a name="authentication"></a>Authentication

Fare clic su **Save and go to Services** (Salva e passa a Servizi).

![Configurazione di Akamai](./media/header-akamai-tutorial/configure20.png)

#### <a name="services"></a>Servizi

Fare clic su **Save and go to Advanced Settings** (Salva e passa a Impostazioni avanzate).

![Configurazione di Akamai](./media/header-akamai-tutorial/configure21.png)

#### <a name="advanced-settings"></a>Impostazioni avanzate

Fare clic su **Save and go to Deployment** (Salva e passa a Distribuzione).

![Configurazione di Akamai](./media/header-akamai-tutorial/configure22.png)

![Configurazione di Akamai](./media/header-akamai-tutorial/configure23.png)

![Configurazione di Akamai](./media/header-akamai-tutorial/configure24.png)

### <a name="deployment"></a>Distribuzione

#### <a name="ssh"></a>SSH

1. Passare ad Add Applications (Aggiungi applicazioni) e scegliere **SSH**.

    ![Configurazione di Akamai](./media/header-akamai-tutorial/configure25.png)

    ![Configurazione di Akamai](./media/header-akamai-tutorial/configure26.png)

1. Configurare l'identità dell'applicazione.

    ![Configurazione di Akamai](./media/header-akamai-tutorial/configure27.png)

    a. Specificare nome/descrizione.

    b. Specificare IP/FQDN e porta del server applicazioni per SSH.

    c. Specificare nome utente/passphrase per SSH. *Controllare la sezione Akamai EAA.

    d. Specificare il nome host esterno.

    e. Specificare il percorso del connettore e scegliere il connettore.

#### <a name="authentication"></a>Authentication

Fare clic su **Save and go to Services** (Salva e passa a Servizi).

![Configurazione di Akamai](./media/header-akamai-tutorial/configure28.png)

#### <a name="services"></a>Servizi

Fare clic su **Save and go to Advanced Settings** (Salva e passa a Impostazioni avanzate).

![Configurazione di Akamai](./media/header-akamai-tutorial/configure29.png)

#### <a name="advanced-settings"></a>Impostazioni avanzate

Fare clic su Save and go to Deployment (Salva e passa a Distribuzione).

![Configurazione di Akamai](./media/header-akamai-tutorial/configure30.png)

![Configurazione di Akamai](./media/header-akamai-tutorial/configure31.png)

#### <a name="deployment"></a>Distribuzione

Fare clic su **Deploy Application** (Distribuisci applicazione).

![Configurazione di Akamai](./media/header-akamai-tutorial/configure32.png)

### <a name="kerberos-applications"></a>Applicazioni Kerberos

#### <a name="adding-directory"></a>Aggiunta della directory

![Configurazione di Akamai](./media/header-akamai-tutorial/configure33.png)

![Configurazione di Akamai](./media/header-akamai-tutorial/configure34.png)

![Configurazione di Akamai](./media/header-akamai-tutorial/configure35.png)

![Configurazione di Akamai](./media/header-akamai-tutorial/configure36.png)

### <a name="create-akamai-test-user"></a>Creare l'utente di test di Akamai

In questa sezione viene creato un utente di nome B.Simon in Akamai. Collaborare con il  [team di supporto clienti di Akamai](https://www.akamai.com/us/en/contact-us/) per aggiungere gli utenti alla piattaforma Akamai. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On. 

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Akamai nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Akamai per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare Akamai con Azure AD](https://aad.portal.azure.com/)
