---
title: 'Esercitazione: Integrazione di Azure Active Directory con Origami | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Origami
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a28bb0ba-b564-46ba-accc-e587699295d4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 1a3e01b7275b7d8329a9fc3bfc90e20398fdf38b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57845097"
---
# <a name="tutorial-azure-active-directory-integration-with-origami"></a>Esercitazione: Integrazione di Azure Active Directory con Origami

Questa esercitazione descrive come integrare Origami con Azure Active Directory (Azure AD).
L'integrazione di Origami con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Origami.
* È possibile abilitare gli utenti di essere automaticamente eseguito l'accesso a Origami (Single Sign-On) con i propri account Azure AD.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Origami, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione abilitata per Origami single sign-on

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Supporta Origami **SP** SSO avviato dal

## <a name="adding-origami-from-the-gallery"></a>Aggiunta di Origami dalla raccolta

Per configurare l'integrazione di Origami in Azure AD, è necessario aggiungere Origami dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Origami dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca, digitare **Origami**, selezionare **Origami** nel pannello dei risultati quindi fare clic su **Add** pulsante per aggiungere l'applicazione.

     ![Origami nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato Azure single sign-on AD con Origami con un utente test di nome **Britta Simon**.
Per single sign-on funzioni, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Origami.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Origami, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configura Origami Single Sign-On](#configure-origami-single-sign-on)**  : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di Origami](#create-origami-test-user)**  : per avere una controparte di Britta Simon in Origami collegata alla rappresentazione in Azure AD dell'utente.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare single sign-on di Azure con Origami, seguire i passaggi seguenti:

1. Nel [portale di Azure](https://portal.azure.com/)via le **Origami** pagina di integrazione dell'applicazione, seleziona **l'accesso Single sign-on**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![URL e dominio Origami informazioni single sign-on](common/sp-signonurl.png)

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://live.origamirisk.com/origami/account/login?account=<companyname>`

    > [!NOTE]
    > Poiché non è reale, è necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere tale valore, contattare il [team di supporto clienti di Origami](https://wordpress.org/support/theme/origami). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nel **configurazione di Origami** sezione, copiare l'URL appropriato in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-origami-single-sign-on"></a>Configura Origami Single Sign-On

1. Accedere all'account Origami con diritti di amministratore.

2. Nel menu in alto fare clic su **Admin**.
   
    ![Configure Single Sign-On](./media/origami-tutorial/tutorial_origami_51.png)

3. Nella pagina della finestra di dialogo Single Sign On Setup (Imposta accesso Single Sign-On) eseguire la procedura seguente:
   
    ![Configure Single Sign-On](./media/origami-tutorial/tutorial_origami_531.png)

    a. Selezionare **Abilita Single Sign-On**.

    b. Nel **Sign-in Page URL del Provider di identità** nella casella di testo, incollare il valore di **URL di accesso**, che è stato copiato dal portale di Azure.

    c. Nel **URL pagina di disconnessione del Provider di identità** nella casella di testo, incollare il valore di **URL di disconnessione**, che è stato copiato dal portale di Azure.

    d. Fare clic su **Sfoglia** per caricare il certificato scaricato dal portale di Azure.

    e. Fare clic su **Salva modifiche**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD 

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon@yourcompanydomain.extension**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a Origami.

1. Nel portale di Azure, selezionare **applicazioni aziendali**, selezionare **tutte le applicazioni**, quindi selezionare **Origami**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni, selezionare **Origami**.

    ![Il collegamento di Origami nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-origami-test-user"></a>Creare l'utente di test di Origami

In questa sezione viene creato un utente di nome Britta Simon in Origami. 

1. Accedere all'account Origami con diritti di amministratore.

2. Nel menu in alto fare clic su **Admin**.
   
    ![Configure Single Sign-On](./media/origami-tutorial/tutorial_origami_51.png)

3. Nella finestra di dialogo **Users and Security** (Utenti e sicurezza) fare clic su **Utenti**.
   
    ![Configure Single Sign-On](./media/origami-tutorial/tutorial_origami_54.png)

4. Fare clic su **Add new user**.
   
    ![Configure Single Sign-On](./media/origami-tutorial/tutorial_origami_55.png)

5. Nella finestra di dialogo Add New User seguire questa procedura:
   
    ![Configure Single Sign-On](./media/origami-tutorial/tutorial_origami_56.png)

    a. Nel **nome utente** casella di testo immettere l'indirizzo di posta elettronica dell'utente, ad esempio **brittasimon\@contoso.com**.

    b. Nella casella di testo **Password** digitare una password.

    c. Nella casella di testo **Conferma password** digitare di nuovo la password.

    d. Nella casella di testo **First Name** (Nome) immettere il nome dell'utente, ad esempio **Britta**.

    e. Nella casella di testo **Last Name** (Cognome) immettere il cognome dell'utente, ad esempio **Simon**.

    f. Fare clic su **Save**.
   
    ![Configure Single Sign-On](./media/origami-tutorial/tutorial_origami_57.png)

6. Assegnare i **ruoli utente** e l'**accesso client** all'utente. 
   
    ![Configure Single Sign-On](./media/origami-tutorial/tutorial_origami_58.png)

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Origami nel Pannello di accesso, dovrebbe automaticamente accedere a Origami per il quale configurare SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

