---
title: 'Esercitazione: Integrazione di Azure Active Directory con InstaVR Viewer | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e InstaVR Viewer.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 13ffa29f-d0a5-4b21-b296-cfd76f380940
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8acc835a7f18ee673f0857f65d49eed59638a6d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57872784"
---
# <a name="tutorial-azure-active-directory-integration-with-instavr-viewer"></a>Esercitazione: Integrazione di Azure Active Directory con InstaVR Viewer

Questa esercitazione descrive come integrare InstaVR Viewer con Azure Active Directory (Azure AD).
L'integrazione di InstaVR Viewer con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a InstaVR Viewer.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a InstaVR Viewer con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con InstaVR Viewer sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di InstaVR Viewer abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* InstaVR Viewer supporta l'accesso SSO avviato da **SP**
* InstaVR Viewer supporta solo il provisioning **Just In Time** dell'utente

## <a name="adding-instavr-viewer-from-the-gallery"></a>Aggiunta di InstaVR Viewer dalla raccolta

Per configurare l'integrazione di InstaVR Viewer in Azure AD, è necessario aggiungere InstaVR Viewer dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere InstaVR Viewer dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **InstaVR Viewer**, selezionare **InstaVR Viewer** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![InstaVR Viewer nell'elenco dei risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con InstaVR Viewer con un utente test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in InstaVR Viewer.

Per configurare e testare l'accesso Single Sign-On di Azure AD con InstaVR Viewer, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di InstaVR Viewer](#configure-instavr-viewer-single-sign-on)**: per configurare le impostazioni dell'accesso Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di InstaVR Viewer](#create-instavr-viewer-test-user)**: per avere una controparte di Britta Simon in InstaVR Viewer collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con InstaVR Viewer, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **InstaVR Viewer** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di InstaVR Viewer](common/sp-identifier.png)

    a. Nella casella di testo **URL di accesso** digitare un URL usando il modello seguente: `https://console.instavr.co/auth/saml/login/<WEBPackagedURL>`

    > [!NOTE]
    > L'URL di accesso non ha un criterio fisso. Viene generato quando il cliente di InstaVR Viewer crea il pacchetto Web. È univoco per ogni cliente e pacchetto. Per ottenere l'URL di accesso esatto è necessario eseguire l'accesso all'istanza di InstaVR Viewer e creare il pacchetto Web.

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL usando il modello seguente: `https://console.instavr.co/auth/saml/sp/<WEBPackagedURL>`

    > [!NOTE]
    > Il valore dell'identificatore non è reale. Aggiornare questo valore con il valore effettivo dell'identificatore, come illustrato più avanti nell'esercitazione.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** e il file **XML metadati federazione** dalle opzioni specificate in base ai propri requisiti e salvarli nel computer in uso.

    ![Collegamento di download del certificato](common/metadata-certificatebase64.png)

6. Nella sezione **Configura InstaVR Viewer** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-instavr-viewer-single-sign-on"></a>Configurare l'accesso Single Sign-On di InstaVR Viewer

1. Aprire una nuova finestra del Web browser e accedere al sito aziendale di InstaVR Viewer come amministratore.

2. Fare clic sull'**icona dell'utente** e selezionare **Account**.

    ![Configurazione di InstaVR Viewer](media/instavr-viewer-tutorial/tutorial-instavr-viewer-account.png)

3. Scorrere verso il basso fino a **SAML Auth** (Autenticazione SAML) e seguire questa procedura:

    ![Configurazione di InstaVR Viewer](media/instavr-viewer-tutorial/tutorial-instavr-viewer-configure.png)

    a. Nella casella di testo **SSO URL** (URL SSO) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    b. Nella casella di testo **Logout URL** (URL disconnessione) incollare il valore di **URL disconnessione** copiato dal portale di Azure.

    c. Nella casella di testo **Entity ID** (ID entità) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    d. Per caricare il file del certificato scaricato, fare clic su **Update** (Aggiorna).

    e. Per caricare il file dei metadati di federazione scaricato, fare clic su **Update** (Aggiorna).

    f. Copiare il valore di **Entity ID** (ID entità) e incollarlo nella casella di testo **Identifier (Entity ID)** (Identificatore - ID entità) nella sezione **Configurazione SAML di base** del portale di Azure.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon\@dominioaziendale.estensione**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a InstaVR Viewer.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **InstaVR Viewer**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni digitare e selezionare **InstaVR Viewer**.

    ![Collegamento di InstaVR Viewer nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-instavr-viewer-test-user"></a>Creare un utente test di InstaVR Viewer

In questa sezione viene creato un utente di nome Britta Simon in InstaVR Viewer. InstaVR Viewer supporta il provisioning JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in InstaVR Viewer, ne viene creato uno nuovo dopo l'autenticazione. In caso di problemi, contattare il [team di supporto di InstaVR Viewer](mailto:contact@instavr.co).

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

1. Aprire una nuova finestra del Web browser e accedere al sito aziendale di InstaVR Viewer come amministratore.

2. Selezionare **Package** (Pacchetto) nel pannello di spostamento a sinistra e selezionare **Make package for Web** (Crea pacchetto Web).

    ![Configurazione di InstaVR Viewer](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing1.png)

3. Selezionare **Download**.

    ![Configurazione di InstaVR Viewer](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing2.png)

4. Selezionare **Open Hosted Page** (Apri pagina ospitata); si verrà reindirizzati ad Azure AD per l'accesso.

    ![Configurazione di InstaVR Viewer](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing3.png)

5. Immettere le credenziali di Azure AD per accedere ad Azure AD tramite SSO.

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
