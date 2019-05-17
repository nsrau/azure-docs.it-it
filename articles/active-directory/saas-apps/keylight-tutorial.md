---
title: 'Esercitazione: Integrazione di Azure Active Directory con LockPath Keylight | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e LockPath Keylight.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 234a32f1-9f56-4650-9e31-7b38ad734b1a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a03bb2626525bf022b109105a7c6bc0dee23aea
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65407037"
---
# <a name="tutorial-azure-active-directory-integration-with-lockpath-keylight"></a>Esercitazione: Integrazione di Azure Active Directory con LockPath Keylight

Questa esercitazione descrive come integrare LockPath Keylight con Azure Active Directory (Azure AD).
L'integrazione di LockPath Keylight con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a LockPath Keylight.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a LockPath Keylight con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con LockPath Keylight, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di LockPath Keylight abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* LockPath Keylight supporta l'accesso SSO avviato da **SP**
* LockPath Keylight supporta il provisioning utenti **JIT**

## <a name="adding-lockpath-keylight-from-the-gallery"></a>Aggiunta di LockPath Keylight dalla raccolta

Per configurare l'integrazione di LockPath Keylight in Azure AD, è necessario aggiungere LockPath Keylight dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere LockPath Keylight dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **LockPath Keylight**, selezionare **LockPath Keylight** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![LockPath Keylight nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con LockPath Keylight in base a un utente test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in LockPath Keylight.

Per configurare e testare l'accesso Single Sign-On di Azure AD con LockPath Keylight, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di LockPath Keylight](#configure-lockpath-keylight-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di LockPath Keylight](#create-lockpath-keylight-test-user)**: per avere una controparte di Britta Simon in LockPath Keylight collegata alla relativa rappresentazione in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con LockPath Keylight, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione [LockPath Keylight](https://portal.azure.com/) del **portale di Azure** fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di LockPath Keylight](common/sp-identifier-reply.png)

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<company name>.keylightgrc.com/`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `https://<company name>.keylightgrc.com`

    c. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<company name>.keylightgrc.com/Login.aspx`

    > [!NOTE]
    > Poiché questi non sono i valori reali, Aggiornarli con l'URL di accesso, l'identificatore e l'URL di risposta effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di LockPath Keylight](https://www.lockpath.com/contact/). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **Certificato (base)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/certificateraw.png)

6. Nella sezione **Configura LockPath Keylight** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-lockpath-keylight-single-sign-on"></a>Configurare l'accesso Single Sign-On per LockPath Keylight

1. Per abilitare l'accesso Single Sign-On in LockPath Keylight, seguire questa procedura:

    a. Accedere al proprio account LockPath Keylight come amministratore.

    b. Nel menu in alto fare clic su **Person** (Persona) e selezionare **Keylight Setup** (Configurazione Keylight).

    ![Configure Single Sign-On](./media/keylight-tutorial/401.png)

    c. Nella visualizzazione albero a sinistra fare clic su **SAML**.

    ![Configure Single Sign-On](./media/keylight-tutorial/402.png)

    d. Nella finestra di dialogo **SAML Settings** (Impostazioni SAML) fare clic su **Edit** (Modifica).

    ![Configure Single Sign-On](./media/keylight-tutorial/404.png)

1. Nella pagina della finestra di dialogo **Edit SAML Settings (Modifica impostazioni SAML)** seguire questa procedura:

    ![Configure Single Sign-On](./media/keylight-tutorial/405.png)

    a. Impostare **SAML authentication** (Autenticazione SAML) su **Active** (Attiva).

    b. Nella casella di testo **Identity Provider Login URL** (URL di accesso del provider di identità) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    c. Nella casella di testo **Identity Provider Logout URL** (URL di disconnessione del provider di identità) incollare il valore di **URL di disconnessione** copiato dal portale di Azure.

    d. Fare clic su **Choose File** (Scegli file) per selezionare il certificato LockPath Keylight scaricato e quindi fare clic su **Open** (Apri) per caricare il certificato.

    e. Impostare **SAML User Id location** (Posizione ID utente SAML) su **NameIdentifier element of the subject statement** (Elemento NameIdentifier dell'istruzione Subject).

    f. Specificare il **provider del servizio Keylight** usando il modello seguente: `https://<CompanyName>.keylightgrc.com`.

    g. Impostare **Auto-provision users** (Utenti che eseguono il provisioning automatico) su **Active** (Attivo).

    h. Impostare **Auto-provision account type** (Tipo di account di provisioning automatico) su **Full User** (Utente completo).

    i. Impostare **Auto-provision security role** (Ruolo di sicurezza del provisioning automatico) selezionando **Standard User with SAML** (Utente standard con SAML).

    j. Impostare **Auto-provision security config** (Configurazione di sicurezza del provisioning automatico) selezionando **Standard User Configuration** (Configurazione utente standard).

    k. Nella casella di testo **Email attribute** (Attributo posta elettronica) digitare `https://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    l. Nella casella di testo **First name attribute** (Attributo nome) digitare `https://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    m. Nella casella di testo **Last name attribute** (Attributo cognome) digitare `https://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.

    n. Fare clic su **Save**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare `brittasimon@yourcompanydomain.extension`. Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a LockPath Keylight.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **LockPath Keylight**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **LockPath Keylight**.

    ![Collegamento di LockPath Keylight nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-lockpath-keylight-test-user"></a>Creare un utente di test di LockPath Keylight

In questa sezione viene creato un utente di nome Britta Simon in LockPath Keylight. LockPath Keylight supporta il provisioning utenti JIT, abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in LockPath Keylight, ne viene creato uno nuovo dopo l'autenticazione. Per creare un utente manualmente, è necessario contattare il [team di supporto clienti di LockPath Keylight](https://www.lockpath.com/contact/).

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di LockPath Keylight nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione LockPath Keylight per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)