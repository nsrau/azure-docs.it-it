---
title: 'Esercitazione: Integrazione di Azure Active Directory con Kantega SSO for Bitbucket | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Kantega SSO for Bitbucket.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c41cdaaf-0441-493c-94c7-569615b7b1ab
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 4f0b6dc725fbce2140ede98e7924730c4426e084
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64727498"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-bitbucket"></a>Esercitazione: Integrazione di Azure Active Directory con Kantega SSO for Bitbucket

Questa esercitazione descrive come integrare Kantega SSO for Bitbucket con Azure Active Directory (Azure AD).
L'integrazione di Kantega SSO for Bitbucket con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Kantega SSO for Bitbucket.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Kantega SSO for Bitbucket con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Kantega SSO for Bitbucket, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Kantega SSO for Bitbucket abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Kantega SSO for Bitbucket supporta l'accesso SSO avviato da **SP e IDP**

## <a name="adding-kantega-sso-for-bitbucket-from-the-gallery"></a>Aggiunta di Kantega SSO for Bitbucket dalla raccolta

Per configurare l'integrazione di Kantega SSO for Bitbucket in Azure AD, è necessario aggiungere Kantega SSO for Bitbucket dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Kantega SSO for Bitbucket dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Kantega SSO for Bitbucket**, selezionare **Kantega SSO for Bitbucket** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Kantega SSO for Bitbucket nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Kantega SSO for Bitbucket usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Kantega SSO for Bitbucket.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Kantega SSO for Bitbucket, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Kantega SSO for Bitbucket](#configure-kantega-sso-for-bitbucket-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Kantega SSO for Bitbucket](#create-kantega-sso-for-bitbucket-test-user)** : per avere una controparte di Britta Simon in Kantega SSO for Bitbucket collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Kantega SSO for Bitbucket, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Kantega SSO for Bitbucket** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Kantega SSO for Bitbucket](common/idp-intiated.png)

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Kantega SSO for Bitbucket](common/metadata-upload-additional-signon.png)

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Questi valori vengono ricevuti durante la configurazione del plug-in Bitbucket descritta più avanti nell'esercitazione.

6. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

7. Nella sezione **Configura Kantega SSO for Bitbucket** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-kantega-sso-for-bitbucket-single-sign-on"></a>Configurare l'accesso Single Sign-On di Kantega SSO for Bitbucket

1. In un'altra finestra del Web browser accedere al portale di amministrazione di Bitbucket come amministratore.

1. Fare clic sull'ingranaggio e scegliere **Find new add-ons** (Trova nuovi componenti aggiuntivi).

    ![Configure Single Sign-On](./media/kantegassoforbitbucket-tutorial/addon1.png)

1. Cercare **Kantega SSO for Bitbucket SAML & Kerberos** e fare clic su **Install** (Installa) per installare il nuovo plug-in di SAML.

    ![Configure Single Sign-On](./media/kantegassoforbitbucket-tutorial/addon2.png)

1. Viene avviata l'installazione del plug-in.

    ![Configure Single Sign-On](./media/kantegassoforbitbucket-tutorial/addon31.png)

1. Al termine dell'installazione, Fare clic su **Close**.

    ![Configure Single Sign-On](./media/kantegassoforbitbucket-tutorial/addon33.png)

1. Fare clic su **Manage**.

    ![Configure Single Sign-On](./media/kantegassoforbitbucket-tutorial/addon34.png)

1. Fare clic su **Configure** (Configura) per configurare il nuovo plug-in.

    ![Configure Single Sign-On](./media/kantegassoforbitbucket-tutorial/addon35.png)

1. Nella sezione **SAML** Selezionare **Azure Active Directory (Azure AD)** dall'elenco a discesa **Add identity provider** (Aggiungi provider di identità).

    ![Configure Single Sign-On](./media/kantegassoforbitbucket-tutorial/addon4.png)

1. Selezionare il livello di sottoscrizione **Basic** (Di base).

    ![Configure Single Sign-On](./media/kantegassoforbitbucket-tutorial/addon5.png)

