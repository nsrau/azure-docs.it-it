---
title: 'Esercitazione: Integrazione di Azure Active Directory con SAML SSO for Bamboo di resolution GmbH | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SAML SSO for Bamboo di resolution GmbH.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f00160c7-f4cc-43bf-af18-f04168d3767c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97c13469c9c70aec31314048b8971c66bae5cd49
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60004721"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bamboo-by-resolution-gmbh"></a>Esercitazione: Integrazione di Azure Active Directory con SAML SSO for Bamboo di resolution GmbH

Questa esercitazione spiega come integrare SAML SSO for Bamboo di resolution GmbH con Azure Active Directory, ovvero Azure AD.
L'integrazione di SAML SSO for Bamboo di resolution GmbH con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi ha accesso a SAML SSO for Bamboo di resolution GmbH.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a SAML SSO for Bamboo by resolution GmbH con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con SAML SSO for Bamboo di resolution GmbH, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Una sottoscrizione attiva del Single Sign-On di SAML SSO for Bamboo by resolution GmbH

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* SAML SSO for Bamboo by resolution GmbH supporta l'accesso SSO avviato da **SP e IDP**
* SAML SSO for Bamboo by resolution GmbH supporta il provisioning utenti **JIT**

## <a name="adding-saml-sso-for-bamboo-by-resolution-gmbh-from-the-gallery"></a>Aggiunta di SAML SSO for Bamboo di resolution GmbH dalla raccolta

Per configurare l'integrazione di SAML SSO for Bamboo di resolution GmbH in Azure AD è necessario aggiungere SAML SSO for Bamboo di resolution GmbH dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere SAML SSO for Bamboo di resolution GmbH dalla raccolta eseguire la procedura seguente:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **SAML SSO for Bamboo di resolution GmbH** selezionare **SAML SSO for Bamboo by resolution GmbH** dal riquadro dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![SAML SSO for Bamboo di resolution GmbH nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con SAML SSO for Bamboo by resolution GmbH con un utente test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SAML SSO for Bamboo by resolution GmbH.

Per configurare e testare l'accesso Single Sign-On di Azure AD con SAML SSO for Bamboo di resolution GmbH, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di SAML SSO for Bamboo by resolution GmbH](#configure-saml-sso-for-bamboo-by-resolution-gmbh-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di SAML SSO for Bamboo by resolution GmbH](#create-saml-sso-for-bamboo-by-resolution-gmbh-test-user)**: per avere una controparte di Britta Simon in SAML SSO for Bamboo by resolution GmbH che sia collegata alla rappresentazione di Azure AD dell'utente.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con SAML SSO for Bamboo by resolution GmbH seguire la procedura illustrata di seguito:

1. Nella pagina di integrazione dell'applicazione **SAML SSO for Bamboo by resolution GmbH** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On in SAML SSO for Bamboo di resolution GmbH](common/idp-intiated.png)

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<server-base-url>/plugins/servlet/samlsso`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<server-base-url>/plugins/servlet/samlsso`

5. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On in SAML SSO for Bamboo di resolution GmbH](common/metadata-upload-additional-signon.png)

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Contattare il [team di supporto del client di SAML SSO for Bamboo di resolution GmbH](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support) per ottenere i valori. È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

6. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

7. Nella sezione **Configura SAML SSO for Bamboo by resolution GmbH** copiare gli URL appropriati in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-saml-sso-for-bamboo-by-resolution-gmbh-single-sign-on"></a>Configurare l'accesso Single Sign-On per SAML SSO for Bamboo by resolution GmbH

1. Accedere al sito aziendale di SAML SSO for Bamboo di resolution GmbH come amministratore.

1. Sul lato destro della barra degli strumenti principale fare clic su **Settings** (Impostazioni)  > **Add-ons** (Componenti aggiuntivi).

    ![Le impostazioni](./media/bamboo-tutorial/tutorial_bamboo_setings.png)

1. Passare alla sezione SECURITY e fare clic su **SAML SingleSignOn** sulla barra dei menu.

    ![Samlsingle](./media/bamboo-tutorial/tutorial_bamboo_samlsingle.png)

1. Nella pagina **SAML SingleSignOn Plugin Configuration** (Configurazione plug-in SAML SingleSignOn) fare clic su **Add idp** (Aggiungi IdP).

    ![Add idp (Aggiungi IdP)](./media/bamboo-tutorial/tutorial_bamboo_addidp.png)

1. Nella pagina **Choose your SAML Identity Provider** (Scegliere il provider di identità SAML) eseguire la procedura seguente:

    ![Provider di identità](./media/bamboo-tutorial/tutorial_bamboo_identityprovider.png)

    a. Come **Idp Type** (Tipo IdP) selezionare **AZURE AD**.

    b. Nella casella di testo **Name** (Nome) digitare un nome.

    c. Nella casella di testo **Description** (Descrizione) digitare la descrizione.

    d. Fare clic su **Avanti**.

1. Nella pagina **Identity provider configuration** (Configurazione provider di identità) fare clic su **Next** (Avanti).

    ![Configurazione identità](./media/bamboo-tutorial/tutorial_bamboo_identityconfig.png)

1. Nella pagina **Import SAML Idp Metadata** (Importa metadati IdP SAML) fare clic su **Load File** (Carica file) per caricare il file **METADATA XML** scaricato dal portale di Azure.

    ![idpmetadata](./media/bamboo-tutorial/tutorial_bamboo_idpmetadata.png)

1. Fare clic su **Avanti**.

1. Fare clic su **Save settings** (Salva impostazioni).

    ![Salvataggio](./media/bamboo-tutorial/tutorial_bamboo_save.png)

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

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a SAML SSO for Bamboo di resolution GmbH.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **SAML SSO for Bamboo by resolution GmbH**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **SAML SSO for Bamboo by resolution GmbH** (SAML SSO for Bamboo di resolution GmbH).

    ![Collegamento SAML SSO for Bamboo di resolution GmbH nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-saml-sso-for-bamboo-by-resolution-gmbh-test-user"></a>Creare un utente di test di SAML SSO for Bamboo by resolution GmbH

L'obiettivo di questa sezione è creare l'utente Britta Simon in SAML SSO for Bamboo di resolution GmbH. SAML SSO for Bamboo di resolution GmbH supporta il provisioning JIT e anche la creazione manuale di utenti. Contattare il [team di assistenza per il client SAML SSO for Bamboo di resolution GmbH](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support) in base ai propri requisiti.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di SAML SSO for Bamboo by resolution GmbH nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione SAML SSO for Bamboo by resolution GmbH per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
