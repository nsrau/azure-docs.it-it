---
title: 'Esercitazione: Integrazione di Azure Active Directory con Comm100 Live Chat | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Comm100 Live Chat.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0340d7f3-ab54-49ef-b77c-62a0efd5d49c
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/22/2019
ms.author: jeedes
ms.openlocfilehash: 1e0fbf7678ddac76d1a31c4bde4d75545b429add
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2019
ms.locfileid: "55700696"
---
# <a name="tutorial-azure-active-directory-integration-with-comm100-live-chat"></a>Esercitazione: Integrazione di Azure Active Directory con Comm100 Live Chat

Questa esercitazione descrive come integrare Comm100 Live Chat con Azure Active Directory (Azure AD).
L'integrazione di Comm100 Live Chat con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Comm100 Live Chat.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Comm100 Live Chat con gli account Azure AD.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Comm100 Live Chat, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Comm100 Live Chat abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Comm100 Live Chat supporta l'accesso SSO avviato da **SP**

## <a name="adding-comm100-live-chat-from-the-gallery"></a>Aggiunta di Comm100 Live Chat dalla raccolta

Per configurare l'integrazione di Comm100 Live Chat in Azure AD, è necessario aggiungere Comm100 Live Chat dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Comm100 Live Chat dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Comm100 Live Chat**, selezionare **Comm100 Live Chat** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Comm100 Live Chat nell'elenco dei risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Comm100 Live Chat usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Comm100 Live Chat.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Comm100 Live Chat, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On per Comm100 Live Chat ](#configure-comm100-live-chat-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di Comm100 Live Chat](#create-comm100-live-chat-test-user)**: per avere una controparte di Britta Simon in Comm100 Live Chat collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Comm100 Live Chat, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Comm100 Live Chat** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per Single Sign-On di Comm100 Live Chat](common/sp-signonurl.png)

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<SUBDOMAIN>.comm100.com/AdminManage/LoginSSO.aspx?siteId=<SITEID>`

    > [!NOTE] 
    > Poiché il valore dell'URL di accesso non è reale, È necessario aggiornarlo con l'URL di accesso effettivo, descritto più avanti nell'esercitazione.

5. L'applicazione Comm100 Live Chat prevede un formato specifico per le asserzioni SAML. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente** nella pagina di integrazione dell'applicazione. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Attributi utente**.

    ![image](common/edit-attribute.png)

6. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** modificare le attestazioni usando l'**icona Modifica** o aggiungere le attestazioni usando l'opzione **Aggiungi nuova attestazione** per configurare l'attributo del token SAML come mostrato nell'immagine precedente e seguire questa procedura: 

    | Nome |  Attributo di origine|
    | ---------------| --------------- |
    |   email    | user.mail |

    a. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Lasciare vuota la casella **Spazio dei nomi**.

    d. Per Origine selezionare **Attributo**.

    e. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo indicato per la riga.

    f. Fare clic su **Ok**

    g. Fare clic su **Save**.

4. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Set up Comm100 Live Chat** (Configura Comm100 Live Chat) copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-comm100-live-chat-single-sign-on"></a>Configurare l'accesso Single Sign-On per Comm100 Live Chat

9. In un'altra finestra del Web browser accedere a Comm100 Live Chat come amministratore della sicurezza.

10. Fare clic su **Account personale** in alto a destra.

    ![Account personale in Comm100 Live Chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

11. A sinistra nel menu fare clic su **Security** (Sicurezza) e quindi su **Agent Single Sign-On** (Agente Single Sign-On).

    ![Sicurezza in Comm100 Live Chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_security.png)

12. Nella pagina **Agent Single Sign-On** (Agente Single Sign-On) seguire questa procedura:

    ![Sicurezza in Comm100 Live Chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_singlesignon.png)

    a. Copiare il primo collegamento evidenziato e incollarlo nella casella di testo **URL accesso** nella sezione **URL e dominio Comm100 Live Chat** del portale di Azure.

    b. Nella casella di testo **SAML SSO URL** (URL SSO SAML) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    c. Nella casella di testo **Remote Logout URL** (URL di disconnessione remota) incollare il valore di **URL di disconnessione** copiato dal portale di Azure.

    d. Fare clic su **Choose a File** (Scegli un file) per caricare il certificato codificato in base 64 scaricato dal portale di Azure in **Certificate** (Certificato).

    e. Fare clic su **Save Changes** (Salva modifiche).

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

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Comm100 Live Chat.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Comm100 Live Chat**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Comm100 Live Chat**.

    ![Collegamento di Comm100 Live Chat nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

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

    a. a. Nella casella di testo **Email** (Posta elettronica) immettere l'indirizzo di posta elettronica dell'utente, ad esempio **Brittasimon@contoso.com**.

    b. Nella casella di testo **First Name** (Nome) immettere il nome dell'utente, ad esempio **Britta**.

    c. Nella casella di testo **Last name** (Cognome) immettere il cognome dell'utente, ad esempio **Simon**.

    d. Nella casella di testo **Display Name** (Nome visualizzato) digitare il nome visualizzato dell'utente, ad esempio **Britta Simon**.

    e. Nella casella di testo **Password** digitare la password.

    f. Fare clic su **Save**.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Comm100 Live Chat nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Comm100 Live Chat per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

