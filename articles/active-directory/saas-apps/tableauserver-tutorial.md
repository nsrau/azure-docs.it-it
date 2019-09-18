---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Tableau Server | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Tableau Server.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e95143da96ce9891c3820479e536e9ea6a18617
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70861353"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-server"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Tableau Server

Questa esercitazione descrive come integrare Tableau Server con Azure Active Directory (Azure AD). Integrando Tableau Server con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Tableau Server.
* Abilitare gli utenti per l'accesso automatico a Tableau Server con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Tableau Server abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Tableau Server supporta l'accesso SSO avviato da **SP**

## <a name="adding-tableau-server-from-the-gallery"></a>Aggiunta di Tableau Server dalla raccolta

Per configurare l'integrazione di Tableau Server in Azure AD, è necessario aggiungere Tableau Server dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Tableau Server** nella casella di ricerca.
1. Selezionare **Tableau Server** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-tableau-server"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Tableau Server

Configurare e testare l'accesso SSO di Azure AD con Tableau Server usando un utente di test di nome **B.Simon**. Per il corretto funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Tableau Server.

Per configurare e testare l'accesso SSO di Azure AD con Tableau Server, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso SSO di Tableau Server](#configure-tableau-server-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Tableau Server](#create-tableau-server-test-user)** : per avere una controparte di B.Simon in Tableau Server collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Tableau Server** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://azure.<domain name>.link`

    b. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://azure.<domain name>.link`

    c. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://azure.<domain name>.link/wg/saml/SSO/index.html`

    > [!NOTE]
    > I valori precedenti non sono valori reali. Aggiornare i valori con l'URL e l'identificatore effettivi indicati nella pagina di configurazione di Tableau Server, come illustrato più avanti in questa esercitazione.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il file **XML dei metadati della federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura Tableau Server** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Tableau Server.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Tableau Server**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-tableau-server-sso"></a>Configurare l'accesso SSO di Tableau Server

1. Per configurare l'accesso SSO per l'applicazione, è necessario accedere al tenant di Tableau Server come amministratore.

2. Nella scheda **CONFIGURATION** (Configurazione) selezionare **User Identity & Access**(Identità utente e accesso), quindi selezionare la scheda **Authentication Method** (Metodo di autenticazione).

    ![Configure Single Sign-On](./media/tableauserver-tutorial/tutorial-tableauserver-auth.png)

3. Nella pagina **CONFIGURATION** (Configurazione) seguire questa procedura:

    ![Configure Single Sign-On](./media/tableauserver-tutorial/tutorial-tableauserver-config.png)

    a. Per **Authentication Method** (Metodo di autenticazione) selezionare SAML.

    b. Selezionare la casella di controllo **Enable SAML Authentication for the server** (Abilita autenticazione SAML per il server).

    c. Tableau Server return URL (URL restituito di Tableau Server): l'URL a cui accedono gli utenti di Tableau Server, ad esempio <http://tableau_server>. L'uso di `http://localhost` non è consigliato. L'uso di un URL con una barra finale, ad esempio `http://tableau_server/`, non è supportato. Copiare il valore di **Tableau Server return URL** (URL restituito di Tableau Server) e incollarlo nella casella di testo **URL di accesso** nella sezione **Configurazione SAML di base** del portale di Azure

    d. SAML entity ID (ID entità SAML): l'ID entità identifica in modo univoco l'installazione di Tableau Server nel provider di identità. Se si desidera, qui è possibile immettere di nuovo l'URL di Tableau Server, ma non deve essere l'URL di Tableau Server in uso. Copiare il valore di **SAML entity ID** (ID entità SAML) e incollarlo nella casella di testo **Identificatore** nella sezione **Configurazione SAML di base** del portale di Azure.

    e. Fare clic su **Download XML Metadata File** (Scarica file di metadati XML) e aprire il file nell'applicazione editor di testo. Cercare l'URL del servizio consumer di asserzione con Http Post e Indice 0 e copiarlo. Ora incollarlo nella casella di testo **URL di risposta** nella sezione **Configurazione SAML di base** del portale di Azure.

    f. Individuare il file di metadati della federazione scaricato dal portale di Azure e quindi caricarlo in **SAML Idp metadata file**(File metadati IdP SAML).

    g. Immettere i nomi per gli attributi usati dal provider di identità per contenere nomi utente, nomi visualizzati e indirizzi di posta elettronica.

    h. Fare clic su **Save** (Salva).

    > [!NOTE]
    > Il cliente deve caricare eventuali certificati nella configurazione SSO SAML di Tableau Server e il passaggio verrà ignorato nel flusso SSO. Per assistenza nella configurazione di SAML in Tableau Server, vedere questo articolo: [Configure SAML](https://help.tableau.com/current/server/en-gb/saml_config_steps_tsm_ui.htm) (Configurazione di SAML).

### <a name="create-tableau-server-test-user"></a>Creare l'utente di test di Tableau Server

Questa sezione descrive come creare un utente di nome B.Simon in Tableau Server. In Tableau Server è necessario eseguire il provisioning di tutti gli utenti.

Il valore username dell'utente deve corrispondere al valore configurato nell'attributo personalizzato **username**in Azure AD. Con un mapping corretto, l'integrazione per la Configurazione dell'accesso Single Sign-On di Azure AD dovrebbe funzionare.

> [!NOTE]
> Se si deve creare manualmente un utente, è necessario contattare l'amministratore di Tableau Server dell'organizzazione.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Tableau Server nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Tableau Server per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare Tableau Server con Azure AD](https://aad.portal.azure.com/)