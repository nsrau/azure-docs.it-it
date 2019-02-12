---
title: 'Esercitazione: Integrazione di Azure Active Directory con Appraisd | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Appraisd.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: db063306-4d0d-43ca-aae0-09f0426e7429
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.openlocfilehash: 44d4988ff68d5ccca575fc29ec3f0b7dce0bf948
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/06/2019
ms.locfileid: "55765433"
---
# <a name="tutorial-azure-active-directory-integration-with-appraisd"></a>Esercitazione: Integrazione di Azure Active Directory con Appraisd

Questa esercitazione descrive come integrare Appraisd con Azure Active Directory (Azure AD).
L'integrazione di Appraisd con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere ad Appraisd.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) ad Appraisd con gli account Azure AD.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Appraisd, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Appraisd abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Appraisd supporta l'accesso SSO avviato da **SP e IDP**

## <a name="adding-appraisd-from-the-gallery"></a>Aggiunta di Appraisd dalla raccolta

Per configurare l'integrazione di Appraisd in Azure AD, è necessario aggiungerla dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Appraisd dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Appraisd**, selezionare **Appraisd** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Appraisd nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Appraisd usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Appraisd.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Appraisd, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On per Appraisd ](#configure-appraisd-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di Appraisd](#create-appraisd-test-user)**: per avere una controparte di Britta Simon in Appraisd collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Appraisd, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Appraisd** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Appraisd](common/both-preintegrated-advanced-urls.png)

    a. Fare clic su **Impostare URL aggiuntivi**.

    b. Nella casella di testo **Stato dell'inoltro** digitare un URL: `<TENANTCODE>`

    c. Se si vuole configurare l'applicazione in modalità avviata da **SP** nella sezione **URL di accesso** digitare un URL usando il modello seguente: `https://app.appraisd.com/saml/<TENANTCODE>`

    > [!NOTE]
    > L'URL di accesso effettivo e il valore dello stato dell'inoltro vengono visualizzati nella pagina di configurazione SSO di Appraisd, descritta più avanti nell'esercitazione.

5. L'applicazione Appraisd prevede che le asserzioni SAML abbiano un formato specifico. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente** nella pagina di integrazione dell'applicazione. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Attributi utente**.

    ![image](common/edit-attribute.png)

6. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** modificare le attestazioni usando l'**icona Modifica** o aggiungere le attestazioni usando l'opzione **Aggiungi nuova attestazione** per configurare l'attributo del token SAML come mostrato nell'immagine precedente e seguire questa procedura:

    | Nome |  Attributo di origine|
    | ---------------| --------------- |
    | nameidentifier | user.mail |
    | | |

    a. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Lasciare vuota la casella **Spazio dei nomi**.

    d. Per Origine selezionare **Attributo**.

    e. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo indicato per la riga.

    f. Fare clic su **Ok**

    g. Fare clic su **Save**.

7. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/certificatebase64.png)

8. Nella sezione **Set up Appraisd** (Configura Appraisd) copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-appraisd-single-sign-on"></a>Configurare l'accesso Single Sign-On per Appraisd

1. In un'altra finestra del Web browser accedere ad Appraisd come amministratore della sicurezza.

2. Nell'angolo in alto a destra della pagina fare clic sull'icona **Settings** (Impostazioni) e quindi passare a **Configuration** (Configurazione).

    ![image](./media/appraisd-tutorial/tutorial_appraisd_sett.png)

3. Nella parte sinistra del menu fare clic su **SAML single sign-on** (Single Sign-On SAML).

    ![image](./media/appraisd-tutorial/tutorial_appraisd_single.png)

4. Nella pagina **SAML 2.0 Single Sign-On configuration** (Configurazione Single Sign-On SAML 2.0) seguire questa procedura:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_saml.png)

    a. Copiare il valore di **Default Relay State** (Stato di inoltro predefinito) e incollarlo nella casella di testo **Stato dell'inoltro** in **Configurazione SAML di base** nel portale di Azure.

    b. Copiare il valore di **Service-initiated login URL** (URL di accesso avviato dal servizio) e incollarlo nella casella di testo **URL di accesso** in **Configurazione SAML di base** nel portale di Azure.

5. Scorrere la stessa pagina verso il basso sotto **Identifying users** (Identificazione utenti) e seguire questa procedura:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_identifying.png)

    a. Nella casella di testo **Identity Provider Single Sign-On URL** (URL di accesso Single Sign-On del provider di identità) incollare il valore di **URL di accesso** copiato dal portale di Azure e fare clic su **Salva**.

    b. Nella casella di testo **Identity Provider Issuer URL** (URL autorità di certificazione provider di identità) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure e fare clic su **Salva**.

    c. Nel Blocco note aprire il certificato con codifica Base 64 scaricato dal portale di Azure, copiarne il contenuto e quindi incollarlo nella casella **X.509 Certificate** (Certificato X.509) e fare clic su **Save** (Salva).

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

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad Appraisd.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Appraisd**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Appraisd**.

    ![Collegamento di Appraisd nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-appraisd-test-user"></a>Creare l'utente di test di Appraisd

Per consentire agli utenti di Azure AD di accedere ad Appraisd, è necessario effettuarne il provisioning in Appraisd. Nel caso di Appraisd il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere ad Appraisd come amministratore della sicurezza.

2. Nell'angolo in alto a destra della pagina fare clic sull'icona **Settings** (Impostazioni), quindi passare ad **Administration centre** (Centro di amministrazione).

    ![image](./media/appraisd-tutorial/tutorial_appraisd_admin.png)

3. Nella barra degli strumenti nella parte superiore della pagina fare clic su **People** (Persone) e quindi passare ad **Add a new user** (Aggiungi un nuovo utente).

    ![image](./media/appraisd-tutorial/tutorial_appraisd_user.png)

4. Nella pagina **Add a New User** (Aggiungi un nuovo utente) seguire questa procedura:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_newuser.png)

    a. Nella casella di testo **First name** (Nome) immettere il nome dell'utente, ad esempio **Britta**.

    b. Nella casella di testo **Last name** (Cognome) immettere il cognome dell'utente, ad esempio **Simon**.

    c. Nella casella di testo **Email** (Posta elettronica) immettere l'indirizzo di posta elettronica dell'utente, ad esempio **Brittasimon@contoso.com**.

    d. Fare clic su **Add User**.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Appraisd nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Appraisd per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
