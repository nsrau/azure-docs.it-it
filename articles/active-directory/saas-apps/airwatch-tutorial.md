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
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 772b37816b83c275bae927d825434dc3ca76a35c
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231982"
---
# <a name="tutorial-integrate-airwatch-with-azure-active-directory"></a>Esercitazione: Integrare AirWatch con Azure Active Directory

Questa esercitazione descrive come integrare AirWatch con Azure Active Directory (Azure AD). Integrando AirWatch con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere ad AirWatch.
* Abilitare gli utenti per l'accesso automatico ad AirWatch con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere una versione di valutazione gratuita per un mese [qui](https://azure.microsoft.com/pricing/free-trial/).
* Sottoscrizione AirWatch abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. AirWatch supporta l'accesso SSO avviato da **SP**.

## <a name="adding-airwatch-from-the-gallery"></a>Aggiunta di AirWatch dalla raccolta

Per configurare l'integrazione di AirWatch in Azure AD, è necessario aggiungere AirWatch dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **AirWatch** nella casella di ricerca.
1. Selezionare **AirWatch** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

Configurare e testare l'accesso Single Sign-On di Azure AD con AirWatch usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in AirWatch.

Per configurare e testare l'accesso SSO di Azure AD con AirWatch, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On per AirWatch](#configure-airwatch-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Creare l'utente di test di AirWatch](#create-airwatch-test-user)** : per avere una controparte di Britta Simon in AirWatch collegata alla rappresentazione dell'utente in Azure AD.
5. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **AirWatch** del [portale di Azure](https://portal.azure.com/) trovare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella pagina **Configurazione SAML di base** immettere i valori per i campi seguenti:

    1. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    1. Nella casella di testo **Identificatore (ID entità)** digitare il valore in questo formato: `AirWatch`

    > [!NOTE]
    > Poiché non è reale, è necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere tale valore, contattare il [team di supporto clienti di AirWatch](https://www.air-watch.com/company/contact-us/). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. L'applicazione AirWatch prevede un formato specifico per le asserzioni SAML. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente** nella pagina di integrazione dell'applicazione. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Attributi utente**.

    ![image](common/edit-attribute.png)

1. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** modificare le attestazioni usando l'**icona Modifica** o aggiungere le attestazioni usando l'opzione **Aggiungi nuova attestazione** per configurare l'attributo del token SAML come mostrato nell'immagine precedente e seguire questa procedura:

    | Nome |  Attributo di origine|
    |---------------|----------------|
    | UID | user.userprincipalname |
    | | |

    a. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Lasciare vuota la casella **Spazio dei nomi**.

    d. Per Origine selezionare **Attributo**.

    e. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo indicato per la riga.

    f. Fare clic su **OK**.

    g. Fare clic su **Save**.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **XML metadati federazione** e selezionare **Scarica** per scaricare il file XML dei metadati e salvarlo nel computer in uso.

   ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura AirWatch** copiare gli URL appropriati in base alle esigenze.

   ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="configure-airwatch-sso"></a>Configurare l'accesso SSO per AirWatch

1. In un'altra finestra del Web browser accedere al sito aziendale di AirWatch come amministratore.

1. Nella pagina delle impostazioni selezionare **Settings > Enterprise Integration > Directory Services** (Impostazioni > Integrazione aziendale > Servizi directory).

   ![Impostazioni](./media/airwatch-tutorial/ic791921.png "Impostazioni")

1. Fare clic sulla scheda **User** (Utente), digitare il proprio nome di dominio nella casella di testo **Base DN** (Nome distinto di base) e quindi fare clic su **Save** (Salva).

   ![Utente](./media/airwatch-tutorial/ic791922.png "Utente")

1. Fare clic sulla scheda **Server** .

   ![Server](./media/airwatch-tutorial/ic791923.png "Server")

1. Seguire questa procedura nella sezione **LDAP**:

    ![Upload](./media/airwatch-tutorial/ic791924.png "LDAP")   

    a. Per **Directory Type** selezionare **None**.

    b. Selezionare **Use SAML For Authentication**.

1. Per caricare il certificato scaricato, fare clic su **Upload** (Carica) nella sezione **SAML 2.0**.

    ![Upload](./media/airwatch-tutorial/ic791932.png "Caricamento")

1. Nella sezione **Request** seguire questa procedura:

    ![Richiesta](./media/airwatch-tutorial/ic791925.png "Richiesta")  

    a. Per **Request Binding Type** selezionare **POST**.

    b. Nella finestra di dialogo **Configura accesso Single Sign-On in AirWatch** del portale di Azure copiare il valore di **URL di accesso** e quindi incollarlo nella casella di testo **URL di accesso Single Sign-On del provider di identità**.

    c. Per **NameID format** selezionare **Email address**.

    d. Per **Authentication Request Security** (Sicurezza della richiesta di autenticazione) selezionare **None** (Nessuna).

    e. Fare clic su **Save**.

1. Fare di nuovo clic sulla scheda **User** .

    ![Utente](./media/airwatch-tutorial/ic791926.png "Utente")

1. Nella sezione **Attribute** seguire questa procedura:

    ![Attributo](./media/airwatch-tutorial/ic791927.png "Attributo")

    a. Nella casella di testo **Identificatore oggetto** digitare `http://schemas.microsoft.com/identity/claims/objectidentifier`.

    b. Nella casella di testo **Nome utente** digitare `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    c. Nella casella di testo **Nome visualizzato** digitare `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    d. Nella casella di testo **Nome** digitare `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    e. Nella casella di testo **Cognome** digitare `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.

    f. Nella casella di testo **Email** (Posta elettronica) digitare `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    g. Fare clic su **Save**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad AirWatch.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **AirWatch**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-airwatch-test-user"></a>Creare l'utente di test di AirWatch

Per consentire agli utenti di Azure AD di accedere ad AirWatch, è necessario effettuarne il provisioning in AirWatch. Nel caso di AirWatch, il provisioning è un'attività manuale.

**Per configurare il provisioning utenti, seguire questa procedura:**

1. Accedere al sito aziendale di **AirWatch** come amministratore.

2. Nel riquadro di spostamento a sinistra fare clic su **Accounts** e quindi su **Users**.
  
   ![Utenti](./media/airwatch-tutorial/ic791929.png "Utenti")

3. Dal menu **Users** (Utenti) scegliere **List View** (Visualizzazione elenco) e quindi fare clic su **Add > Add User** (Aggiungi > Aggiungi utente).
  
   ![Add User](./media/airwatch-tutorial/ic791930.png "Aggiunta di un utente")

4. Nella finestra di dialogo **Add / Edit User** seguire questa procedura:

   ![Add User](./media/airwatch-tutorial/ic791931.png "Aggiunta di un utente")

   a. Nelle caselle di testo **Username**, **Password**, **Confirm Password**, **First Name**, **Last Name** e **Email Address** digitare il nome utente, la password, la conferma password, il nome e il cognome di un account Azure Active Directory valido di cui si vuole effettuare il provisioning.

   b. Fare clic su **Save**.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente forniti da AirWatch per effettuare il provisioning degli account utente di Azure AD.

### <a name="test-sso"></a>Testare l'accesso SSO

Quando si seleziona il riquadro di AirWatch nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di AirWatch per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
