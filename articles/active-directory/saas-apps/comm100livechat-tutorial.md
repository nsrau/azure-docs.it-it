---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Comm100 Live Chat | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Comm100 Live Chat.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/22/2019
ms.author: jeedes
ms.openlocfilehash: 9636c8cbb517c7aece450f53cfc37e4ddd9803b7
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92455494"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-comm100-live-chat"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Comm100 Live Chat

Questa esercitazione descrive come integrare Comm100 Live Chat con Azure Active Directory (Azure AD). Integrando Comm100 Live Chat con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Comm100 Live Chat.
* Abilitare gli utenti per l'accesso automatico a Comm100 Live Chat con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Comm100 Live Chat abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Comm100 Live Chat supporta l'accesso SSO avviato da **SP**

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.

## <a name="adding-comm100-live-chat-from-the-gallery"></a>Aggiunta di Comm100 Live Chat dalla raccolta

Per configurare l'integrazione di Comm100 Live Chat in Azure AD, è necessario aggiungere Comm100 Live Chat dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory** .
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni** .
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione** .
1. Nella sezione **Aggiungi dalla raccolta** digitare **Comm100 Live Chat** nella casella di ricerca.
1. Selezionare **Comm100 Live Chat** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-comm100-live-chat"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Comm100 Live Chat

Configurare e testare l'accesso SSO di Azure AD con Comm100 Live Chat usando un utente di test di nome **B.Simon** . Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Comm100 Live Chat.

Per configurare e testare l'accesso SSO di Azure AD con Comm100 Live Chat, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Comm100 Live Chat](#configure-comm100-live-chat-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Comm100 Live Chat](#create-comm100-live-chat-test-user)** : per avere una controparte di B.Simon in Comm100 Live Chat collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Comm100 Live Chat** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On** .
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML** .
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<SUBDOMAIN>.comm100.com/AdminManage/LoginSSO.aspx?siteId=<SITEID>`

    > [!NOTE] 
    > Poiché il valore di URL accesso non è reale, È necessario aggiornarlo con l'URL di accesso effettivo, descritto più avanti nell'esercitazione.

1. L'applicazione Comm100 Live Chat prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/edit-attribute.png)

1. Oltre quelli elencati in precedenza, l'applicazione Comm100 Live Chat prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.

    | Nome |  Attributo di origine|
    | ---------------| --------------- |
    |   email    | user.mail |

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura Comm100 Live Chat** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory** , **Utenti** e quindi **Tutti gli utenti** .
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password** .
   1. Fare clic su **Crea** .

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Comm100 Live Chat.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni** .
1. Nell'elenco delle applicazioni selezionare **Comm100 Live Chat** .
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi** .

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione** .

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna** .

## <a name="configure-comm100-live-chat-sso"></a>Configurare l'accesso Single Sign-On di Comm100 Live Chat

1. In un'altra finestra del Web browser accedere a Comm100 Live Chat come amministratore della sicurezza.

1. Fare clic su **Account personale** in alto a destra.

   ![Account personale in Comm100 Live Chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

1. A sinistra nel menu fare clic su **Security** (Sicurezza) e quindi su **Agent Single Sign-On** (Agente Single Sign-On).

   ![Screenshot che mostra il menu dell'account sul lato sinistro con le opzioni "Security" e "Agent Single Sign-On" evidenziate.](./media/comm100livechat-tutorial/tutorial_comm100livechat_security.png)

1. Nella pagina **Agent Single Sign-On** (Agente Single Sign-On) seguire questa procedura:

   ![Sicurezza in Comm100 Live Chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_singlesignon.png)

   a. Copiare il primo collegamento evidenziato e incollarlo nella casella di testo **URL di accesso** nella sezione **Configurazione SAML di base** del portale di Azure.

   b. Nella casella di testo **SAML SSO URL** (URL SSO SAML) incollare il valore di **URL di accesso** copiato dal portale di Azure.

   c. Nella casella di testo **Remote Logout URL** (URL di disconnessione remota) incollare il valore di **URL di disconnessione** copiato dal portale di Azure.

   d. Fare clic su **Choose a File** (Scegli un file) per caricare il certificato codificato in base 64 scaricato dal portale di Azure in **Certificate** (Certificato).

   e. Fare clic su **Salva modifiche** .

### <a name="create-comm100-live-chat-test-user"></a>Creare l'utente di test di Comm100 Live Chat

Per consentire agli utenti di Azure AD di accedere a Comm100 Live Chat, è necessario effettuarne il provisioning in Comm100 Live Chat. In Comm100 Live Chat, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere a Comm100 Live Chat come amministratore della sicurezza.

2. Fare clic su **Account personale** in alto a destra.

    ![Account personale in Comm100 Live Chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

3. A sinistra nel menu fare clic su **Agents** (Agenti) e quindi su **New Agent** (Nuovo agente).

    ![Agente in Comm100 Live Chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_agent.png)

4. Nella pagina **New Agent** (Nuovo agente) seguire questa procedura:

    ![Nuovo agente in Comm100 Live Chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_newagent.png)

    a. a. Nella casella di testo **Email** (Posta elettronica) immettere l'indirizzo di posta elettronica dell'utente, ad esempio **B.simon\@contoso.com** .

    b. Nella casella di testo **First name** (Nome) immettere il nome dell'utente, ad esempio **B** .

    c. Nella casella di testo **Last name** (Cognome) immettere il cognome dell'utente, ad esempio **Simon** .

    d. Nella casella di testo **Display Name** (Nome visualizzato) digitare il nome visualizzato dell'utente, ad esempio **B.simon** .

    e. Nella casella di testo **Password** digitare la password.

    f. Fare clic su **Salva** .

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Comm100 Live Chat nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Comm100 Live Chat per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](./tutorial-list.md)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)

- [Provare Comm100 Live Chat con Azure AD](https://aad.portal.azure.com/)