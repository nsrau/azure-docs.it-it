---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con KnowledgeOwl | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e KnowledgeOwl.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2ae30996-864d-4872-90bc-f770e1ea159a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc7d481b757a76ba65e0c78a93bde1bc58ace7cc
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791629"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-knowledgeowl"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con KnowledgeOwl

Questa esercitazione descrive come integrare KnowledgeOwl con Azure Active Directory (Azure AD). Integrando KnowledgeOwl con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a KnowledgeOwl.
* Abilitare gli utenti per l'accesso automatico a KnowledgeOwl con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di KnowledgeOwl abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* KnowledgeOwl supporta l'accesso SSO avviato da **SP e IDP**
* KnowledgeOwl supporta il provisioning utenti **Just-In-Time**

## <a name="adding-knowledgeowl-from-the-gallery"></a>Aggiunta di KnowledgeOwl dalla raccolta

Per configurare l'integrazione di KnowledgeOwl in Azure AD, è necessario aggiungere KnowledgeOwl dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **KnowledgeOwl** nella casella di ricerca.
1. Selezionare **KnowledgeOwl** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-knowledgeowl"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per KnowledgeOwl

Configurare e testare l'accesso SSO di Azure AD con KnowledgeOwl usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in KnowledgeOwl.

Per configurare e testare l'accesso SSO di Azure AD con KnowledgeOwl, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    * **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    * **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di KnowledgeOwl](#configure-knowledgeowl-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    * **[Creare l'utente di test di KnowledgeOwl](#create-knowledgeowl-test-user)** : per avere una controparte di B.Simon in KnowledgeOwl collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **KnowledgeOwl** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente:
    
    | | |
    |-|-|
    | `https://app.knowledgeowl.com/sp`|
    | `https://app.knowledgeowl.com/sp/id/<unique ID>`|

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente:
    
    | | |
    |-|-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente:
    
    | | |
    |-|-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli in seguito con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. La procedura è descritta più avanti nell'esercitazione.

1. L'applicazione KnowledgeOwl prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/default-attributes.png)

1. Oltre quelli elencati in precedenza, l'applicazione KnowledgeOwl prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.

    | Nome | Attributo di origine | Spazio dei nomi |
    | ------------ | -------------------- | -----|
    | ssoid | user.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (base)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificateraw.png)

1. Nella sezione **Configura KnowledgeOwl** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a KnowledgeOwl.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **KnowledgeOwl**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-knowledgeowl-sso"></a>Configurare l'accesso Single Sign-On di KnowledgeOwl

1. In un'altra finestra del Web browser accedere al sito aziendale di KnowledgeOwl come amministratore.

1. Fare clic su **Settings** (Impostazioni) e quindi selezionare **Security** (Sicurezza).

    ![Configurazione di KnowledgeOwl](./media/knowledgeowl-tutorial/configure1.png)

1. Scorrere verso il basso fino a **SAML SSO Integration** (Integrazione SSO SAML) e seguire questa procedura:

    ![Configurazione di KnowledgeOwl](./media/knowledgeowl-tutorial/configure2.png)

    a. Selezionare **Enable SAML SSO** (Abilita SSO SAML).

    b. Copiare il valore di **SP Entity ID** (ID entità SP) e incollarlo in **Identifier (Entity ID)** (Identificatore - ID entità) nella sezione **Configurazione SAML di base** del portale di Azure.

    c. Copiare il valore di **SP Login URL** (URL di accesso del provider di servizi) e incollarlo nelle caselle di testo **URL di accesso e URL di risposta** nella sezione **Configurazione SAML di base** del portale di Azure.

    d. Nella casella di testo **IdP entityID** (ID entità IdP) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    e. Nella casella di testo **IdP Login URL** (URL di accesso IdP) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    f. Nella casella di testo **IdP Logout URL** (URL di disconnessione IdP) incollare il valore di **URL disconnessione** copiato dal portale di Azure

    g. Caricare il certificato scaricato dal portale di Azure facendo clic su **Upload IdP Certificate** (Carica certificato IdP).

    h. Fare clic su **Map SAML Attributes** (Esegui il mapping degli attributi SAML) per eseguire il mapping degli attributi e seguire questa procedura:

    ![Configurazione di KnowledgeOwl](./media/knowledgeowl-tutorial/configure3.png)

    * Immettere `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ssoid` nella casella di testo **SSO ID** (ID SSO)
    * Immettere `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` nella casella di testo **Username/Email** (Nome utente/indirizzo di posta elettronica).
    * Immettere `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` nella casella di testo **First Name** (Nome).
    * Immettere `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` nella casella di testo **Last Name** (Cognome).
    * Fare clic su **Save** (Salva).

    i. Fare clic su **Save** nella parte inferiore della pagina.

    ![Configurazione di KnowledgeOwl](./media/knowledgeowl-tutorial/configure4.png)

### <a name="create-knowledgeowl-test-user"></a>Creare l'utente di test di KnowledgeOwl

In questa sezione viene creato un utente di nome B.Simon in KnowledgeOwl. KnowledgeOwl supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in KnowledgeOwl, ne viene creato uno nuovo dopo l'autenticazione.

> [!Note]
> Se è necessario creare un utente manualmente, contattare il [team di supporto di KnowledgeOwl](mailto:support@knowledgeowl.com).

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di KnowledgeOwl nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di KnowledgeOwl per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare KnowledgeOwl con Azure AD](https://aad.portal.azure.com/)