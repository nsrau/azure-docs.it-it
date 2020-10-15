---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Hub Planner | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Hub Planner.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/12/2020
ms.author: jeedes
ms.openlocfilehash: 1f1da374c2bf5ab8e570c705638ca4d6e627bcab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88549165"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-hub-planner"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Hub Planner

Questa esercitazione descrive come integrare Hub Planner con Azure Active Directory (Azure AD). Integrando Hub Planner con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Hub Planner.
* Abilitare gli utenti per l'accesso automatico a Hub Planner con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Hub Planner abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Hub Planner supporta l'accesso SSO avviato da **SP**.
* Dopo aver configurato Hub Planner, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-hub-planner-from-the-gallery"></a>Aggiunta di Hub Planner dalla raccolta

Per configurare l'integrazione di Hub Planner in Azure AD, è necessario aggiungere Hub Planner dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Hub Planner** nella casella di ricerca.
1. Selezionare **Hub Planner** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-hub-planner"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Hub Planner

Configurare e testare l'accesso SSO di Azure AD con Hub Planner usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Hub Planner.

Per configurare e testare l'accesso SSO di Azure AD con Hub Planner, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Hub Planner](#configure-hub-planner-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Hub Planner](#create-hub-planner-test-user)** : per avere una controparte di B.Simon in Hub Planner collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Hub Planner** del [portale di Azure](https://portal.azure.com/) trovare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<SUBDOMAIN>.hubplanner.com`

    b. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://app.hubplanner.com/sso/metadata`

    c. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://app.hubplanner.com/sso/callback`

    > [!NOTE]
    > Questi valori sono quelli che verranno usati. L'unica modifica da apportare è la sostituzione di \<SUBDOMAIN\> nell'**URL di accesso** con il sottodominio ricevuto al momento dell'iscrizione a Hub Planner. È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura Hub Planner** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Hub Planner.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Hub Planner**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-hub-planner-sso"></a>Configurare l'accesso Single Sign-On di Hub Planner

Per configurare la funzionalità Single Sign-On nel lato **Hub Planner**, è necessario accedere all'account Hub Planner e completare le attività seguenti. 

### <a name="install-the-extension-in-hub-planner"></a>Installare l'estensione in Hub Planner

Per abilitare la funzionalità SSO, è prima necessario abilitare l'estensione. Se si è proprietario dell'account o si hanno autorizzazioni equivalenti, seguire questa procedura:

1. Passare a **Impostazioni**.
1. Nel menu laterale selezionare **Manage Extensions** > **Add/Remove Extensions** (Gestisci estensioni > Aggiungi/Rimuovi estensioni).
1. Trovare l'estensione per Single Sign On e fare clic su Add (Aggiungi) o Try Free (Prova gratuitamente).
1. Quando richiesto, accettare le condizioni e quindi selezionare **Add Now** (Aggiungi ora).

### <a name="enable-sso"></a>Abilitare SSO

Dopo aver abilitato l'estensione, è necessario abilitare l'accesso SSO per l'account. 

1. Passare a **Impostazioni**.
1. Nel menu laterale selezionare **Authentication** (Autenticazione).
1. Selezionare **SSO (Single Sign-On)** .
1. Immettere le informazioni di autenticazione aggiuntive, come illustrato nella figura seguente e quindi selezionare **Save** (Salva).

![Screenshot delle impostazioni SSO](media/hub-planner-tutorial/sso-settings.png)

### <a name="create-hub-planner-test-user"></a>Creare l'utente di test di Hub Planner

Se si vogliono aggiungere altri utenti, passare a **Settings** > **Manage resources** (Impostazioni > Gestisci risorse) e aggiungere gli utenti da tale posizione. Assicurarsi di aggiungere l'indirizzo di posta elettronica e di invitarli. Gli utenti invitati riceveranno un messaggio di posta elettronica e potranno accedere tramite SSO. 

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Hub Planner nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Hub Planner per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare Hub Planner con Azure AD](https://aad.portal.azure.com/)

- [Informazioni sul controllo sessioni in Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Come proteggere Hub Planner con visibilità e controlli avanzati](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