1. Nella sezione **App properties** (Proprietà app) seguire questa procedura:

    ![Configure Single Sign-On](./media/kantegassoforbitbucket-tutorial/addon6.png)

    a. Copiare il valore di **App ID URI** (URI ID App) e usarlo come valore di **Identificatore, URL di risposta e URL di accesso** nella sezione **Configurazione SAML di base** del portale di Azure.

    b. Fare clic su **Avanti**.

1. Nella sezione **Metadata import** (Importazione metadati) seguire questa procedura:

    ![Configure Single Sign-On](./media/kantegassoforbitbucket-tutorial/addon7.png)

    a. Selezionare **Metadata file on my computer** (File metadati in questo computer) e caricare il file di metadati scaricato dal portale di Azure.

    b. Fare clic su **Avanti**.

1. Nella sezione **Name and SSO location** (Nome e percorso SSO) seguire questa procedura:

    ![Configure Single Sign-On](./media/kantegassoforbitbucket-tutorial/addon8.png)

    a. Aggiungere il nome del provider di identità nella casella di testo **Identity provider name** (Nome provider di identità), ad esempio Azure AD.

    b. Fare clic su **Avanti**.

1. Verificare il certificato di firma e fare clic su **Next** (Avanti).

    ![Configure Single Sign-On](./media/kantegassoforbitbucket-tutorial/addon9.png)

1. Nella sezione **Bitbucket user accounts** (Account utente Bitbucket) seguire questa procedura:

    ![Configure Single Sign-On](./media/kantegassoforbitbucket-tutorial/addon10.png)

    a. Selezionare **Create users in Bitbucket's internal Directory if needed** (Crea utenti nella directory interna di Bitbucket se necessario) e immettere il nome appropriato del gruppo per gli utenti (è possibile specificare più gruppi separati da virgola).

    b. Fare clic su **Avanti**.

1. Fare clic su **Fine**.

    ![Configure Single Sign-On](./media/kantegassoforbitbucket-tutorial/addon11.png)

1. Nella sezione **Known domains for Azure AD** (Domini noti per Azure AD) seguire questa procedura:

    ![Configure Single Sign-On](./media/kantegassoforbitbucket-tutorial/addon12.png)

    a. Selezionare **Known domains** (Domini noti) dal pannello sinistro della pagina.

    b. Immettere il nome di dominio nella casella di testo **Known domains** (Domini noti).

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

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Kantega SSO for Bitbucket.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Kantega SSO for Bitbucket**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **Kantega SSO for Bitbucket**.

    ![Collegamento di Kantega SSO for Bitbucket nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-kantega-sso-for-bitbucket-test-user"></a>Creare un utente di test di Kantega SSO for Bitbucket

Per consentire agli utenti di Azure AD di accedere a Bitbucket, è necessario effettuarne il provisioning in Bitbucket. Nel caso di Kantega SSO for Bitbucket, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale Bitbucket come amministratore.

1. Fare clic sull'icona delle impostazioni.

    ![Aggiungere un dipendente](./media/kantegassoforbitbucket-tutorial/user1.png) 

1. Nella sezione della scheda **Administration** (Amministrazione) fare clic su **Users** (Utenti).

    ![Aggiungere un dipendente](./media/kantegassoforbitbucket-tutorial/user2.png)

1. Fare clic su **Create User** (Crea utente).

    ![Aggiungere un dipendente](./media/kantegassoforbitbucket-tutorial/user3.png)   

1. Nella pagina della finestra di dialogo **Create User** (Crea utente) seguire questa procedura:

    ![Aggiungere un dipendente](./media/kantegassoforbitbucket-tutorial/user4.png) 

    a. Nella casella di testo **Username** (Nome utente) digitare l'indirizzo di posta elettronica di un utente, ad esempio Brittasimon@contoso.com.

    b. Nella casella di testo **Full Name** (Nome completo) digitare il nome completo dell'utente, ad esempio Britta Simon.

    c. Nella casella di testo **Email address** (Indirizzo di posta elettronica) digitare l'indirizzo di posta elettronica dell'utente, ad esempio Brittasimon@contoso.com.

    d. Nella casella di testo **Password** digitare la password dell'utente.

    e. Nella casella di testo **Confirm Password** (Conferma password) digitare di nuovo la password dell'utente.

    f. Fare clic su **Create User** (Crea utente).

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Kantega SSO for Bitbucket nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Kantega SSO for Bitbucket per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

