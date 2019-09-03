---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Watch by Colors | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Watch by Colors.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 586a029c-fb8d-4233-b280-103b9ba7102d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce0882be0419cbbbc7d94cb8d517e27bdb06a780
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013936"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-watch-by-colors"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Watch by Colors

Questa esercitazione descrive come integrare Watch by Colors con Azure Active Directory (Azure AD). Integrando Watch by Colors con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Watch by Colors.
* Abilitare gli utenti per l'accesso automatico a Watch by Colors con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Watch by Colors abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Watch by Colors supporta l'accesso SSO avviato da **SP e IDP**

## <a name="adding-watch-by-colors-from-the-gallery"></a>Aggiunta di Watch by Colors dalla raccolta

Per configurare l'integrazione di Watch by Colors in Azure AD, è necessario aggiungere Watch by Colors dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Watch by Colors** nella casella di ricerca.
1. Selezionare **Watch by Colors** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-watch-by-colors"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Watch by Colors

Configurare e testare l'accesso SSO di Azure AD con Watch by Colors usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Watch by Colors.

Per configurare e testare l'accesso SSO di Azure AD con Watch by Colors, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Watch by Colors](#configure-watch-by-colors-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Watch by Colors](#create-watch-by-colors-test-user)** : per avere una controparte di B.Simon in Watch by Colors collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Watch by Colors** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** l'applicazione è preconfigurata in modalità avviata da **IDP** e gli URL necessari sono già prepopolati con Azure. L'utente deve salvare la configurazione facendo clic sul pulsante **Salva**. 

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL di accesso** digitare un URL: `https://app.colorscorporation.com/login`

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e salvarlo nel computer.

    ![Collegamento di download del certificato](common/copy-metadataurl.png)

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Watch by Colors.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Watch by Colors**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-watch-by-colors-sso"></a>Configurare l'accesso Single Sign-On di Watch by Colors

1. Aprire una nuova finestra del Web browser, accedere al sito aziendale di Watch by Colors come amministratore e seguire questa procedura:

1. Nell'angolo in alto a destra della pagina fare clic su **Profile** (Profilo)  > **Account Settings** (Impostazioni account) > **SSO (Single Sign-On)** .

    ![Configurazione di Watch by Colors](./media/watch-by-colors-tutorial/config01.png)

1. Nella pagina **SSO (Single Sign-On)** eseguire la procedura seguente:

    ![Configurazione di Watch by Colors](./media/watch-by-colors-tutorial/config02.png)

    a. Impostare **Enable SAML** (Abilita SAML) su **ON** (Attivato).

    b. Nella casella di testo **URL** incollare l'**URL dei metadati di federazione** copiato dal portale di Azure.

    c. Fare clic su **Import** (Importa) per popolare automaticamente nella pagina i campi seguenti.

    d. Fare clic su **Save**.

### <a name="create-watch-by-colors-test-user"></a>Creare l'utente di test di Watch by Colors

Per consentire agli utenti di Azure AD di accedere a Watch by Colors, è necessario effettuarne il provisioning in Watch by Colors. Nel caso di Watch by Colors il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere a Watch by Colors come amministratore della sicurezza.

1. Nell'angolo in alto a destra della pagina fare clic su **Profile** (Profilo)  > **Users** (Utenti) > **Add User** (Aggiungi utente).

    ![Configurazione di Watch by Colors](./media/watch-by-colors-tutorial/config03.png)

1. Nella pagina **User Details** (Dettagli utente) seguire questa procedura:

    ![Configurazione di Watch by Colors](./media/watch-by-colors-tutorial/config04.png)

    a. Nella casella di testo **First name** (Nome) immettere il nome dell'utente, ad esempio **B**.

    b. Nella casella di testo **Last Name** (Nome) immettere il cognome dell'utente, ad esempio **Simon**.

    c. Nella casella di testo **E-mail** (Posta elettronica) immettere l'indirizzo di posta elettronica dell'utente, ad esempio `B.Simon@contoso.com`.

    d. Nella casella di testo **Password** immettere la password.

    e. Selezionare **Account Permissions** (Autorizzazioni per l'account) in base alle indicazioni dell'organizzazione.

    f. Fare clic su **Save**.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Watch by Colors nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Watch by Colors per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare Slack con Azure AD](https://aad.portal.azure.com/)

