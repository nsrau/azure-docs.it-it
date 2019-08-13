---
title: 'Esercitazione: Integrazione di Azure Active Directory con RunMyProcess | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e RunMyProcess.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d31f7395-048b-4a61-9505-5acf9fc68d9b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46c31a209e8521b24e7f604dbe630f689fca484e
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68880389"
---
# <a name="tutorial-integrate-runmyprocess-with-azure-active-directory"></a>Esercitazione: Integrare RunMyProcess con Azure Active Directory

Questa esercitazione descrive come integrare RunMyProcess con Azure Active Directory (Azure AD). Integrando RunMyProcess con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a RunMyProcess.
* Abilitare gli utenti per l'accesso automatico a RunMyProcess con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di RunMyProcess abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* RunMyProcess supporta l'accesso SSO avviato da **provider di servizi**

## <a name="adding-runmyprocess-from-the-gallery"></a>Aggiunta di RunMyProcess dalla raccolta

Per configurare l'integrazione di RunMyProcess in Azure AD, è necessario aggiungere RunMyProcess dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **RunMyProcess** nella casella di ricerca.
1. Selezionare **RunMyProcess** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

Configurare e testare l'accesso SSO di Azure AD con RunMyProcess usando un utente di test di nome **B.Simon**. Per il corretto funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in RunMyProcess.

Per configurare e testare l'accesso SSO di Azure AD con RunMyProcess, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso SSO di RunMyProcess](#configure-runmyprocess-sso)** : per configurare le impostazioni dell'accesso Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di RunMyProcess](#create-runmyprocess-test-user)** : per avere una controparte di B.Simon in RunMyProcess collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **RunMyProcess** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://live.runmyprocess.com/live/<tenant id>`

    > [!NOTE]
    > Poiché non è reale, è necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere il valore, contattare il [team di supporto client di RunMyProcess](mailto:support@runmyprocess.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura RunMyProcess** copiare gli URL appropriati in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="configure-runmyprocess-sso"></a>Configurare l'accesso SSO di RunMyProcess

1. In un'altra finestra del Web browser accedere al tenant di RunMyProcess come amministratore.

1. Nel pannello di spostamento a sinistra fare clic su **Account** e selezionare **Configuration** (Configurazione).

    ![Configurazione accesso Single Sign-On sul lato app](./media/runmyprocess-tutorial/tutorial_runmyprocess_001.png)

1. Passare alla sezione **Authentication method** (Metodo di autenticazione) e seguire questa procedura:

    ![Configurazione accesso Single Sign-On sul lato app](./media/runmyprocess-tutorial/tutorial_runmyprocess_002.png)

    a. In **Metodo** selezionare **SSO con Samlv2**.

    b. Nella casella di testo **SSO redirect** (Reindirizzamento SSO) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    c. Nella casella di testo **Logout redirect** (Reindirizzamento disconnessione) incollare il valore di **URL di disconnessione** copiato dal portale di Azure.

    d. Nella casella di testo **Name ID Format** (Formato ID nome) digitare il valore di **Formato identificatore nome** come **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**.

    e. Aprire nel Blocco note il file di certificato scaricato dal portale di Azure, copiarne il contenuto e incollarlo nella casella di testo **Certificate** (Certificato).

    f. Fare clic sull'icona di **salvataggio**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a RunMyProcess.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco di applicazioni selezionare **RunMyProcess**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-runmyprocess-test-user"></a>Creare un utente di test di RunMyProcess

Per consentire agli utenti di Azure AD di accedere a RunMyProcess, è necessario eseguirne il provisioning in RunMyProcess. Nel caso di RunMyProcess, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di RunMyProcess come amministratore.

1. Nel pannello di spostamento a sinistra fare clic su **Account** e selezionare **Users** (Utenti), quindi fare clic su **New User** (Nuovo utente).

    ![Nuovo utente](./media/runmyprocess-tutorial/tutorial_runmyprocess_003.png "Nuovo utente")

1. Nella sezione **Impostazioni utente** , eseguire la procedura seguente:

    ![Profilo](./media/runmyprocess-tutorial/tutorial_runmyprocess_004.png "Profilo")
  
    a. Digitare il **nome** e l'**indirizzo di posta elettronica** di un account di Azure AD valido di cui si desidera eseguire il provisioning nelle relative caselle di testo.

    b. Selezionare una **lingua IDE**, una **lingua** e un **profilo**.

    c. Selezionare **Inviami un messaggio di posta elettronica di creazione account**.

    d. Fare clic su **Save**.

    > [!NOTE]
    > È possibile usare qualsiasi altro strumento o API di creazione di account utente offerti da RunMyProcess per eseguire il provisioning degli account utente di Azure AD.

### <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di RunMyProcess nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione RunMyProcess per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)