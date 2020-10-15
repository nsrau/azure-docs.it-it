---
title: 'Esercitazione: Esercitazione: Integrazione di Azure Active Directory con Tableau Online | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Tableau Online.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.openlocfilehash: 035bc13ee44a8a003ae860eb2bdd67432fa91f14
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88542519"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-online"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Tableau Online

Questa esercitazione descrive come integrare Tableau Online con Azure Active Directory (Azure AD). Integrando Tableau Online con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Tableau Online.
* Abilitare gli utenti per l'accesso automatico a Tableau Online con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Tableau Online abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Tableau Online supporta l'accesso SSO avviato da **SP**
* Dopo aver configurato Tableau Online, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-tableau-online-from-the-gallery"></a>Aggiunta di Tableau Online dalla raccolta

Per configurare l'integrazione di Tableau Online in Azure AD, è necessario aggiungere Tableau Online dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Tableau Online** nella casella di ricerca.
1. Selezionare **Tableau Online** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Tableau Online usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Tableau Online.

Per configurare e testare l'accesso SSO di Azure AD con Tableau Online, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Tableau Online](#configure-tableau-online-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Tableau Online](#create-tableau-online-test-user)** : per avere una controparte di B.Simon in Tableau Online collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Tableau Online, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Tableau Online** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Tableau Online](common/sp-identifier.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL: `https://sso.online.tableau.com/public/sp/login?alias=<entityid>`

    b. Nella casella di testo **Identificatore (ID entità)** digitare l'URL: `https://sso.online.tableau.com/public/sp/metadata?alias=<entityid>`

    > [!NOTE]
    > Il valore `<entityid>` verrà ottenuto dalla sezione **Configura Tableau Online** di questa esercitazione. Il valore dell'ID entità corrisponderà al valore di **Identificatore Azure AD** nella sezione **Configura Tableau Online**.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

6. Nella sezione **Configura Tableau Online** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

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
    Ad esempio, BrittaSimon\@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Tableau Online.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Tableau Online**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Tableau Online**.

    ![Collegamento di Tableau Online nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-tableau-online-sso"></a>Configurare l'accesso Single Sign-On di Tableau Online

1. In una finestra del browser diversa accedere all'applicazione Tableau Online. Fare clic su **Settings** (Impostazioni) e quindi su **Authentication** (Autenticazione).

    ![Configure Single Sign-On](./media/tableauonline-tutorial/tutorial_tableauonline_09.png)

2. Per abilitare SAML, nella sezione **Authentication types** (Tipi di autenticazione): Selezionare **Enable an additional authentication method** (Abilita un metodo di autenticazione aggiuntivo) e quindi selezionare la casella di controllo **SAML**.

    ![Configure Single Sign-On](./media/tableauonline-tutorial/tutorial_tableauonline_12.png)

3. Scorrere fino alla sezione **Import metadata file into Tableau Online** (Importa file di metadati in Tableau Online).  Fare clic su Browse (Sfoglia) e importare il file di metadati scaricato da Azure AD. Fare quindi clic su **Apply** (Applica).

   ![Configure Single Sign-On](./media/tableauonline-tutorial/tutorial_tableauonline_13.png)

4. Nella sezione **Match assertions** (Corrispondenza asserzioni) inserire il nome di asserzione del provider di identità corrispondente per l'**indirizzo e-mail**, il **nome** e il **cognome**. Per ottenere queste informazioni da Azure AD: 
  
    a. Nel portale di Azure, aprire la pagina di integrazione dell'applicazione **Tableau Online**.

    b. Nella sezione **User Attributes & Claims** (Attributi e attestazioni utente) fare clic sull'icona di modifica.

   ![Configure Single Sign-On](./media/tableauonline-tutorial/attributesection.png)

    c. Copiare il valore dello spazio dei nomi per i seguenti attributi: givenname, e-mail e cognome utilizzando la procedura seguente:

   ![Single Sign-On di Microsoft Azure AD](./media/tableauonline-tutorial/tutorial_tableauonline_10.png)

    d. Fare clic sul valore **user.givenname**

    e. Copiare il valore dalla casella di testo **Spazio dei nomi**.

    ![Configure Single Sign-On](./media/tableauonline-tutorial/attributesection2.png)

    f. Per copiare i valori dello spazio dei nomi per l'indirizzo di posta elettronica e il cognome, ripetere i passaggi precedenti.

    g. Passare all'applicazione Tableau Online e quindi impostare la sezione **User Attributes & Claims** (Attributi e attestazioni utente) nel modo seguente:

    * Indirizzo di posta elettronica: **mail** o **userprincipalname**

    * Nome: **givenname**

    * Cognome: **surname**

    ![Configure Single Sign-On](./media/tableauonline-tutorial/tutorial_tableauonline_14.png)

### <a name="create-tableau-online-test-user"></a>Creare l'utente di test di Tableau Online

In questa sezione viene creato un utente chiamato Britta Simon in Tableau Online.

1. In **Tableau Online** fare clic su **Settings** (Impostazioni) e quindi sulla sezione **Authentication** (Autenticazione). Scorrere fino alla sezione **Manage Users** (Gestisci utenti). Fare clic su **Add Users** (Aggiungi utenti) e quindi su **Enter Email Addresses** (Immetti indirizzi di posta elettronica).
  
    ![Creazione di un utente di test di Azure AD](./media/tableauonline-tutorial/tutorial_tableauonline_15.png)

2. Selezionare **Add users for (SAML) authentication** (Aggiungi utenti per autenticazione - SAML). Nella casella di testo **Enter email addresses** (Immetti indirizzi di posta elettronica) aggiungere britta.simon\@contoso.com
  
    ![Creazione di un utente di test di Azure AD](./media/tableauonline-tutorial/tutorial_tableauonline_11.png)

3. Fare clic su **Aggiungi utenti**.

### <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Tableau Online nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Tableau Online per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Informazioni sul controllo sessioni in Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)