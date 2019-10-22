---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con SAP Cloud for Customer | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SAP Cloud for Customer.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 90154dab-eba2-4563-bcf0-f2acc797ea97
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 837787d375a7570b7daf0a149960ca0020bcdced
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72264043"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-cloud-for-customer"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con SAP Cloud for Customer

Questa esercitazione descrive come integrare SAP Cloud for Customer con Azure Active Directory (Azure AD). Integrando SAP Cloud for Customer con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a SAP Cloud for Customer.
* Abilitare gli utenti per l'accesso automatico a SAP Cloud for Customer con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di SAP Cloud for Customer abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* SAP Cloud for Customer supporta l'accesso SSO avviato da **provider di servizi**

## <a name="adding-sap-cloud-for-customer-from-the-gallery"></a>Aggiunta di SAP Cloud for Customer dalla raccolta

Per configurare l'integrazione di SAP Cloud for Customer in Azure AD, è necessario aggiungere SAP Cloud for Customer dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **SAP Cloud for Customer** nella casella di ricerca.
1. Selezionare **SAP Cloud for Customer** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-cloud-for-customer"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per SAP Cloud for Customer

Configurare e testare l'accesso SSO di Azure AD con SAP Cloud for Customer usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SAP Cloud for Customer.

Per configurare e testare l'accesso SSO di Azure AD con SAP Cloud for Customer, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di SAP Cloud for Customer](#configure-sap-cloud-for-customer-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di SAP Cloud for Customer](#create-sap-cloud-for-customer-test-user)** : per avere una controparte di B.Simon in SAP Cloud for Customer collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **SAP Cloud for Customer** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<server name>.crm.ondemand.com`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `https://<server name>.crm.ondemand.com`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto client di SAP Cloud for Customer](https://www.sap.com/about/agreements.sap-cloud-services-customers.html). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. L'applicazione SAP Cloud for Customer prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti. Fare clic su **Modifica** per aprire la finestra di dialogo Attributi utente.

    ![image](common/edit-attribute.png)

1. Nella sezione **Attributi utente** della finestra di dialogo **Attributi utente e attestazioni** eseguire questa procedura:

    a. Fare clic sull'**icona Modifica** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![image](./media/sap-customer-cloud-tutorial/tutorial_usermail.png)

    ![image](./media/sap-customer-cloud-tutorial/tutorial_usermailedit.png)

    b. Selezionare **Trasformazione** come **origine**.

    c. Nell'elenco **Trasformazione** selezionare **ExtractMailPrefix()** .

    d. Nell'elenco **Parametro 1** selezionare l'attributo utente da usare per l'implementazione.
    Ad esempio, se si vuole usare il valore EmployeeID come identificatore utente univoco e il valore dell'attributo è stato archiviato in ExtensionAttribute2, selezionare user.extensionattribute2.

    e. Fare clic su **Save**.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il file **XML dei metadati della federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura SAP Cloud for Customer** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a SAP Cloud for Customer.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **SAP Cloud for Customer**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-sap-cloud-for-customer-sso"></a>Configurare l'accesso Single Sign-On di SAP Cloud for Customer

1. Aprire una nuova finestra del Web browser e accedere al sito aziendale di SAP Cloud for Customer come amministratore.

2. Sul lato sinistro del menu fare clic su  **Identity Providers** (Provider di identità) > **Corporate Identity Providers** (Provider di identità aziendali) > **Add** (Aggiungi) e nella finestra popup aggiungere il nome del provider di identità, ad esempio **Azure AD**, fare clic su **Save** (Salva) e quindi fare clic su **SAML 2.0 Configuration** (Configurazione di SAML 2.0).

    ![Configurazione di SAP](./media/sap-customer-cloud-tutorial/configure01.png)

3. Nella sezione **SAML 2.0 Configuration** (Configurazione di SAML 2.0) seguire questa procedura:

    ![Configurazione di SAP](./media/sap-customer-cloud-tutorial/configure02.png)

    a. Fare clic su **Browse** (Sfoglia) per caricare il file XML dei metadati della federazione scaricato dal portale di Azure.

    b. Dopo il caricamento del file XML, i valori seguenti verranno popolati automaticamente. A questo punto, fare clic su **Save** (Salva).

### <a name="create-sap-cloud-for-customer-test-user"></a>Creare l'utente di test di SAP Cloud for Customer

Per consentire agli utenti di Azure AD di accedere a SAP Cloud for Customer, è necessario effettuarne il provisioning in SAP Cloud for Customer. In SAP Cloud for Customer il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere a SAP Cloud for Customer come amministratore della sicurezza.

2. Sul lato sinistro del menu fare clic su  **Users & Authorizations** (Utenti e autorizzazioni)> **User Management** (Gestione utenti) > **Add User** (Aggiungi utente).

    ![Configurazione di SAP](./media/sap-customer-cloud-tutorial/configure03.png)

3. Nella sezione **Add New User** (Aggiungi nuovo utente) seguire questa procedura:

    ![Configurazione di SAP](./media/sap-customer-cloud-tutorial/configure04.png)

    a. Nella casella di testo **First Name** (Nome) immettere il nome dell'utente, ad esempio **B**.

    b. Nella casella di testo **Last Name** (Cognome) immettere il cognome dell'utente, ad esempio **Simon**.

    c. Nella casella di testo **E-Mail** (Posta elettronica) immettere l'indirizzo di posta elettronica dell'utente, ad esempio `B.Simon@contoso.com`.

    d. Nella casella di testo **Login Name** (Nome di accesso) immettere il nome dell'utente, ad esempio **B.Simon**.

    e. Selezionare **User Type** (Tipo utente) in base ai requisiti.

    f. Selezionare l'opzione **Account Activation** (Attivazione account) in base ai requisiti.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro SAP Cloud for Customer nel pannello di accesso, si accederà automaticamente all'istanza di SAP Cloud for Customer per cui è stato configurato l'accesso Single Sign-On. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare SAP Cloud for Customer con Azure AD](https://aad.portal.azure.com/)

