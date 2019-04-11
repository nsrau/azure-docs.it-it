---
title: 'Esercitazione: Integrazione di Azure Active Directory con Teamphoria| Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Teamphoria.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d569c705-6f0f-4ec1-b485-ba82526b5d32
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 11d0a0a67d1ce5049166cab3d9ca3e4903b6b460
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59277205"
---
# <a name="tutorial-azure-active-directory-integration-with-teamphoria"></a>Esercitazione: Integrazione di Azure Active Directory con Teamphoria

Questa esercitazione illustra come integrare Teamphoria con Azure Active Directory (Azure AD).
L'integrazione di Teamphoria con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Teamphoria.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Teamphoria con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Teamphoria, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Teamphoria abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Teamphoria supporta l'accesso SSO avviato da **SP**

## <a name="adding-teamphoria-from-the-gallery"></a>Aggiungere Teamphoria dalla raccolta

Per configurare l'integrazione di Teamphoria in Azure AD, è necessario aggiungere Teamphoria dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Teamphoria dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Teamphoria**, selezionare **Teamphoria** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Teamphoria nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Teamphoria usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Teamphoria.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Teamphoria, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Teamphoria](#configure-teamphoria-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di Teamphoria](#create-teamphoria-test-user)**: per avere una controparte di Britta Simon in Teamphoria collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Teamphoria, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Teamphoria** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Teamphoria](common/sp-intiated.png)

    Nella casella di testo **URL di accesso** digitare un URL usando il modello seguente: `https://<sub-domain>.teamphoria.com/login`

    > [!NOTE]
    > Poiché il valore dell'URL di accesso non è reale, È necessario aggiornare questo valore con l'URL di accesso effettivo. Contattare il [team di supporto clienti di Teamphoria](https://www.teamphoria.com/) per richiedere l'URL di accesso. È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura Teamphoria** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-teamphoria-single-sign-on"></a>Configurare l'accesso Single Sign-On di Teamphoria

1. Per configurare l'accesso Single Sign-On sul lato **Teamphoria**, accedere all'applicazione Teamphoria come amministratore.

1. Passare all'opzione **ADMIN SETTINGS** (Impostazioni di amministrazione) nella barra degli strumenti di sinistra e scegliere **SINGLE SIGN-ON** nella scheda di configurazione per aprire la finestra di configurazione SSO.

    ![Configure Single Sign-On](./media/teamphoria-tutorial/admin_sso_configure.png)

1. Fare clic sull'opzione **ADD NEW IDENTITY PROVIDER** (Aggiungi nuovo provider di identità) nell'angolo superiore destro per aprire il modulo e aggiungere le impostazioni per l'accesso Single Sign-On.

    ![Configure Single Sign-On](./media/teamphoria-tutorial/add_new_identity_provider.png)

1. Immettere i dettagli nei campi come descritto di seguito.

    ![Configure Single Sign-On](./media/teamphoria-tutorial/Teamphoria_sso_save.png)

    a. **NOME VISUALIZZATO**: immettere il nome del plug-in visualizzato nella pagina di amministrazione.

    b. **NOME PULSANTE**: il nome della scheda che verrà visualizzata nella pagina di accesso per l'accesso Single Sign-On.

    c. **CERTIFICATO**: aprire il certificato scaricato in precedenza dal portale di Azure nel Blocco note, copiare il contenuto dello stesso e incollarlo qui nella casella.

    d. **PUNTO DI INGRESSO**: incollare l'**URL di accesso** copiato in precedenza dal portale di Azure.

    e. Posizionare il selettore dell'opzione su **ON** e fare clic su **SAVE** (Salva).

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

In questa sezione viene concesso a Britta Simon l'accesso a Teamphoria per consentirle di usare l'accesso Single Sign-On di Azure.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Teamphoria**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Teamphoria**.

    ![Collegamento di Teamphoria nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-teamphoria-test-user"></a>Creare l'utente di test di Teamphoria

Per consentire agli utenti di Azure AD di accedere a Teamphoria, è necessario effettuarne il provisioning in Teamphoria. Nel caso di Teamphoria, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di Teamphoria come amministratore.

1. Fare clic sulle impostazioni **ADMIN** nella barra degli strumenti di sinistra e scegliere **USERS** (Utenti) nella scheda **MANAGE** (Gestisci) per aprire la pagina di amministrazione per gli utenti.

    ![Aggiungere un dipendente](./media/teamphoria-tutorial/admin_manage_users.png)

1. Fare clic sull'opzione **MANUAL INVITE** (Invito manuale).

    ![Invita persone](./media/teamphoria-tutorial/admin_manage_add_users.png)

1. In questa pagina effettuare l'operazione seguente.

    ![Invita persone](./media/teamphoria-tutorial/manual_user_invite.png)

    a. Nella casella di testo **EMAIL ADDRESS** (INDIRIZZO DI POSTA ELETTRONICA) immettere l'**indirizzo di posta elettronica** dell'utente, ad esempio BrittaSimon.

    b. Nella casella di testo **FIRST NAME** (NOME) immettere il nome dell'utente, ad esempio **Britta**.

    c. Nella casella di testo **LAST NAME** (COGNOME) immettere il cognome dell'utente, ad esempio **Simon**.

    d. Fare clic su **INVITE 1 USER** (Invita 1 utente). L'utente deve accettare l'invito per essere creato nel sistema.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Teamphoria nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Teamphoria per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

