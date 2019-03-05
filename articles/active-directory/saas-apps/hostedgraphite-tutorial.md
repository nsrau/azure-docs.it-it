---
title: 'Esercitazione: Integrazione di Azure Active Directory con Hosted Graphite | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Hosted Graphite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a1ac4d7f-d079-4f3c-b6da-0f520d427ceb
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c44b89b66c1908c00e075606d6b0201bd3ea6af6
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56872948"
---
# <a name="tutorial-azure-active-directory-integration-with-hosted-graphite"></a>Esercitazione: Integrazione di Azure Active Directory con Hosted Graphite

Questa esercitazione descrive come integrare Hosted Graphite con Azure Active Directory (Azure AD).
L'integrazione di Hosted Graphite con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Hosted Graphite.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Hosted Graphite con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Hosted Graphite, è necessario quanto segue:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Hosted Graphite abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Hosted Graphite supporta l'accesso SSO avviato da **SP e IDP**
* Hosted Graphite supporta il provisioning utenti **JIT**

## <a name="adding-hosted-graphite-from-the-gallery"></a>Aggiunta di Hosted Graphite dalla raccolta

Per configurare l'integrazione di Hosted Graphite in Azure AD, è necessario aggiungere Hosted Graphite dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Hosted Graphite dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Hosted Graphite**, selezionare **Hosted Graphite** nel pannello dei risultati, quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Hosted Graphite nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Hosted Graphite usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Hosted Graphite.

Per configurare e testare l'accesso Single Sign-On in AD Azure con Hosted Graphite, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On per Hosted Graphite](#configure-hosted-graphite-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di Hosted Graphite](#create-hosted-graphite-test-user)**: per avere una controparte di Britta Simon in Hosted Graphite collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Hosted Graphite, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Hosted Graphite** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Hosted Graphite](common/idp-intiated.png)

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://www.hostedgraphite.com/metadata/<user id>`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://www.hostedgraphite.com/complete/saml/<user id>`

5. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Hosted Graphite](common/metadata-upload-additional-signon.png)

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://www.hostedgraphite.com/login/saml/<user id>/`

    > [!NOTE]
    > Si noti che questi non sono i valori reali. È necessario aggiornare questi valori con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori passare a Access->SAML setup (Accesso -> Configurazione SAML) nell'applicazione oppure contattare il [team di supporto di Hosted Graphite](mailto:help@hostedgraphite.com).

6. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/certificatebase64.png)

7. Nella sezione **Configura Hosted Graphite** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL disconnessione

### <a name="configure-hosted-graphite-single-sign-on"></a>Configurare l'accesso Single Sign-On per Hosted Graphite

1. Accedere al tenant di Hosted Graphite come amministratore.

2. Accedere alla pagina **SAML Setup** (Configurazione SAML) dalla barra laterale, facendo clic su **Access -> SAML Setup** (Accesso -> Configurazione SAML).

    ![Configurazione accesso Single Sign-On sul lato app](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_000.png)

3. Verificare che gli URL corrispondano alla configurazione eseguita nella sezione **Configurazione SAML di base** del portale di Azure.

    ![Configurazione accesso Single Sign-On sul lato app](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_001.png)

4. Nelle caselle di testo **Entity or Issuer ID** (ID entità o autorità di certificazione) e **SSO Login URL** (URL di accesso SSO) incollare il valore di **Identificatore Azure AD** e **URL di accesso** copiato dal portale di Azure.

    ![Configurazione accesso Single Sign-On sul lato app](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_002.png)

5. Selezionare **Read-only** (Sola lettura) come valore di **Default User Role** (Ruolo utente predefinito).

    ![Configurazione accesso Single Sign-On sul lato app](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_004.png)

6. Aprire nel Blocco note il certificato con codifica Base 64 scaricato dal portale di Azure, copiarne il contenuto negli Appunti e incollarlo nella casella di testo **Certificato X.509**.

    ![Configurazione accesso Single Sign-On sul lato app](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_005.png)

7. Fare clic sul pulsante **Salva**.

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

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Hosted Graphite.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Hosted Graphite**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Hosted Graphite**.

    ![Collegamento di Hosted Graphite nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-hosted-graphite-test-user"></a>Creare l'utente di test di Hosted Graphite

In questa sezione viene creato un utente di nome Britta Simon in Hosted Graphite. Hosted Graphite supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in Hosted Graphite, ne viene creato uno nuovo dopo l'autenticazione.

> [!NOTE]
> Per creare un utente manualmente, contattare il team di supporto di Hosted Graphite all'indirizzo <mailto:help@hostedgraphite.com>.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Hosted Graphite nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Hosted Graphite per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

