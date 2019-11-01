---
title: 'Esercitazione: Integrazione di Azure Active Directory con Cezanne HR Software | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Cezanne HR Software.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 62b42e15-c282-492d-823a-a7c1c539f2cc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa689e6138f8d965e59f7cfa7a85e0835301086c
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73158706"
---
# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>Esercitazione: Integrazione di Azure Active Directory con Cezanne HR Software

Questa esercitazione descrive come integrare Cezanne HR Software con Azure Active Directory (Azure AD).
L'integrazione di Cezanne HR Software con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Cezanne HR Software.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Cezanne HR Software con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Cezanne HR Software, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Cezanne HR Software abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Cezanne HR Software supporta l'accesso SSO avviato da **SP**

## <a name="adding-cezanne-hr-software-from-the-gallery"></a>Aggiunta di Cezanne HR Software dalla raccolta

Per configurare l'integrazione di Cezanne HR Software in Azure AD, è necessario aggiungere Cezanne HR Software dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Cezanne HR Software dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Cezanne HR Software**, selezionare **Cezanne HR Software** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Cezanne HR Software nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Cezanne HR Software usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Cezanne HR Software.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Cezanne HR Software, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On per Cezanne HR Software](#configure-cezanne-hr-software-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di Cezanne HR Software](#create-cezanne-hr-software-test-user)** : per avere una controparte di Britta Simon in Cezanne HR Software collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Cezanne HR Software, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Cezanne HR Software** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Cezanne HR Software](common/sp-identifier-reply.png)

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://w3.cezanneondemand.com/CezanneOnDemand/-/<tenantidentifier>`

    b. Nella casella di testo **Identificatore (ID entità)** digitare l'URL: `https://w3.cezanneondemand.com/CezanneOnDemand/`

    c. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://w3.cezanneondemand.com:443/cezanneondemand/-/<tenantidentifier>/Saml/samlp`
    
    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'URL di accesso e l'URL di risposta effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Cezanne HR Software](https://cezannehr.com/services/support/).

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura Cezanne HR Software** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-cezanne-hr-software-single-sign-on"></a>Configurare l'accesso Single Sign-On per Cezanne HR Software

1. In un'altra finestra del browser Web accedere al tenant Cezanne HR Software come amministratore.

2. Nella barra di spostamento a sinistra fare clic su **System Setup**(Configurazione di sistema). Passare a **Security Settings**(Impostazioni di sicurezza). Passare quindi a **Single Sign-On Configuration**(Configurazione Single Sign-On).

    ![Configurazione accesso Single Sign-On sul lato app](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

3. Nel pannello **Allow users to log in using the following Single Sign-On (SSO) Service** (Consenti agli utenti di accedere usando il servizio Single Sign-On (SSO) seguente) selezionare la casella **SAML 2.0** e quindi l'opzione **Advanced Configuration** (Configurazione avanzata).

    ![Configurazione accesso Single Sign-On sul lato app](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

4. Fare clic sul pulsante **Add New** (Aggiungi nuovo).

    ![Configurazione accesso Single Sign-On sul lato app](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

5. Seguire questa procedura nella sezione **SAML 2.0 IDENTITY PROVIDERS** (PROVIDER DI IDENTITÀ SAML 2.0).

    ![Configurazione accesso Single Sign-On sul lato app](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)

    a. Immettere il nome del provider di identità in **Display Name**(Nome visualizzato).

    b. Nella casella di testo **Entity Identifier** (Identificatore entità) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    c. Modificare il valore **SAML Binding** (Associazione SAML) in 'POST'.

    d. Nella casella di testo **Security Token Service Endpoint** (Endpoint servizio token di sicurezza) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    e. Nella casella di testo User ID Attribute Name (Nome attributo ID utente) inserire `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    f. Fare clic sull'icona **Upload** (Carica) per caricare il certificato scaricato dal portale di Azure.

    g. Fare clic sul pulsante **OK** .

6. Fare clic sul pulsante **Salva** .

    ![Configurazione accesso Single Sign-On sul lato app](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

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

    d. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a Cezanne HR Software.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Cezanne HR Software**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **Cezanne HR Software**.

    ![Nell'elenco di applicazioni selezionare Cezanne HR Software](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-cezanne-hr-software-test-user"></a>Creare l'utente di test di Cezanne HR Software

Per consentire agli utenti di Azure AD di accedere a Cezanne HR Software, è necessario eseguirne il provisioning in Cezanne HR Software. Nel caso di Cezanne HR Software, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di Cezanne HR Software come amministratore.

2. Nella barra di spostamento a sinistra fare clic su **System Setup**(Configurazione di sistema). Passare a **Gestisci utenti**. Passare quindi ad **Add New User**(Aggiungi nuovo utente).

    ![New User](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "Nuovo utente")

3. Nella sezione **PERSON DETAILS** (DETTAGLI PERSONA) seguire questa procedura:

    ![New User](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "Nuovo utente")

    a. Impostare **Internal User** (Utente interno) su OFF.

    b. Digitare il nome dell'utente, ad esempio **Britta**, nella casella di testo **First Name** (Nome).  

    c. Digitare il nome dell'utente, ad esempio **Simon**, nella casella di testo **Last Name** (Cognome).

    d. Nella casella di testo **Email** digitare l'indirizzo e-mail dell'utente, ad esempio Brittasimon@contoso.com.

4. Nella sezione **Account Information** (Informazioni account) seguire questa procedura:

    ![New User](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "Nuovo utente")

    a. Nella casella di testo **Username** (Nome utente) digitare l'indirizzo di posta elettronica di un utente, ad esempio Brittasimon@contoso.com.

    b. Nella casella di testo **Password** digitare la password dell'utente.

    c. Selezionare **HR Professional** (Professionista HR) come **Security Role** (Ruolo di sicurezza).

    d. Fare clic su **OK**.

5. Passare alla scheda **Single Sign-On** e selezionare **Add New** (Aggiungi nuovo) nell'area **SAML 2.0 Identifiers** (Identificatori SAML 2.0).

    ![Utente](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "Utente")

6. Scegliere il provider di identità per **Identity Provider** (Provider di identità) e nella casella di testo **User Identifier** (Identificatore utente) immettere l'indirizzo di posta elettronica dell'account Britta Simon.

    ![Utente](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "Utente")

7. Fare clic sul pulsante **Salva** .

    ![Utente](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "Utente")

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Cezanne HR Software nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Cezanne HR Software per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
