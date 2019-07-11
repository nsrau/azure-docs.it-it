---
title: 'Esercitazione: Integrazione di Azure Active Directory con Azure AD SAML Toolkit | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Azure AD SAML Toolkit.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 3f4348e7-c34e-43c7-926e-f1b26ffacf6d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4baeda4fca69969fae3f866e642ad3dc8ad46509
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67091928"
---
# <a name="tutorial-integrate-azure-ad-saml-toolkit-with-azure-active-directory"></a>Esercitazione: Integrare Azure AD SAML Toolkit con Azure Active Directory

Questa esercitazione descrive come integrare Azure AD SAML Toolkit con Azure Active Directory (Azure AD). Integrando Azure AD SAML Toolkit con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere ad Azure AD SAML Toolkit.
* Abilitare gli utenti per l'accesso automatico ad Azure AD SAML Toolkit con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere una versione di valutazione gratuita per un mese [qui](https://azure.microsoft.com/pricing/free-trial/).
* Sottoscrizione di Azure AD SAML Toolkit abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Azure AD SAML Toolkit supporta l'accesso SSO avviato da **SP**.

## <a name="adding-azure-ad-saml-toolkit-from-the-gallery"></a>Aggiunta di Azure AD SAML Toolkit dalla raccolta

Per configurare l'integrazione di Azure AD SAML Toolkit in Azure AD, è necessario aggiungere Azure AD SAML Toolkit dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Azure AD SAML Toolkit** nella casella di ricerca.
1. Selezionare **Azure AD SAML Toolkit** nel riquadro dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

Configurare e testare l'accesso SSO di Azure AD con Azure AD SAML Toolkit usando un utente di test di nome **B. Simon**. Per il corretto funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Azure AD SAML Toolkit.

Per configurare e testare l'accesso SSO di Azure AD con Azure AD SAML Toolkit, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Azure AD SAML Toolkit](#configure-azure-ad-saml-toolkit-sso)** per configurare le impostazioni dell'accesso Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Azure AD SAML Toolkit](#create-azure-ad-saml-toolkit-test-user)** per avere una controparte di B. Simon in Azure AD SAML Toolkit collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-sso)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Azure AD SAML Toolkit** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella pagina **Configurazione SAML di base** immettere i valori per i campi seguenti:

    1. Nella casella di testo **URL di accesso** digitare l'URL `https://samltoolkit.azurewebsites.net/`

    1. Nella casella di testo **Identificatore (ID entità)** digitare un URL: `https://samltoolkit.azurewebsites.net`

    1. Nella casella di testo **URL di risposta** digitare un URL: `https://samltoolkit.azurewebsites.net/SAML/Consume`

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** fare clic su **Scarica** per scaricare il file **XML dei metadati della federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

   ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura Azure AD SAML Toolkit** copiare gli URL appropriati in base alle esigenze.

   ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="configure-azure-ad-saml-toolkit-sso"></a>Configurare l'accesso Single Sign-On per Azure AD SAML Toolkit

1. Aprire una nuova finestra del Web browser e, se non è ancora stata effettuata la registrazione nel sito Web di Azure AD SAML Toolkit, prima di tutto registrarsi facendo clic su **Register** (Registra). Se è già stata effettuata la registrazione, accedere al sito aziendale di Azure AD SAML Toolkit usando le credenziali di accesso registrate.

    ![Registrazione in Azure AD SAML Toolkit](./media/saml-toolkit-tutorial/register.png)

1. Fare clic su **SAML Configuration** (Configurazione SAML).

    ![Configurazione SAML in Azure AD SAML Toolkit](./media/saml-toolkit-tutorial/saml-configure.png)

1. Fare clic su **Create**(Crea).

    ![Creazione dell'accesso SSO in Azure AD SAML Toolkit](./media/saml-toolkit-tutorial/createsso.png)

1. Nella pagina **SAML SSO Configuration** (Configurazione SSO SAML) seguire questa procedura:

    ![Creazione dell'accesso SSO in Azure AD SAML Toolkit](./media/saml-toolkit-tutorial/fill-details.png)

    1. Nella casella di testo **Login URL** (URL di accesso) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    1. Nella casella di testo **Azure AD Identifier** (Identificatore Azure AD) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    1. Nella casella di testo **Logout URL** (URL disconnessione) incollare il valore di **URL disconnessione** copiato dal portale di Azure.

    1. Fare clic su **Choose File** (Scegli file) e caricare il file **XML dei metadati della federazione** scaricato dal portale di Azure.

    1. Fare clic su **Create**(Crea).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

In questa sezione verrà creato un utente di test di nome B. Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abiliterà B. Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad Azure AD SAML Toolkit.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Azure AD SAML Toolkit**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B. Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-azure-ad-saml-toolkit-test-user"></a>Creare l'utente di test di Azure AD SAML Toolkit

In questa sezione viene creato un utente di nome B. Simon in Azure AD SAML Toolkit. Azure AD SAML Toolkit supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in Azure AD SAML Toolkit, ne viene creato uno nuovo dopo l'autenticazione.

### <a name="test-sso"></a>Testare l'accesso SSO

Quando si seleziona il riquadro di Azure AD SAML Toolkit nel Pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Azure AD SAML Toolkit per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
