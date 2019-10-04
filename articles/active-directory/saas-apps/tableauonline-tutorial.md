---
title: 'Esercitazione: Esercitazione: Integrazione di Azure Active Directory con Tableau Online | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Tableau Online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1d4b1149-ba3b-4f4e-8bce-9791316b730d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/05/2019
ms.author: jeedes
ms.openlocfilehash: 5e405dc4ea323a6869207de53b7577ee960924eb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67089224"
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-online"></a>Esercitazione: Integrazione di Azure Active Directory con Tableau Online

Questa esercitazione descrive come integrare Tableau Online con Azure Active Directory (Azure AD).
L'integrazione di Tableau Online con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Tableau Online.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Tableau Online con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Tableau Online, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Tableau Online abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Tableau Online supporta l'accesso SSO avviato da **SP**

## <a name="adding-tableau-online-from-the-gallery"></a>Aggiunta di Tableau Online dalla raccolta

Per configurare l'integrazione di Tableau Online in Azure AD, è necessario aggiungere Tableau Online dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Tableau Online dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Tableau Online** selezionare **Tableau Online** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Tableau Online nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Tableau Online usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Tableau Online.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Tableau Online, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Tableau Online](#configure-tableau-online-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di Tableau Online](#create-tableau-online-test-user)** : per avere una controparte di Britta Simon in Tableau Online collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

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

### <a name="configure-tableau-online-single-sign-on"></a>Configurare l'accesso Single Sign-On di Tableau Online

1. In una finestra del browser diversa accedere all'applicazione Tableau Online. Fare clic su **Settings** (Impostazioni) e quindi su **Authentication** (Autenticazione).

    ![Configure Single Sign-On](./media/tableauonline-tutorial/tutorial_tableauonline_09.png)

2. Per abilitare SAML, nella sezione **Authentication types** (Tipi di autenticazione): Selezionare **Enable an additional authentication method** (Abilita un metodo di autenticazione aggiuntivo) e quindi selezionare la casella di controllo **SAML**.

    ![Configure Single Sign-On](./media/tableauonline-tutorial/tutorial_tableauonline_12.png)

3. Scorrere fino alla sezione **Import metadata file into Tableau Online** (Importa file di metadati in Tableau Online).  Fare clic su Browse (Sfoglia) e importare il file di metadati scaricato da Azure AD. Fare quindi clic su **Apply**(Applica).

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

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

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

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

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

### <a name="create-tableau-online-test-user"></a>Creare l'utente di test di Tableau Online

In questa sezione viene creato un utente chiamato Britta Simon in Tableau Online.

1. In **Tableau Online** fare clic su **Settings** (Impostazioni) e quindi sulla sezione **Authentication** (Autenticazione). Scorrere fino alla sezione **Manage Users** (Gestisci utenti). Fare clic su **Add Users** (Aggiungi utenti) e quindi su **Enter Email Addresses** (Immetti indirizzi di posta elettronica).
  
    ![Creazione di un utente test di Azure AD](./media/tableauonline-tutorial/tutorial_tableauonline_15.png)

2. Selezionare **Add users for (SAML) authentication** (Aggiungi utenti per autenticazione - SAML). Nella casella di testo **Enter email addresses** (Immetti indirizzi di posta elettronica) aggiungere britta.simon\@contoso.com
  
    ![Creazione di un utente test di Azure AD](./media/tableauonline-tutorial/tutorial_tableauonline_11.png)

3. Fare clic su **Aggiungi utenti**.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Tableau Online nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Tableau Online per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
