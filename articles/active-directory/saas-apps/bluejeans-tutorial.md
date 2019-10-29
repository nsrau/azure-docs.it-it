---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con BlueJeans for Azure AD | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e BlueJeans for Azure AD.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dfc634fd-1b55-4ba8-94a8-b8288429b6a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfc38f63c5b6361122c236543320b91d22faa70a
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595041"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bluejeans-for-azure-ad"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con BlueJeans for Azure AD

Questa esercitazione descrive come integrare BlueJeans for Azure AD con Azure Active Directory (Azure AD). Integrando BlueJeans for Azure AD con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a BlueJeans for Azure AD.
* Abilitare gli utenti per l'accesso automatico a BlueJeans for Azure AD con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di BlueJeans for Azure AD abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* BlueJeans for Azure AD supporta l'accesso SSO avviato da **SP**

* BlueJeans for Azure AD supporta il [provisioning utenti **automatico**](bluejeans-provisioning-tutorial.md)

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.

## <a name="adding-bluejeans-for-azure-ad-from-the-gallery"></a>Aggiunta di BlueJeans for Azure AD dalla raccolta

Per configurare l'integrazione di BlueJeans for Azure AD in Azure AD, è necessario aggiungere BlueJeans for Azure AD dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **BlueJeans for Azure AD** nella casella di ricerca.
1. Selezionare **BlueJeans for Azure AD** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-bluejeans-for-azure-ad"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per BlueJeans for Azure AD

Configurare e testare l'accesso SSO di Azure AD con BlueJeans for Azure AD usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in BlueJeans for Azure AD.

Per configurare e testare l'accesso SSO di Azure AD con BlueJeans for Azure AD, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di BlueJeans for Azure AD](#configure-bluejeans-for-azure-ad-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di BlueJeans for Azure AD](#create-bluejeans-for-azure-ad-test-user)** : per avere una controparte di B.Simon in BlueJeans for Azure AD collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **BlueJeans for Azure AD** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<companyname>.bluejeans.com`

    > [!NOTE]
    > Poiché non è reale, è necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere il valore, contattare il [team di supporto clienti di BlueJeans for Azure AD](https://support.bluejeans.com/contact). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura BlueJeans for Azure AD** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a BlueJeans for Azure AD.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **BlueJeans for Azure AD**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-bluejeans-for-azure-ad-sso"></a>Configurare l'accesso Single Sign-On di BlueJeans for Azure AD

1. In un'altra finestra del Web browser accedere al sito aziendale di **BlueJeans for Azure AD** come amministratore.

2. Passare ad **ADMIN \> GROUP SETTINGS \> SECURITY**.

    ![Admin](./media/bluejeans-tutorial/ic785868.png "Admin")

3. Nella sezione **SECURITY** seguire questa procedura:

    ![SAML Single Sign On](./media/bluejeans-tutorial/ic785869.png "SAML Single Sign On")

    a. Selezionare **SAML Single Sign On**.

    b. Selezionare **Enable automatic provisioning**.

4. Continuare e seguire questa procedura:

    ![Certificate Path](./media/bluejeans-tutorial/ic785870.png "Certificate Path")

    a. Fare clic su **Choose a File** (Scegli un file) per caricare il certificato codificato in base 64 scaricato dal portale di Azure.

    b. Nella casella di testo **URL di accesso** incollare il valore di **URL di accesso** copiato dal portale di Azure.

    c. Nella casella di testo **Modifica URL password** incollare il valore dell'**URL di modifica password** copiato dal portale di Azure.

    d. Nella casella di testo **URL disconnessione** incollare il valore dell'**URL di disconnessione** copiato dal portale di Azure.

5. Continuare e seguire questa procedura:

    ![Salva modifiche](./media/bluejeans-tutorial/ic785874.png "Salva modifiche")

    a. Nella casella di testo **User Id** (ID utente) digitare `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    b. Nella casella di testo **Email** (Posta elettronica) digitare `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    c. Fare clic su **SAVE CHANGES** (Salva modifiche).

### <a name="create-bluejeans-for-azure-ad-test-user"></a>Creare l'utente di test di BlueJeans for Azure AD

Questa sezione descrive come creare un utente di nome B.Simon in BlueJeans for Azure AD. BlueJeans for Azure AD supporta il provisioning utenti automatico, che è abilitato per impostazione predefinita. Altri dettagli su come configurare il provisioning utenti automatico sono disponibili [qui](bluejeans-provisioning-tutorial.md).

**Per creare un utente manualmente, seguire questa procedura:**

1. Accedere al sito aziendale di **BlueJeans for Azure AD** come amministratore.

2. Passare ad **AMMINISTRATORE \> GESTISCI UTENTI \> AGGIUNGI UTENTE**.

    ![Admin](./media/bluejeans-tutorial/ic785877.png "Admin")

    > [!IMPORTANT]
    > La scheda **AGGIUNGI UTENTE** è disponibile solo se nella scheda **SICUREZZA** l'opzione **Enable automatic provisioning** è deselezionata.

3. Nella sezione **AGGIUNGI UTENTE** seguire questa procedura:

    ![Add User](./media/bluejeans-tutorial/ic785886.png "Aggiunta di un utente")

    a. Nella casella di testo **First name** (Nome) immettere il nome dell'utente, ad esempio **B**.

    b. Nella casella di testo **Last Name** (Nome) immettere il cognome dell'utente, ad esempio **Simon**.

    c. Nella casella di testo **Pick a BlueJeans for Azure AD Username** (Scegli un nome utente BlueJeans for Azure AD) immettere il nome utente dell'utente, ad esempio **Brittasimon**

    d. Nella casella di testo **Crea una password** immettere la password.

    e. Nella casella di testo **Società** immettere il nome della società.

    f. Nella casella di testo **Email address** (Indirizzo e-mail) immettere l'indirizzo di posta elettronica dell'utente, ad esempio `b.simon\@contoso.com`.

    g. Nella casella di testo **Create a BlueJeans for Azure AD Meeting I.D.** (Crea un ID riunione BlueJeans for Azure AD) immettere l'ID riunione.

    h. Nella casella di testo **Pick a Moderator Passcode** (Seleziona passcode moderatore) immettere il passcode.

    i. Fare clic su **CONTINUE** (Continua).

    ![Add User](./media/bluejeans-tutorial/ic785887.png "Aggiunta di un utente")

    J. Fare clic su **Aggiungi utente**.

> [!NOTE]
> È possibile usare qualsiasi altro strumento di creazione di account utente di BlueJeans for Azure AD o qualsiasi API fornita da BlueJeans for Azure AD per effettuare il provisioning degli account utente di Azure AD.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di BlueJeans for Azure AD nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di BlueJeans for Azure AD per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare BlueJeans for Azure AD con Azure AD](https://aad.portal.azure.com/)

