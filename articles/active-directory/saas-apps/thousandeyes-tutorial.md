---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con ThousandEyes | Microsoft Docs"
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
ms.date: 10/04/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd179049f88454c62244cf1819cee08ef78d0633
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72373243"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-thousandeyes"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con ThousandEyes

Questa esercitazione descrive come integrare ThousandEyes con Azure Active Directory (Azure AD). Integrando ThousandEyes con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a ThousandEyes.
* Abilitare gli utenti per l'accesso automatico a ThousandEyes con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di ThousandEyes abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* ThousandEyes supporta l'accesso SSO avviato da **SP e IDP**
* ThousandEyes supporta il [provisioning utenti **automatico**](https://docs.microsoft.com/azure/active-directory/saas-apps/thousandeyes-provisioning-tutorial)

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.

## <a name="adding-thousandeyes-from-the-gallery"></a>Aggiunta di ThousandEyes dalla raccolta

Per configurare l'integrazione di ThousandEyes in Azure AD, è necessario aggiungere ThousandEyes dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **ThousandEyes** nella casella di ricerca.
1. Selezionare **ThousandEyes** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-thousandeyes"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per ThousandEyes

Configurare e testare l'accesso SSO di Azure AD con ThousandEyes usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in ThousandEyes.

Per configurare e testare l'accesso SSO di Azure AD con ThousandEyes, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    * **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    * **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di ThousandEyes](#configure-thousandeyes-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    * **[Creare l'utente di test di ThousandEyes](#create-thousandeyes-test-user)** : per avere una controparte di B.Simon in ThousandEyes collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **ThousandEyes** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** l'applicazione è preconfigurata e gli URL necessari sono già prepopolati con Azure. L'utente deve salvare la configurazione facendo clic sul pulsante **Salva**.

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL di accesso** digitare un URL: `https://app.thousandeyes.com/login/sso`

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura ThousandEyes** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a ThousandEyes.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **ThousandEyes**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-thousandeyes-sso"></a>Configurare l'accesso Single Sign-On di ThousandEyes

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

### <a name="create-thousandeyes-test-user"></a>Creare l'utente di test di ThousandEyes

Questa sezione descrive come creare un utente chiamato Britta Simon in ThousandEyes. ThousandEyes supporta il provisioning utenti automatico, che è abilitato per impostazione predefinita. È possibile scoprire più dettagli [qui](thousandeyes-provisioning-tutorial.md) su come configurare il provisioning utenti automatico.

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

    a. Nella casella di testo **Name** (Nome) digitare il nome dell'utente, ad esempio **B.Simon**.

    b. Nella casella di testo **Email** (Posta elettronica) digitare l'indirizzo di posta elettronica dell'utente, ad esempio b.simon@contoso.com.

    b. Fare clic su **Add New User to Account**.

    > [!NOTE]
    > Il titolare dell'account Azure Active Directory riceve un messaggio di posta elettronica con un collegamento da selezionare per confermare e attivare l'account.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da ThousandEyes per eseguire il provisioning degli account utente di Azure Active Directory.


## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di ThousandEyes nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di ThousandEyes per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare ThousandEyes con Azure AD](https://aad.portal.azure.com/)

- [Configura provisioning utenti](https://docs.microsoft.com/azure/active-directory/saas-apps/thousandeyes-provisioning-tutorial)