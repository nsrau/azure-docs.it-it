---
title: 'Esercitazione: Integrazione di Azure Active Directory con AirWatch | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory ed AirWatch.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 96a3bb1c-96c6-40dc-8ea0-060b0c2a62e5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ec8b575157dcf2fe8430f554798af62b966c78d
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406707"
---
# <a name="tutorial-azure-active-directory-integration-with-airwatch"></a>Esercitazione: Integrazione di Azure Active Directory con AirWatch

Questa esercitazione descrive come integrare AirWatch con Azure Active Directory (Azure AD).
L'integrazione di AirWatch con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a AirWatch.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a AirWatch con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con AirWatch, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione AirWatch abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* AirWatch supporta l'accesso SSO avviato da **SP**

## <a name="adding-airwatch-from-the-gallery"></a>Aggiunta di AirWatch dalla raccolta

Per configurare l'integrazione di AirWatch in Azure AD, è necessario aggiungere AirWatch dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere AirWatch dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **AirWatch**, selezionare **AirWatch** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![AirWatch nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con AirWatch usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in AirWatch.

Per configurare e testare l'accesso Single Sign-On di Azure AD con AirWatch, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di AirWatch](#configure-airwatch-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Creare l'utente di test di AirWatch](#create-airwatch-test-user)**: per avere una controparte di Britta Simon in AirWatch collegata alla rappresentazione dell'utente in Azure AD.
5. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con AirWatch, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **AirWatch** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di AirWatch](common/sp-identifier.png)

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    b. Nella casella di testo **Identificatore (ID entità)** digitare il valore in questo formato: `AirWatch`

    > [!NOTE]
    > Poiché non è reale, è necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere tale valore, contattare il [team di supporto clienti di AirWatch](https://www.air-watch.com/company/contact-us/). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. L'applicazione AirWatch prevede un formato specifico per le asserzioni SAML. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente** nella pagina di integrazione dell'applicazione. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Attributi utente**.

    ![image](common/edit-attribute.png)

6. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** modificare le attestazioni usando l'**icona Modifica** o aggiungere le attestazioni usando l'opzione **Aggiungi nuova attestazione** per configurare l'attributo del token SAML come mostrato nell'immagine precedente e seguire questa procedura:

    | Nome |  Attributo di origine|
    |---------------|----------------|
    | UID | user.userprincipalname |
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

7. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

8. Nella sezione **Configura AirWatch** copiare gli URL appropriati in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-airwatch-single-sign-on"></a>Configurare l'accesso Single Sign-On per AirWatch

1. In un'altra finestra del Web browser accedere al sito aziendale di AirWatch come amministratore.

2. Nel riquadro di spostamento sinistro fare clic su **Accounts** e quindi su **Administrators**.

   ![Amministratori](./media/airwatch-tutorial/ic791920.png "Amministratori")

3. Espandere il menu **Settings** e quindi fare clic su **Directory Services**.

   ![Impostazioni](./media/airwatch-tutorial/ic791921.png "Impostazioni")

4. Fare clic sulla scheda **User** (Utente), digitare il proprio nome di dominio nella casella di testo **Base DN** (Nome distinto di base) e quindi fare clic su **Save** (Salva).

   ![Utente](./media/airwatch-tutorial/ic791922.png "Utente")

5. Fare clic sulla scheda **Server** .

   ![Server](./media/airwatch-tutorial/ic791923.png "Server")

6. Eseguire la procedura seguente:

    ![Caricamento](./media/airwatch-tutorial/ic791924.png "Caricamento")   

    a. Per **Directory Type** selezionare **None**.

    b. Selezionare **Use SAML For Authentication**.

    c. Per caricare il certificato scaricato, fare clic su **Upload**.

7. Nella sezione **Request** seguire questa procedura:

    ![Richiesta](./media/airwatch-tutorial/ic791925.png "Richiesta")  

    a. Per **Request Binding Type** selezionare **POST**.

    b. Nella finestra di dialogo **Configura accesso Single Sign-On in Airwatch** del portale di Azure copiare il valore di **URL di accesso** e quindi incollarlo nella casella di testo **URL di accesso Single Sign-On del provider di identità**.

    c. Per **NameID format** selezionare **Email address**.

    d. Fare clic su **Save**.

8. Fare di nuovo clic sulla scheda **User** .

    ![Utente](./media/airwatch-tutorial/ic791926.png "Utente")

9. Nella sezione **Attribute** seguire questa procedura:

    ![Attributo](./media/airwatch-tutorial/ic791927.png "Attributo")

    a. Nella casella di testo **Identificatore oggetto** digitare `http://schemas.microsoft.com/identity/claims/objectidentifier`.

    b. Nella casella di testo **Nome utente** digitare `https://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    c. Nella casella di testo **Nome visualizzato** digitare `https://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    d. Nella casella di testo **Nome** digitare `https://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    e. Nella casella di testo **Cognome** digitare `https://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.

    f. Nella casella di testo **Email** (Posta elettronica) digitare `https://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    g. Fare clic su **Save**.

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
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a AirWatch.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **AirWatch**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **AirWatch**.

    ![Collegamento di AirWatch nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-airwatch-test-user"></a>Creare l'utente di test di AirWatch

Per consentire agli utenti di Azure AD di accedere a AirWatch, è necessario effettuarne il provisioning in AirWatch. Nel caso di AirWatch, il provisioning è un'attività manuale.

**Per configurare il provisioning utenti, seguire questa procedura:**

1. Accedere al sito aziendale di **AirWatch** come amministratore.

2. Nel riquadro di spostamento a sinistra fare clic su **Accounts** e quindi su **Users**.
  
   ![Utenti](./media/airwatch-tutorial/ic791929.png "Utenti")

3. Dal menu **Users** scegliere **List View** e quindi fare clic su **Add \> Add User**.
  
   ![Aggiungere un utente](./media/airwatch-tutorial/ic791930.png "Aggiungere un utente")

4. Nella finestra di dialogo **Add / Edit User** seguire questa procedura:

   ![Aggiungere un utente](./media/airwatch-tutorial/ic791931.png "Aggiungere un utente")

   a. Nelle caselle di testo **Username**, **Password**, **Confirm Password**, **First Name**, **Last Name** e **Email Address** digitare il nome utente, la password, la conferma password, il nome e il cognome di un account Azure Active Directory valido di cui si vuole eseguire il provisioning.

   b. Fare clic su **Save**.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da AirWatch per eseguire il provisioning degli account utente di Azure AD.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di AirWatch nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione AirWatch per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
