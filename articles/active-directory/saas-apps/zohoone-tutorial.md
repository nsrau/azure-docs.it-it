---
title: 'Esercitazione: Integrazione di Azure Active Directory con Zoho One | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Zoho One.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bbc3038c-0d8b-45dd-9645-368bd3d01a0f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: c731919baf3acc8cedfb31c088f9a0a12791251c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64717982"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho-one"></a>Esercitazione: Integrazione di Azure Active Directory con Zoho One

Questa esercitazione descrive come integrare Zoho One con Azure Active Directory (Azure AD).
L'integrazione di Zoho One con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Zoho One.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Zoho One con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Zoho One, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Zoho One abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Zoho One supporta l'accesso SSO avviato da **SP** e **IDP**

## <a name="adding-zoho-one-from-the-gallery"></a>Aggiunta di Zoho One dalla raccolta

Per configurare l'integrazione di Zoho One in Azure AD, è necessario aggiungere Zoho One dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Zoho One dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Zoho One**, selezionare **Zoho One** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Zoho One nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Zoho One usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Zoho One.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Zoho One, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Zoho One](#configure-zoho-one-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l' di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di Zoho One](#create-zoho-one-test-user)** : per avere una controparte di Britta Simon in Zoho One collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Zoho One, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Zoho One** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Zoho One](common/idp-relay.png)

    a. Nella casella di testo **Identificatore** digitare un URL: `one.zoho.com`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://accounts.zoho.com/samlresponse/<saml-identifier>`

    > [!NOTE]
    > Il valore di **URL di risposta** precedente non è reale. Si otterrà il valore `<saml-identifier>` dal passaggio4 della sezione **Configurare l'accesso Single Sign-On di Zoho One**, descritto più avanti nell'esercitazione.

    c. Fare clic su **Impostare URL aggiuntivi**.

    d. Nella casella di testo **Stato dell'inoltro** digitare un URL: `https://one.zoho.com`

5. Per configurare l'applicazione in modalità avviata da **SP**, seguire questa procedura:


    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Zoho One](common/both-signonurl.png)

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com` 

    > [!NOTE] 
    > Il valore precedente dell'**URL di accesso** non è reale. Aggiornare il valore con l'URL di accesso effettivo ottenuto nella sezione **Configurare l'accesso Single Sign-On di Zoho One**, descritta più avanti nell'esercitazione. 

6. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

7. Nella sezione **Configura Zoho One** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-zoho-one-single-sign-on"></a>Configurare l'accesso Single Sign-On di Zoho One

1. In un'altra finestra del Web browser accedere al sito aziendale di Zoho One come amministratore.

2. Nella scheda **Organizzazione**, fare clic su **Installazione** nella sezione **Autenticazione SAML**.

    ![Organizzazione Zoho One](./media/zohoone-tutorial/tutorial_zohoone_setup.png)

3. Nella pagina popup eseguire i passaggi seguenti:

    ![Accesso Zoho One](./media/zohoone-tutorial/tutorial_zohoone_save.png)

    a. Nella casella di testo **Sign-in URL** (URL di accesso) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    b. Nella casella di testo **Sign-out URL** (URL di disconnessione) incollare il valore dell'**URL di disconnessione** copiato dal portale di Azure.

    c. Fare clic su **Sfoglia** per caricare il **Certificato (Base64)** scaricato dal portale di Azure.

    d. Fare clic su **Save**.

4. Dopo il salvataggio della configurazione dell'autenticazione SAML, copiare il valore dell'**identificatore SAML** e aggiungerlo all'**URL di risposta** al posto di `<saml-identifier>`, ad esempio `https://accounts.zoho.com/samlresponse/one.zoho.com`, quindi incollare il valore generato nella casella di testo **URL di risposta** nella sezione **Configurazione SAML di base**.

    ![SAML Zoho One](./media/zohoone-tutorial/tutorial_zohoone_samlidenti.png)

5. Passare alla scheda **Domini** e quindi fare clic su **Aggiungi dominio**.

    ![Dominio Zoho One](./media/zohoone-tutorial/tutorial_zohoone_domain.png)

6. Nella pagina **Aggiungi dominio** seguire la procedura seguente:

    ![Aggiungi dominio Zoho One](./media/zohoone-tutorial/tutorial_zohoone_adddomain.png)

    a. Nella casella di testo **Nome di dominio** digitare il dominio, ad esempio contoso.com.

    b. Fare clic su **Aggiungi**.

    >[!Note]
    >Dopo aver aggiunto il dominio seguire [questi](https://www.zoho.com/one/help/admin-guide/domain-verification.html) passaggi per verificarlo. Dopo la verifica del dominio, usare il nome di dominio in **URL di accesso** nella sezione **Configurazione SAML di base** del portale di Azure.

### <a name="create-an-azure-ad-test-user"></a>Creare un  di Azure AD 

Questa sezione descrive come creare un  denominato Britta Simon nel portale di Azure.

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

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l' di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Zoho One.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Zoho One**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Zoho One**.

    ![Collegamento di Zoho One nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-zoho-one-test-user"></a>Creare l'utente di test di Zoho One

Per consentire agli utenti di Azure AD di accedere a Zoho One, è necessario effettuarne il provisioning in Zoho One. Nel caso di Zoho One il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere a Zoho One come amministratore della sicurezza.

2. Nella scheda **Utenti**, fare clic su **logo utente**.

    ![Utente Zoho One](./media/zohoone-tutorial/tutorial_zohoone_users.png)

3. Nella pagina **Aggiungi utente** eseguire la procedura seguente:

    ![Aggiungi Utente Zoho One](./media/zohoone-tutorial/tutorial_zohoone_adduser.png)
    
    a. Nella casella di testo **Nome** digitare il nome dell'utente, ad esempio **Britta Simon**.
    
    b. Nella casella di testo **Email address** (Indirizzo e-mail) immettere l'indirizzo di posta elettronica dell'utente, ad esempio brittasimon@contoso.com.

    >[!Note]
    >Selezionare il dominio verificato dall'elenco di domini.

    c. Fare clic su **Aggiungi**.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Zoho One nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Zoho One per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

