---
title: 'Esercitazione: Integrazione di Azure Active Directory con OrgChart Now | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e OrgChart Now.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 50a1522f-81de-4d14-9b6b-dd27bb1338a4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: cc2bbd0c1220a37de640bde6294eb096b25e5398
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59258199"
---
# <a name="tutorial-azure-active-directory-integration-with-orgchart-now"></a>Esercitazione: Integrazione di Azure Active Directory con OrgChart Now

Questa esercitazione descrive come integrare OrgChart Now con Azure Active Directory (Azure AD).
L'integrazione di OrgChart Now con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a OrgChart Now.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a OrgChart Now con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con OrgChart Now, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di OrgChart Now abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* OrgChart Now supporta l'accesso SSO avviato da **SP** e **IDP**

## <a name="adding-orgchart-now-from-the-gallery"></a>Aggiunta di OrgChart Now dalla raccolta

Per configurare l'integrazione di OrgChart Now in Azure AD, è necessario aggiungere OrgChart Now dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere OrgChart Now dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **OrgChart Now**, selezionare **OrgChart Now** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![OrgChart Now nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con OrgChart Now in base a un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in OrgChart Now.

Per configurare e testare l'accesso Single Sign-On di Azure AD con OrgChart Now, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di OrgChart Now](#configure-orgchart-now-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di OrgChart Now](#create-orgchart-now-test-user)**: per avere una controparte di Britta Simon in OrgChart Now collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con OrgChart Now, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione [OrgChart Now](https://portal.azure.com/) del **portale di Azure** selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di OrgChart Now](common/idp-identifier.png)

    Nella casella di testo **Identificatore** digitare un URL: `https://sso2.orgchartnow.com`

5. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![image](common/both-preintegrated-signon.png)

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://sso2.orgchartnow.com/Shibboleth.sso/Login?entityID=<YourEntityID>&target=https://sso2.orgchartnow.com`

    > [!NOTE]
    > `<YourEntityID>` è l'**identificatore Azure AD** copiato dalla sezione **Configura Springer Link**, descritta più avanti nell'esercitazione.

6. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

7. Nella sezione **Configura OrgChart Now** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-orgchart-now-single-sign-on"></a>Configurare l'accesso Single Sign-On di OrgChart Now

Per configurare l'accesso Single Sign-On sul lato **OrgChart Now**, è necessario inviare il file di **XML metadati federazione** scaricato e gli URL appropriati copiati dal portale di Azure al [team di supporto di OrgChart Now](mailto:ocnsupport@officeworksoftware.com). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

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

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a OrgChart Now.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **OrgChart Now**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **OrgChart Now**.

    ![Collegamento di OrgChart Now nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-orgchart-now-test-user"></a>Creare l'utente di test di OrgChart Now

Per consentire agli utenti di Azure AD di accedere a OrgChart Now, è necessario effettuarne il provisioning in OrgChart Now. 

1. OrgChart Now supporta il provisioning JIT, che è abilitato per impostazione predefinita. Se non esiste già, un nuovo utente viene creato durante un tentativo di accesso a OrgChart Now. La funzionalità di provisioning dell'utente JIT crea solo un utente **di sola lettura** quando una richiesta SSO proviene da un IDP riconosciuto e non viene trovato l'indirizzo di posta elettronica nell'asserzione SAML. Per questa funzionalità di provisioning automatico, è necessario creare un gruppo di accesso denominato **General** in OrgChart Now. Seguire questa procedura per creare un gruppo di accesso:

    a. Andare all'opzione **Manage Groups** (Gestisci gruppi) dopo avere fatto clic sull'icona a forma di **ingranaggio** nell'angolo in alto a destra dell'interfaccia utente.

    ![Gruppi di OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_manage.png)    

    b. Selezionare l'icona **Add** (Aggiungi) e assegnare al gruppo il nome **General**, quindi fare clic su **OK**. 

    ![Aggiunta in OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_add.png)

    c. Selezionare le cartelle a cui si vuole che abbiano accesso gli utenti generali o di sola lettura:

    ![Cartelle di OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_chart.png)

    d. **Bloccare** le cartelle in modo che solo gli utenti amministratori possano modificarle e quindi fare clic su **OK**.

    ![Blocco in OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_lock.png)

2. Per creare utenti **amministratori** e utenti **in lettura/scrittura**, è necessario creare manualmente un utente per ottenere l'accesso al livello di privilegio tramite SSO. Per eseguire il provisioning di un account utente, seguire questa procedura:

    a. Accedere a OrgChart Now come amministratore della sicurezza.

    b.  Fare clic su **Settings** (Impostazioni) nell'angolo in alto a destra e quindi passare a **Manage Users** (Gestisci utenti).

    ![Impostazioni di OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_settings.png)

    c. Fare clic su **Add** (Aggiungi) e seguire questa procedura:

    ![Gestione in OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_manageusers.png)

    * Nella casella di testo **User ID** (ID utente) immettere l'ID 'utente, ad esempio **brittasimon\@contoso.com**.

    * Nella casella di testo **Email Address** (Indirizzo di posta elettronica) immettere l'indirizzo di posta elettronica dell'utente, ad esempio **brittasimon\@contoso.com**.

    * Fare clic su **Aggiungi**.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di OrgChart Now nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione OrgChart Now per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

