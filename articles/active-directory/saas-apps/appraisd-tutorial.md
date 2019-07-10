---
title: 'Esercitazione: Integrazione di Azure Active Directory con Appraisd | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Appraisd.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: db063306-4d0d-43ca-aae0-09f0426e7429
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08453928ab000cf906c451fa6c1cd619a00ee4ca
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561193"
---
# <a name="tutorial-integrate-appraisd-with-azure-active-directory"></a>Esercitazione: Integrare Appraisd con Azure Active Directory

Questa esercitazione descrive come integrare Appraisd con Azure Active Directory (Azure AD). Integrando Appraisd con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere ad Appraisd.
* Abilitare gli utenti per l'accesso automatico ad Appraisd con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Appraisd abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Appraisd supporta l'accesso SSO avviato da **SP e IDP**.

## <a name="adding-appraisd-from-the-gallery"></a>Aggiunta di Appraisd dalla raccolta

Per configurare l'integrazione di Appraisd in Azure AD, è necessario aggiungerla dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Appraisd** nella casella di ricerca.
1. Selezionare **Appraisd** nel riquadro dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

Configurare e testare l'accesso Single Sign-On di Azure AD con Appraisd usando un utente di test di nome **B. Simon**. Per il corretto funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Appraisd.

Per configurare e testare l'accesso SSO di Azure AD con Appraisd, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** per consentire agli utenti di usare questa funzionalità.
2. **[Configurare Appraisd](#configure-appraisd)** per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente test di Azure AD](#create-an-azure-ad-test-user)** per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di Appraisd](#create-appraisd-test-user)** : per avere una controparte di B. Simon in Appraisd collegata alla relativa rappresentazione in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-sso)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Appraisd** del [portale di Azure](https://portal.azure.com/) trovare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** l'applicazione è preconfigurata e gli URL necessari sono già prepopolati con Azure. L'utente deve salvare la configurazione facendo clic sul pulsante Salva ed eseguire la procedura seguente:

    a. Fare clic su **Impostare URL aggiuntivi**.

    b. Nella casella di testo **Stato dell'inoltro** digitare un URL: `<TENANTCODE>`

    c. Se si vuole configurare l'applicazione in modalità avviata da **SP** nella sezione **URL di accesso** digitare un URL usando il modello seguente: `https://app.appraisd.com/saml/<TENANTCODE>`

    > [!NOTE]
    > L'URL di accesso effettivo e il valore dello stato dell'inoltro vengono visualizzati nella pagina di configurazione SSO di Appraisd, descritta più avanti nell'esercitazione.

1. L'applicazione Appraisd prevede un formato specifico per le asserzioni SAML, in base al quale è necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi token SAML. La schermata seguente illustra l'elenco degli attributi predefiniti in cui **nameidentifier** è associato a **user.userprincipalname**. L'applicazione Appraisd prevede che **nameidentifier** sia associato a **user.mail**, di conseguenza è necessario modificare il mapping dell'attributo. A questo scopo, fare clic sull'icona **Modifica** e modificare il mapping.

    ![image](common/edit-attribute.png)

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML**, trovare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer in uso.

   ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura Appraisd** copiare gli URL appropriati in base alle esigenze.

   ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="configure-appraisd"></a>Configurare Appraisd

1. Per automatizzare la configurazione all'interno di Appraisd, è necessario installare l'**estensione del browser per l'accesso sicuro alle app personali** facendo clic su **Installa estensione**.

    ![Estensione My Apps](common/install-myappssecure-extension.png)

2. Dopo aver aggiunto l'estensione al browser, fare clic su **Configura Appraisd** per passare direttamente all'applicazione Appraisd. Da qui, fornire le credenziali di amministratore per accedere ad Appraisd. L'estensione del browser configurerà automaticamente l'applicazione per l'utente e automatizzerà i passaggi da 3 a 7.

    ![Eseguire la configurazione](common/setup-sso.png)

3. Se si vuole configurare manualmente Appraisd, aprire un'altra finestra del Web browser, accedere al sito aziendale di Appraisd come amministratore e seguire questa procedura:

4. Nell'angolo in alto a destra della pagina fare clic sull'icona **Settings** (Impostazioni), quindi passare a **Configuration** (Configurazione).

    ![image](./media/appraisd-tutorial/tutorial_appraisd_sett.png)

5. Nella parte sinistra del menu fare clic su **SAML single sign-on** (Single Sign-On SAML).

    ![image](./media/appraisd-tutorial/tutorial_appraisd_single.png)

6. Nella pagina **SAML 2.0 Single Sign-On configuration** (Configurazione Single Sign-On SAML 2.0) seguire questa procedura:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_saml.png)

    a. Copiare il valore di **Default Relay State** (Stato di inoltro predefinito) e incollarlo nella casella di testo **Stato dell'inoltro** in **Configurazione SAML di base** nel portale di Azure.

    b. Copiare il valore di **Service-initiated login URL** (URL di accesso avviato dal servizio) e incollarlo nella casella di testo **URL di accesso** in **Configurazione SAML di base** nel portale di Azure.

7. Scorrere la stessa pagina verso il basso sotto **Identifying users** (Identificazione utenti) e seguire questa procedura:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_identifying.png)

    a. Nella casella di testo **Identity Provider Single Sign-On URL** (URL di accesso Single Sign-On del provider di identità) incollare il valore di **URL di accesso** copiato dal portale di Azure e fare clic su **Salva**.

    b. Nella casella di testo **Identity Provider Issuer URL** (URL autorità di certificazione provider di identità) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure e fare clic su **Salva**.

    c. Nel Blocco note aprire il certificato con codifica Base 64 scaricato dal portale di Azure, copiarne il contenuto e quindi incollarlo nella casella **X.509 Certificate** (Certificato X.509) e fare clic su **Save** (Salva).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

In questa sezione verrà creato un utente di test di nome B. Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B. Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B. Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione B. Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad Appraisd.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Appraisd**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B. Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-appraisd-test-user"></a>Creare l'utente di test di Appraisd

Per consentire agli utenti di Azure AD di accedere ad Appraisd, è necessario effettuarne il provisioning in Appraisd. Nel caso di Appraisd il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere ad Appraisd come amministratore della sicurezza.

2. Nell'angolo in alto a destra della pagina fare clic sull'icona **Settings** (Impostazioni), quindi passare ad **Administration centre** (Centro di amministrazione).

    ![image](./media/appraisd-tutorial/tutorial_appraisd_admin.png)

3. Nella barra degli strumenti nella parte superiore della pagina fare clic su **People** (Persone), quindi passare ad **Add a new user** (Aggiungi un nuovo utente).

    ![image](./media/appraisd-tutorial/tutorial_appraisd_user.png)

4. Nella pagina **Add a New User** (Aggiungi un nuovo utente) seguire questa procedura:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_newuser.png)

    a. Nella casella di testo **First name** (Nome) immettere il nome dell'utente, ad esempio **Britta**.

    b. Nella casella di testo **Last name** (Cognome) immettere il cognome dell'utente, ad esempio **Simon**.

    c. Nella casella di testo **E-mail** (Posta elettronica) immettere l'indirizzo di posta elettronica dell'utente, ad esempio `B. Simon@contoso.com`.

    d. Fare clic su **Add User**.

### <a name="test-sso"></a>Testare l'accesso SSO

Quando si seleziona il riquadro di Appraisd nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Appraisd per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
