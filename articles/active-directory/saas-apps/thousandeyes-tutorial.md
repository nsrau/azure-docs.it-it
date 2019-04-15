---
title: 'Esercitazione: Integrazione di Azure Active Directory con ThousandEyes | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e ThousandEyes.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 790e3f1e-1591-4dd6-87df-590b7bf8b4ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 587d6f8ecdc447672c14ce400ebc64609e4d472d
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59273907"
---
# <a name="tutorial-azure-active-directory-integration-with-thousandeyes"></a>Esercitazione: Integrazione di Azure Active Directory con ThousandEyes

Questa esercitazione descrive come integrare ThousandEyes con Azure Active Directory (Azure AD).
L'integrazione di ThousandEyes con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a ThousandEyes.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a ThousandEyes con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con ThousandEyes, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di ThousandEyes abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* ThousandEyes supporta l'accesso SSO avviato da **SP**

* ThousandEyes supporta il [provisioning utenti **automatico**](https://docs.microsoft.com/azure/active-directory/saas-apps/thousandeyes-provisioning-tutorial)

## <a name="adding-thousandeyes-from-the-gallery"></a>Aggiunta di ThousandEyes dalla raccolta

Per configurare l'integrazione di ThousandEyes in Azure AD, è necessario aggiungere ThousandEyes dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere ThousandEyes dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **ThousandEyes**, selezionare **ThousandEyes** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![ThousandEyes nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con ThousandEyes usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in ThousandEyes.

Per configurare e testare l'accesso Single Sign-On di Azure AD con ThousandEyes, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di ThousandEyes](#configure-thousandeyes-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di ThousandEyes ](#create-thousandeyes-test-user)**: per avere una controparte di Britta Simon in ThousandEyes collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con ThousandEyes, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **ThousandEyes** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di ThousandEyes](common/sp-signonurl.png)

    Nella casella di testo **URL di accesso** digitare un URL: `https://app.thousandeyes.com/login/sso`

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura ThousandEyes** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-thousandeyes-single-sign-on"></a>Configurare l'accesso Single Sign-On di ThousandEyes

1. In un'altra finestra del browser Web accedere al sito aziendale di **ThousandEyes** come amministratore.

2. Nel menu in alto fare clic su **Impostazioni**.

    ![Impostazioni](./media/thousandeyes-tutorial/ic790066.png "Impostazioni")

3. Fare clic su **Account**

    ![Account](./media/thousandeyes-tutorial/ic790067.png "Account")

4. Fare clic sulla scheda **Security & Authentication** (Sicurezza e autenticazione).

    ![Sicurezza e autenticazione](./media/thousandeyes-tutorial/ic790068.png "Security e autenticazione")

5. Nella sezione **Setup Single Sign-On** eseguire la procedura seguente:

    ![Configurare l'accesso Single Sign-On](./media/thousandeyes-tutorial/ic790069.png "Configurare l'accesso Single Sign-On")

    a. Selezionare **Enable Single Sign-On**.

    b. Nella casella di testo **Login Page URL** (URL della pagina di accesso) incollare l'**URL di accesso** copiato dal portale di Azure.

    c. Nella casella di testo **Logout Page URL** (URL della pagina di disconnessione) incollare l'**URL di disconnessione** copiato dal portale di Azure.

    d. Nella casella di testo **Identity Provider Issuer** (Autorità di certificazione del provider di identità) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    e. In **Certificato di verifica** fare clic su **Scegli file** e quindi caricare il certificato scaricato dal portale di Azure.

    f. Fare clic su **Save**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD 

Questa sezione descrive come creare un utente di test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare brittasimon@yourcompanydomain.extension. Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a ThousandEyes.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **ThousandEyes**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **ThousandEyes**.

    ![Collegamento di ThousandEyes nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-thousandeyes-test-user"></a>Creare l'utente di test di ThousandEyes

Questa sezione descrive come creare un utente chiamato Britta Simon in ThousandEyes. ThousandEyes supporta il provisioning utenti automatico, che è abilitato per impostazione predefinita. Altri dettagli su come configurare il provisioning utenti automatico sono disponibili [qui](thousandeyes-provisioning-tutorial.md).

**Per creare un utente manualmente, seguire questa procedura:**

1. Accedere al sito aziendale di ThousandEyes come amministratore.

2. Fare clic su **Impostazioni**.

    ![Impostazioni](./media/thousandeyes-tutorial/IC790066.png "Impostazioni")

3. Fare clic su **Account**.

    ![Account](./media/thousandeyes-tutorial/IC790067.png "Account")

4. Fare clic sulla scheda **Accounts & Users** (Account e utenti).

    ![Account e utenti](./media/thousandeyes-tutorial/IC790073.png "Account e utenti")

5. Nella sezione **Add Users & Accounts** (Account e utenti) eseguire la procedura seguente:

    ![Aggiungere account utente](./media/thousandeyes-tutorial/IC790074.png "Aggiungere account utente")

    a. Nella casella di testo **Name** (Nome) digitare il nome dell'utente, ad esempio **Britta Simon**.

    b. Nella casella di testo **Email** (Posta elettronica) digitare l'indirizzo di posta elettronica dell'utente, ad esempio brittasimon@contoso.com.

    b. Fare clic su **Add New User to Account**.

    > [!NOTE]
    > Il titolare dell'account Azure Active Directory riceve un messaggio di posta elettronica con un collegamento da selezionare per confermare e attivare l'account.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da ThousandEyes per eseguire il provisioning degli account utente di Azure Active Directory.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di ThousandEyes nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione ThousandEyes per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Configura provisioning utenti](https://docs.microsoft.com/azure/active-directory/saas-apps/thousandeyes-provisioning-tutorial)
