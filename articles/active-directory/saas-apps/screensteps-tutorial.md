---
title: 'Esercitazione: Integrazione di Azure Active Directory con ScreenSteps | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e ScreenSteps.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4563fe94-a88f-4895-a07f-79df44889cf9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 864a9243a9f737506fd4d8cbc3940d7a86711f20
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67091655"
---
# <a name="tutorial-azure-active-directory-integration-with-screensteps"></a>Esercitazione: Integrazione di Azure Active Directory con ScreenSteps

Questa esercitazione descrive come integrare ScreenSteps con Azure Active Directory (Azure AD).
L'integrazione di ScreenSteps con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a ScreenSteps.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a ScreenSteps con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con ScreenSteps, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di ScreenSteps abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* ScreenSteps supporta l'accesso SSO avviato da **SP**

## <a name="adding-screensteps-from-the-gallery"></a>Aggiunta di ScreenSteps dalla raccolta

Per configurare l'integrazione di ScreenSteps in Azure AD, è necessario aggiungere ScreenSteps dalla raccolta al proprio elenco delle app SaaS gestite.

**Per aggiungere ScreenSteps dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **ScreenSteps**, selezionare **ScreenSteps** dal pannello dei risultati quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![ScreenSteps nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con ScreenSteps usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in ScreenSteps.

Per configurare e testare l'accesso Single Sign-On di Azure AD con ScreenSteps, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di ScreenSteps](#configure-screensteps-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di ScreenSteps](#create-screensteps-test-user)** : per avere una controparte di Britta Simon in ScreenSteps collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con ScreenSteps, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **ScreenSteps** del [portale di Azure](https://portal.azure.com/) fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni sull'accesso Single Sign-On per URL e dominio di ScreenSteps](common/sp-signonurl.png)

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<tenantname>.ScreenSteps.com`

    > [!NOTE]
    > Poiché non è reale, Aggiornare questo valore con l'URL di accesso effettivo, come illustrato più avanti nell'esercitazione.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura ScreenSteps** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-screensteps-single-sign-on"></a>Configurare l'accesso Single Sign-On di ScreenSteps

1. In un'altra finestra del Web browser accedere al sito aziendale di ScreenSteps come amministratore.

1. Click **Account Settings** (Impostazioni account).

    ![Gestione degli account](./media/screensteps-tutorial/ic778523.png "Gestione degli account")

1. Fare clic su **Single Sign-On**.

    ![Autenticazione remota](./media/screensteps-tutorial/ic778524.png "Autenticazione remota")

1. Fare clic su **Create Single Sign-on Endpoint** (Crea endpoint Single Sign-On).

    ![Autenticazione remota](./media/screensteps-tutorial/ic778525.png "Autenticazione remota")

1. Nella sezione **Create Single Sign-on Endpoint** (Crea endpoint Single Sign-On) seguire questa procedura:

    ![Creare un endpoint di autenticazione](./media/screensteps-tutorial/ic778526.png "Creare un endpoint di autenticazione")

    a. Nella casella di testo **Title** digitare un titolo.

    b. Nell'elenco **Mode** selezionare **SAML**.

    c. Fare clic su **Create**(Crea).

1. **Modificare** il nuovo endpoint.

    ![Modifica endpoint](./media/screensteps-tutorial/ic778528.png "Modifica endpoint")

1. Nella sezione **Edit Single Sign-on Endpoint** (Modifica endpoint Single Sign-On) seguire questa procedura:

    ![Endpoint di autenticazione remota](./media/screensteps-tutorial/ic778527.png "Endpoint di autenticazione remota")

    a. Fare clic su **Upload new SAML Certificate file** (Carica il nuovo file di certificato SAML) e quindi caricare il certificato scaricato dal portale di Azure.

    b. Incollare il valore di **URL di accesso**, copiato dal portale di Azure, nella casella di testo **Remote Login URL** (URL di accesso remoto).

    c. Incollare il valore di **URL di disconnessione** copiato dal portale di Azure nella casella di testo **Log out URL** (URL di disconnessione).

    d. Selezionare un **gruppo** a cui assegnare gli utenti quando viene effettuato il provisioning.

    e. Fare clic su **Update**.

    f. Copiare il valore di **SAML Consumer URL** (URL consumer SAML) negli Appunti e incollarlo nella casella di testo **URL di accesso** nella sezione **Configurazione SAML di base** nel portale di Azure.

    g. Tornare a **Edit Single Sign-on Endpoint** (Modifica endpoint Single Sign-On).

    h. Fare clic sul pulsante **Make default for account** (Rendi predefinito per account) per usare questo endpoint per tutti gli utenti che accedono a ScreenSteps. In alternativa, è possibile scegliere il pulsante **Add to Site** (Aggiungi a sito) per usare questo endpoint per siti specifici in **ScreenSteps**.

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

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure tramite la concessione dell'accesso a ScreenSteps.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **ScreenSteps**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni, selezionare **ScreenSteps**.

    ![Collegamento di ScreenSteps nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-screensteps-test-user"></a>Creare l'utente di test di ScreenSteps

In questa sezione viene creato un utente di nome Britta Simon in ScreenSteps. Collaborare con il  [team di supporto clienti di ScreenSteps](https://www.screensteps.com/contact) per aggiungere gli utenti nella piattaforma ScreenSteps. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di ScreenSteps nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione ScreenSteps per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)