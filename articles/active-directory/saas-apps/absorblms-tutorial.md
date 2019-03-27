---
title: 'Esercitazione: Integrazione di Azure Active Directory con Absorb LMS | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Absorb LMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: ba9f1b3d-a4a0-4ff7-b0e7-428e0ed92142
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e86ae61a6aec6c546b36c52f3f3875cbebdc838e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57861928"
---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>Esercitazione: Integrazione di Azure Active Directory con Absorb LMS

Questa esercitazione descrive come integrare Absorb LMS con Azure Active Directory (Azure AD).
L'integrazione di Absorb LMS con Azure AD offre i vantaggi seguenti:

* In Azure AD è possibile controllare chi può accedere ad Absorb LMS.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) ad Absorb LMS con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Absorb LMS, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Absorb LMS abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Absorb LMS supporta l'accesso SSO avviato da **IdP**

## <a name="adding-absorb-lms-from-the-gallery"></a>Aggiunta di Absorb LMS dalla raccolta

Per configurare l'integrazione di Absorb LMS in Azure AD, è necessario aggiungere Absorb LMS dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Absorb LMS dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Absorb LMS**, selezionare **Absorb LMS** dal pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Absorb LMS nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Absorb LMS usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Absorb LMS.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Absorb LMS, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Absorb LMS](#configure-absorb-lms-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Absorb LMS](#create-absorb-lms-test-user)**: per avere una controparte di Britta Simon in Absorb LMS collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Absorb LMS, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Absorb LMS** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Absorb LMS](common/idp-intiated.png)

    Se si usa **Absorb 5 - UI**, usare la configurazione seguente:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://company.myabsorb.com/account/saml`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://company.myabsorb.com/account/saml`

    Se si usa **Absorb 5 - New Learner Experience**, usare la configurazione seguente:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://company.myabsorb.com/api/rest/v2/authentication/saml`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://company.myabsorb.com/api/rest/v2/authentication/saml`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di risposta effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Absorb LMS](https://support.absorblms.com/hc/). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **file XML dei metadati** definito dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/metadataxml.png)

6. Nella sezione **Configura Absorb LMS** copiare gli URL appropriati in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-absorb-lms-single-sign-on"></a>Configurare l'accesso Single Sign-On di Absorb LMS

1. In una nuova finestra del Web browser accedere al sito aziendale di Absorb LMS come amministratore.

2. Selezionare il pulsante **Account** in alto a destra.

    ![Pulsante Account](./media/absorblms-tutorial/1.png)

3. Nel riquadro Account selezionare **Portal Settings** (Impostazioni del portale).

    ![Collegamento Impostazioni del portale](./media/absorblms-tutorial/2.png)

4. Selezionare la scheda **Manage SSO Settings** (Gestisci impostazioni SSO).

    ![La scheda Utenti](./media/absorblms-tutorial/managesso.png)

5. Nella pagina **Manage Single Sign-On Settings** (Gestisci impostazioni Single Sign-On) eseguire le operazioni seguenti:

    ![Pagina di configurazione Single Sign-on](./media/absorblms-tutorial/settings.png)

    a. Nela casella di testo **Name** (Nome) immettere un nome, come Azure AD Marketplace SSO.

    b. In **Method** (Metodo) selezionare **SAML**.

    c. Nel blocco note, aprire il certificato scaricato dal portale di Azure. Rimuovere i tag **-----BEGIN CERTIFICATE-----** ed **-----END CERTIFICATE-----**. Quindi, nella casella **Key** (Chiave) incollare il contenuto rimanente.

    d. Nella casella **Mode** (Modalità) selezionare **Identity Provider Initiated** (Avviato dal provider di identità).

    e. Nella casella **Id Property** (ID proprietà) selezionare l'attributo configurato come identificatore dell'utente in Azure AD. Ad esempio, se in Azure AD è stato selezionato *userPrincipalName*, selezionare **Username**.

    f. In **Signature Type** (Tipo di firma) selezionare **Sha256**.

    g. Nella casella **Login URL** (URL di accesso) incollare l'**URL di accesso utente** dalla pagina **Proprietà** dell'applicazione del portale di Azure.

    h. In **Logout URL** (URL disconnessione) incollare il valore dell'**URL di disconnessione** copiato dalla finestra **Configura accesso** del portale di Azure.

    i. Spostare l'interruttore **Automatically Redirect** (Reindirizzamento automatico) sulla posizione **attiva**.

6. Selezionare **Salva**.

    ![Abilitare Only Allow SSO Login](./media/absorblms-tutorial/save.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon\@dominioaziendale.estensione**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Absorb LMS.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Absorb LMS**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni digitare e selezionare **Absorb LMS**.

    ![Collegamento di Absorb LMS nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-absorb-lms-test-user"></a>Creare un utente di test di Absorb LMS

Affinché gli utenti di Azure AD possano accedere ad Absorb LMS devono essere configurati in Absorb LMS. In Absorb LMS il provisioning è un'attività manuale.

**Per configurare il provisioning utenti, seguire questa procedura:**

1. Accedere al sito aziendale di Absorb LMS come amministratore.

2. Nel riquadro **Users** (Utenti) selezionare **Users** (Utenti).

    ![Collegamento Utenti](./media/absorblms-tutorial/absorblms_userssub.png)

3. Selezionare la scheda **User** (Utente).

    ![Elenco a discesa Aggiungi nuovo](./media/absorblms-tutorial/absorblms_createuser.png)

4. Nella pagina **Add User** (Aggiungi utente) eseguire la procedura seguente:

    ![Pagina Aggiungi utente](./media/absorblms-tutorial/user.png)

    a. Nella casella di testo **First Name** (Nome) digitare il nome, ad esempio **Britta**.

    b. Nella casella di testo **Last Name** (Cognome) digitare il cognome, ad esempio **Simon**.

    c. Nella casella di testo **Username** (Nome utente) digitare il nome completo, ad esempio **Britta Simon**.

    d. Nella casella **Password** digitare la password dell'utente.

    e. Nella casella **Confirm Password** (Conferma la password) ridigitare la password.

    f. Impostare il pulsante **Is Active** (È attivo) su **Active** (Attivo).

5. Selezionare **Salva**.

    ![Abilitare Only Allow SSO Login](./media/absorblms-tutorial/save.png)

    > [!NOTE]
    > Per impostazione predefinita, il provisioning utenti non è abilitato nel Single Sign-On. Gli utenti che vogliono abilitare questa funzionalità devono configurarla nel modo indicato in [questa](https://support.absorblms.com/hc/en-us/articles/360014083294-Incoming-SAML-2-0-SSO-Account-Provisioning) documentazione. Si noti anche che il provisioning utenti è disponibile solo in **Absorb 5 - New Learner Experience** con l'URL di Controllo di accesso `https://company.myabsorb.com/api/rest/v2/authentication/saml`

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Absorb LMS nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Absorb LMS per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
