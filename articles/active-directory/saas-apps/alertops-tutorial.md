---
title: 'Esercitazione: Integrazione di Azure Active Directory con AlertOps | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e AlertOps.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 3db13ed4-35c2-4b1e-bed8-9b5977061f93
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 124f69b3fd6944d0a6e3814f1bbfa5594bcd95cb
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65988379"
---
# <a name="tutorial-integrate-alertops-with-azure-active-directory"></a>Esercitazione: Integrare AlertOps con Azure Active Directory

Questa esercitazione descrive come integrare AlertOps con Azure Active Directory (Azure AD). Integrando AlertOps con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere ad AlertOps.
* Abilitare gli utenti per l'accesso automatico ad AlertOps con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di AlertOps abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. AlertOps supporta l'accesso SSO avviato da **SP e IDP**.

## <a name="adding-alertops-from-the-gallery"></a>Aggiunta di AlertOps dalla raccolta

Per configurare l'integrazione di AlertOps in Azure AD, è necessario aggiungere AlertOps dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **AlertOps** nella casella di ricerca.
1. Selezionare **AlertOps** nel riquadro dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

Configurare e testare l'accesso Single Sign-On di Azure AD con AlertOps usando un utente di test di nome **Britta Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in AlertOps.

Per configurare e testare l'accesso SSO di Azure AD con AlertOps, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** per consentire agli utenti di usare questa funzionalità.
2. **[Configurare AlertOps](#configure-alertops)** per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente test di Azure AD](#create-an-azure-ad-test-user)** per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)** per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di AlertOps](#create-alertops-test-user)** : per avere una controparte di Britta Simon in AlertOps collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-sso)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **AlertOps** del [portale di Azure](https://portal.azure.com/) trovare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    1. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<SUBDOMAIN>.alertops.com`

    1. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<SUBDOMAIN>.alertops.com/login.aspx`

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<SUBDOMAIN>.alertops.com/login.aspx`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di AlertOps](mailto:support@alertops.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML**, trovare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer in uso.

   ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Set up AlertOps** (Configura AlertOps) copiare gli URL appropriati in base alle proprie esigenze.

   ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="configure-alertops"></a>Configurare AlertOps

1. Per automatizzare la configurazione all'interno di AlertOps, è necessario installare l'**estensione del browser per l'accesso sicuro alle app personali** facendo clic su **Installa estensione**.

    ![Estensione MyApps](common/install-myappssecure-extension.png)

2. Dopo aver aggiunto l'estensione al browser, fare clic su **Setup AlertOps** (Configura AlertOps) per passare direttamente all'applicazione AlertOps. Da qui, fornire le credenziali di amministratore per accedere ad AlertOps. L'estensione del browser configurerà automaticamente l'applicazione e automatizzerà i passaggi da 3 a 5.

    ![Eseguire la configurazione](common/setup-sso.png)

3. Se si vuole configurare manualmente AlertOps, aprire una nuova finestra del Web browser, accedere al sito aziendale di AlertOps come amministratore e seguire questa procedura:

4. Fare clic su **Account settings** (Impostazioni account) dal pannello di spostamento sinistro.

    ![Configurazione di AlertOps](./media/alertops-tutorial/configure1.png)

5. Nella pagina **Subscription Settings** (Impostazioni sottoscrizione) selezionare **SSO** e seguire questa procedura:

    ![Configurazione di AlertOps](./media/alertops-tutorial/configure2.png)

    a. Selezionare la casella di controllo **Use Single Sign-On(SSO)** (Usa Single Sign-On (SSO)).

    b. Nell'elenco a discesa selezionare **Azure Active Directory** come valore di **SSO Provider** (Provider SSO).

    c. Nella casella di testo **Issuer URL** (URL autorità di certificazione) usare il valore dell'identificatore usato nella sezione **Configurazione SAML di base** del portale di Azure.

    d. Nella casella di testo **SAML endpoint URL** (URL endpoint SAML) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    e. Nella casella di testo **SLO endpoint URL** (URL endpoint SLO) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    f. Nell'elenco a discesa selezionare **SHA256** come valore di **SAML Signature Algorithm** (Algoritmo di firma SAML).

    g. Aprire il file del certificato (Base64) scaricato nel blocco note. Copiare il contenuto negli Appunti e quindi incollarlo nella casella Certificato X.509.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

In questa sezione verrà creato un utente di test di nome Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `Britta Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `BrittaSimon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon verrà abilitata per l'uso dell'accesso Single Sign-On di Azure concedendo l'accesso ad AlertOps.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **AlertOps**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-alertops-test-user"></a>Creare l'utente di test di AlertOps

1. In un'altra finestra del browser accedere al sito aziendale di AlertOps come amministratore.

2. Fare clic su **Users** (Utenti) nel pannello di spostamento sinistro.

    ![Configurazione di AlertOps](./media/alertops-tutorial/user1.png)

3. Selezionare **Add User** (Aggiungi utente).

    ![Configurazione di AlertOps](./media/alertops-tutorial/user2.png)

4. Nella finestra di dialogo **Aggiungi utente** seguire questa procedura:

    ![Configurazione di AlertOps](./media/alertops-tutorial/user3.png)

    a. Nella casella di testo **Login User Name** (Nome utente di accesso) immettere il nome utente dell'utente, ad esempio **Brittasimon**.

    b. Nella casella di testo **Official Email** (Posta elettronica ufficiale) immettere l'indirizzo di posta elettronica dell'utente, ad esempio **Brittasimon\@contoso.com**.

    c. Nella casella di testo **First Name** (Nome) immettere il nome dell'utente, ad esempio **Britta**.

    d. Nella casella di testo **Last Name** (Cognome) immettere il cognome dell'utente, ad esempio **Simon**.

    e. Nell'elenco a discesa selezionare il valore di **Type** (Tipo) in base alle indicazioni dell'organizzazione.

    f. Nell'elenco a discesa selezionare il valore di **Role** (Ruolo) per l'utente in base alle indicazioni dell'organizzazione.

    g. Selezionare **Aggiungi**.

### <a name="test-sso"></a>Testare l'accesso SSO

Quando si seleziona il riquadro di AlertOps nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione AlertOps per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)