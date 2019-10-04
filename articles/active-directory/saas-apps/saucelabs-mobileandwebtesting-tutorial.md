---
title: 'Esercitazione: integrazione di Azure Active Directory con Sauce Labs - Mobile and Web Testing | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Sauce Labs - Mobile and Web Testing.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3142d947-70e5-4345-8a30-b92d8715fac9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: 8933cb90672e49305cd0fb7dc5e4f8f04f94093e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67091564"
---
# <a name="tutorial-azure-active-directory-integration-with-sauce-labs---mobile-and-web-testing"></a>Esercitazione: integrazione di Azure Active Directory con Sauce Labs - Mobile and Web Testing

Questa esercitazione descrive come integrare Sauce Labs - Mobile and Web Testing con Azure Active Directory (Azure AD).
L'integrazione di Sauce Labs - Mobile and Web Testing con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Sauce Labs - Mobile and Web Testing.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Sauce Labs - Mobile and Web Testing con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Sauce Labs - Mobile and Web Testing, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Una sottoscrizione di Sauce Labs - Mobile and Web Testing abilitata per l'accesso Single Sign-On.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Sauce Labs - Mobile and Web Testing supporta l'accesso Single Sign-On avviato da **IDP**
* Sauce Labs - Mobile and Web Testing supporta il provisioning utenti **Just-In-Time** (JIT)

## <a name="adding-sauce-labs---mobile-and-web-testing-from-the-gallery"></a>Aggiunta di Sauce Labs - Mobile and Web Testing dalla raccolta

Per configurare l'integrazione di Sauce Labs - Mobile and Web Testing in Azure AD, è necessario aggiungere Sauce Labs - Mobile and Web Testing dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Sauce Labs - Mobile and Web Testing dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca, digitare **Sauce Labs - Mobile and Web Testing**, selezionare **Sauce Labs - Mobile and Web Testing** nel pannello dei risultati, quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Sauce Labs - Mobile and Web Testing nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Sauce Labs - Mobile and Web Testing usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Sauce Labs - Mobile and Web Testing.

Per configurare e testare l'accesso Single Sign-On di Azure AD conSauce Labs - Mobile and Web Testing, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On per Sauce Labs - Mobile and Web Testing](#configure-sauce-labs---mobile-and-web-testing-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Sauce Labs - Mobile and Web Testing](#create-sauce-labs---mobile-and-web-testing-test-user)** : per avere una controparte di Britta Simon in Sauce Labs - Mobile and Web Testing collegata alla rappresentazione di Azure AD dell'utente.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Sauce Labs - Mobile and Web Testing, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Sauce Labs - Mobile and Web Testing** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** l'utente non deve eseguire alcuna operazione perché l'app è già preintegrata in Azure.

    ![Informazioni sull'accesso Single Sign-On agli URL e al dominio di Sauce Labs - Mobile and Web Testing](common/preintegrated.png)

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

6. Nella sezione **Configura Sauce Labs - Mobile and Web Testing** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-sauce-labs---mobile-and-web-testing-single-sign-on"></a>Configurare l'accesso Single Sign-On per Sauce Labs - Mobile and Web Testing

1. In un'altra finestra del Web browser, accedere al sito aziendale di Sauce Labs - Mobile and Web Testing come amministratore.

2. Fare clic su **Icona utente** e selezionare la scheda **Gestione team**.

    ![Configure Single Sign-On](./media/saucelabs-mobileandwebtesting-tutorial/configure1.png)

3. Immettere il **Nome di dominio** nella casella di testo.

    ![Configure Single Sign-On](./media/saucelabs-mobileandwebtesting-tutorial/configure2.png)

4. Fare clic sulla scheda **Configura**.

    ![Configure Single Sign-On](./media/saucelabs-mobileandwebtesting-tutorial/configure3.png)

5. Nella sezione **Single Sign-On**, eseguire la procedura seguente.

    ![Configure Single Sign-On](./media/saucelabs-mobileandwebtesting-tutorial/configure4.png)

    a. Fare clic su **Sfoglia** e caricare il file di metadati scaricato da Azure AD.

    b. Selezionare la casella di controllo **CONSENTI IL PROVISIONING JIT**.

    c. Fare clic su **Save**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD 

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare `brittasimon@yourcompanydomain.extension`.  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Sauce Labs - Mobile and Web Testing.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Sauce Labs - Mobile and Web Testing**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni, selezionare **Sauce Labs - Mobile and Web Testing**.

    ![Collegamento di Sauce Labs - Mobile and Web Testing nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-sauce-labs---mobile-and-web-testing-test-user"></a>Creare l'utente di test di Sauce Labs - Mobile and Web Testing

In questa sezione viene creato un utente di nome Britta Simon in Sauce Labs - Mobile and Web Testing. Sauce Labs - Mobile and Web Testing supporta il provisioning utenti Just-In-Time (JIT), che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in Sauce Labs - Mobile and Web Testing, ne viene creato uno nuovo dopo l'autenticazione.

> [!Note]
> Se è necessario creare un utente manualmente, contattare il  [team di supporto di Sauce Labs - Mobile and Web Testing](mailto:support@saucelabs.com).

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Sauce Labs - Mobile and Web Testing nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Sauce Labs - Mobile and Web Testing per cui si è configurato l'accesso Single Sign-On. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

