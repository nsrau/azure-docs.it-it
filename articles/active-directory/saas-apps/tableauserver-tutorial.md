---
title: 'Esercitazione: Integrazione di Azure Active Directory con Tableau Server | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Tableau Server.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: 480e799e0fe307b39ce8a2f0b026a04f507a6beb
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65408006"
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-server"></a>Esercitazione: Integrazione di Azure Active Directory con Tableau Server

Questa esercitazione descrive come integrare Tableau Server con Azure Active Directory (Azure AD).
L'integrazione di Tableau Server con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Tableau Server.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Tableau Server con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Tableau Server, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Tableau Server abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Tableau Server supporta l'accesso SSO avviato da **SP**

## <a name="adding-tableau-server-from-the-gallery"></a>Aggiunta di Tableau Server dalla raccolta

Per configurare l'integrazione di Tableau Server in Azure AD, è necessario aggiungere Tableau Server dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Tableau Server dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Tableau Server**, selezionare **Tableau Server** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Tableau Server nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Tableau Server usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Tableau Server.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Tableau Server, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Tableau Server](#configure-tableau-server-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di Tableau Online](#create-tableau-server-test-user)**: per avere una controparte di Britta Simon in Tableau Server collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Tableau Server, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Tableau Server** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Tableau Server](common/sp-identifier-reply.png)

    a. Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://azure.<domain name>.link`

    b. Nella casella di testo **Identificatore** digitare un URL usando il modello seguente: `https://azure.<domain name>.link`

    c. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://azure.<domain name>.link/wg/saml/SSO/index.html`

    > [!NOTE]
    > I valori precedenti non sono valori reali. Aggiornare i valori con l'URL e l'identificatore effettivi indicati nella pagina di configurazione di Tableau Server, come illustrato più avanti in questa esercitazione.

5. L'applicazione Tableau Server prevede un'attestazione personalizzata **username** che deve essere definita come indicato di seguito. Questa attestazione viene usata come attestazione di identificatore utente invece dell'attestazione di identificatore univoco dell'utente. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente e attestazioni** nella pagina di integrazione dell'applicazione. Fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Attributi e attestazioni utente**.

    ![image](common/edit-attribute.png)

6. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente e attestazioni** configurare l'attributo del token SAML come mostrato nell'immagine precedente e seguire questa procedura:

    | Nome | Attributo di origine | Spazio dei nomi |
    | ---------------| --------------- | ----------- |
    | username | user.userprincipalname | `https://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | | |

    a. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Immettere il valore **Nome spazio dei nomi**.

    d. Per Origine selezionare **Attributo**.

    e. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo indicato per la riga.

    f. Fare clic su **Ok**

    g. Fare clic su **Save**.

7. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

8. Nella sezione **Configura Tableau Server** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-tableau-server-single-sign-on"></a>Configurare il Single Sign-On di Tableau Server

1. Per configurare l'accesso SSO per l'applicazione, è necessario accedere al tenant di Tableau Server come amministratore.

2. Nella scheda **CONFIGURATION** (Configurazione) selezionare **User Identity & Access**(Identità utente e accesso), quindi selezionare la scheda **Authentication Method** (Metodo di autenticazione).

    ![Configure Single Sign-On](./media/tableauserver-tutorial/tutorial-tableauserver-auth.png)

3. Nella pagina **CONFIGURATION** (Configurazione) seguire questa procedura:

    ![Configure Single Sign-On](./media/tableauserver-tutorial/tutorial-tableauserver-config.png)

    a. Per **Authentication Method** (Metodo di autenticazione) selezionare SAML.

    b. Selezionare la casella di controllo **Enable SAML Authentication for the server** (Abilita autenticazione SAML per il server).

    c. Tableau Server return URL (URL restituito di Tableau Server): l'URL a cui accedono gli utenti di Tableau Server, ad esempio <http://tableau_server>. L'uso di `http://localhost` non è consigliato. L'uso di un URL con una barra finale, ad esempio `http://tableau_server/`, non è supportato. Copiare il valore di **Tableau Server return URL** (URL restituito di Tableau Server) e incollarlo nella casella di testo **URL di accesso** nella sezione **Configurazione SAML di base** del portale di Azure

    d. SAML entity ID (ID entità SAML): l'ID entità identifica in modo univoco l'installazione di Tableau Server nel provider di identità. Se si desidera, qui è possibile immettere di nuovo l'URL di Tableau Server, ma non deve essere l'URL di Tableau Server in uso. Copiare il valore di **SAML entity ID** (ID entità SAML) e incollarlo nella casella di testo **Identificatore** nella sezione **Configurazione SAML di base** del portale di Azure.

    e. Fare clic su **Download XML Metadata File** (Scarica file di metadati XML) e aprire il file nell'applicazione editor di testo. Cercare l'URL del servizio consumer di asserzione con Http Post e Indice 0 e copiarlo. Ora incollarlo nella casella di testo **URL di risposta** nella sezione **Configurazione SAML di base** del portale di Azure.

    f. Individuare il file di metadati della federazione scaricato dal portale di Azure e quindi caricarlo in **SAML Idp metadata file**(File metadati IdP SAML).

    g. Immettere i nomi per gli attributi usati dal provider di identità per contenere nomi utente, nomi visualizzati e indirizzi di posta elettronica.

    h. Fare clic su **Save** (Salva).

    > [!NOTE]
    > Il cliente deve caricare eventuali certificati nella configurazione SSO SAML di Tableau Server e il passaggio verrà ignorato nel flusso SSO. Per assistenza nella configurazione di SAML in Tableau Server, vedere questo articolo: [Configure SAML](https://onlinehelp.tableau.com/v2018.2/server/en-us/saml_config_steps_tsm_ui.htm) (Configurazione di SAML).

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

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Tableau Server.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Tableau Server** .

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Tableau Server**.

    ![Collegamento di Tableau Server nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-tableau-server-test-user"></a>Creare l'utente di test di Tableau Server

Questa sezione descrive come creare un utente chiamato Britta Simon in Tableau Server. In Tableau Server è necessario eseguire il provisioning di tutti gli utenti.

Il valore username dell'utente deve corrispondere al valore configurato nell'attributo personalizzato **username**in Azure AD. Con un mapping corretto, l'integrazione per la Configurazione dell'accesso Single Sign-On di Azure AD dovrebbe funzionare.

> [!NOTE]
> Se si deve creare manualmente un utente, è necessario contattare l'amministratore di Tableau Server dell'organizzazione.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Tableau Server nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Tableau Server per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

