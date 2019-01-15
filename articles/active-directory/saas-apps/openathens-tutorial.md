---
title: 'Esercitazione: Integrazione di Azure Active Directory con OpenAthens | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e OpenAthens.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dd4adfc7-e238-41d5-8b25-1811f08078b6
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/4/2019
ms.author: jeedes
ms.openlocfilehash: b5765485ba9f45f95db7235a87bdba5b0dc5f9bd
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/07/2019
ms.locfileid: "54062180"
---
# <a name="tutorial-azure-active-directory-integration-with-openathens"></a>Esercitazione: Integrazione di Azure Active Directory con OpenAthens

Questa esercitazione descrive come integrare OpenAthens con Azure Active Directory (Azure AD).
L'integrazione di OpenAthens con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a OpenAthens.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a OpenAthens con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con OpenAthens, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di OpenAthens abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* OpenAthens supporta l'accesso SSO avviato da **IDP**

* OpenAthens supporta il provisioning utenti **JIT**

## <a name="adding-openathens-from-the-gallery"></a>Aggiunta di OpenAthens dalla raccolta

Per configurare l'integrazione di OpenAthens in Azure AD, è necessario aggiungere OpenAthens dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere OpenAthens dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **OpenAthens**, selezionare **OpenAthens** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![OpenAthens nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con OpenAthens in base a un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in OpenAthens.

Per configurare e testare l'accesso Single Sign-On di Azure AD con OpenAthens, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di OpenAthens](#configure-openathens-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test OpenAthens](#create-openathens-test-user)**: per avere una controparte di Britta Simon in OpenAthens collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con OpenAthens, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **OpenAthens** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

5. Nella sezione **Configurazione SAML di base** caricare il **file di metadati del provider di servizi**, la cui procedura è illustrata più avanti in questa esercitazione.

    a. Fare clic su **Carica il file di metadati**.

    ![Caricamento dei metadati di OpenAthens](common/upload-metadata.png)

    b. Fare clic su **logo cartella** per selezionare il file di metadati e fare quindi clic su **Upload**.

    ![Selezione dei metadati da caricare di OpenAthens](common/browse-upload-metadata.png)

    c. Dopo il caricamento del file di metadati, il valore **Identificatore** viene inserito automaticamente nella casella di testo della sezione **Configurazione SAML di base**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di OpenAthens](common/idp-identifier.png)

6. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML dei metadati della federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

### <a name="configure-openathens-single-sign-on"></a>Configurare l'accesso Single Sign-On per OpenAthens

1. In un'altra finestra del Web browser accedere al sito aziendale di OpenAthens come amministratore.

2. Selezionare **Connections** (Connessioni) nella scheda **Management** (Gestione). 

    ![Configura accesso Single Sign-On](./media/openathens-tutorial/tutorial_openathens_application1.png)

3. Selezionare **SAML 1.1 o 2.0** e quindi selezionare il pulsante **Configura**.

    ![Configura accesso Single Sign-On](./media/openathens-tutorial/tutorial_openathens_application2.png)
    
4. Per aggiungere la configurazione, selezionare il pulsante **Sfoglia** per caricare il file di metadati con estensione XML scaricato dal portale di Azure e quindi selezionare **Aggiungi**.

    ![Configura accesso Single Sign-On](./media/openathens-tutorial/tutorial_openathens_application3.png)

5. Nella scheda **Dettagli** seguire questa procedura.

    ![Configura accesso Single Sign-On](./media/openathens-tutorial/tutorial_openathens_application4.png)

    a. In **Display name mapping** (Mapping nome visualizzato) selezionare **Use attribute** (Usa attributo).

    b. Nella casella di testo **Display name attribute** (Attributo nome visualizzato) immettere il valore `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    c. In **Unique user mapping** (Mapping utente univoco) selezionare **Use attribute** (Usa attributo).

    d. Nella casella di testo **Unique user attribute** (Attributo utente univoco) immettere il valore `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    e. In **Status** (Stato) selezionare tutte le tre caselle di controllo.

    f. In **Create local accounts** (Crea account locali) selezionare **automatically** (automaticamente).

    g. Selezionare **Save changes** (Salva modifiche).

    h. Dalla scheda **Relying Party** copiare il valore di **URL dei metadati** e aprire l'URL nel browser per scaricare il file **XML dei metadati SP**. Caricare questo file di metadati SP nella sezione **Configurazione SAML di base** in Azure AD.

    ![Configura accesso Single Sign-On](./media/openathens-tutorial/tutorial_openathens_application5.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD 

Questa sezione descrive come creare un utente di test denominato Britta Simon nel portale di Azure.

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

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a OpenAthens.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **OpenAthens**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni digitare e selezionare **OpenAthens**.

    ![Collegamento di OpenAthens nell'elenco Applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-openathens-test-user"></a>Creare l'utente di test di OpenAthens

In questa sezione viene creato un utente di nome Britta Simon in OpenAthens. OpenAthens supporta il **provisioning utenti JIT**, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in OpenAthens, ne viene creato uno nuovo dopo l'autenticazione.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di OpenAthens nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione OpenAthens per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

