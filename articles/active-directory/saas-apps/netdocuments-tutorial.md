---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con NetDocuments | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e NetDocuments.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1a47dc42-1a17-48a2-965e-eca4cfb2f197
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0bf25ce318415a8aa36bca8d4cd7380e4e8e67ca
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70743466"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netdocuments"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con NetDocuments

Questa esercitazione descrive come integrare NetDocuments con Azure Active Directory (Azure AD). Integrando NetDocuments con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a NetDocuments.
* Abilitare gli utenti per l'accesso automatico a NetDocuments con i rispettivi account Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di NetDocuments abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* NetDocuments supporta l'accesso SSO avviato da **SP**

## <a name="adding-netdocuments-from-the-gallery"></a>Aggiunta di NetDocuments dalla raccolta

Per configurare l'integrazione di NetDocuments in Azure AD, è necessario aggiungere NetDocuments dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **NetDocuments** nella casella di ricerca.
1. Selezionare **NetDocuments** nel riquadro dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netdocuments"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per NetDocuments

Configurare e testare l'accesso SSO di Azure AD con NetDocuments usando un utente di test di nome **B.Simon**. Per il corretto funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in NetDocuments.

Per configurare e testare l'accesso SSO di Azure AD con NetDocuments, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On per NetDocuments](#configure-netdocuments-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare un utente di test di NetDocuments](#create-netdocuments-test-user)** : per avere in NetDocuments una controparte di B.Simon collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **NetDocuments** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'URL di risposta e l'URL di accesso Single Sign-On effettivi. L'ID repository è un valore che inizia con **CA-** seguito da un codice di 8 caratteri associato al repository NetDocuments. Per altre informazioni, è possibile esaminare il [documento di supporto identità federativa NetDocuments](https://support.netdocuments.com/hc/en-us/articles/205220410-Federated-Identity-Login). In alternativa, è possibile contattare il [team di supporto clienti di NetDocuments](https://support.netdocuments.com/hc/) per ottenere questi valori, in caso di difficoltà nella configurazione riscontrate usando le informazioni sopra riportate. È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. L'applicazione NetDocuments prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. La schermata seguente illustra l'elenco degli attributi predefiniti in cui **nameidentifier** è associato a **user.userprincipalname**. L'applicazione NetDocuments prevede che **nameidentifier** sia mappato a **employeeid** o a qualsiasi altra attestazione applicabile all'organizzazione come **nameidentifier**, di conseguenza è necessario modificare il mapping dell'attributo. A tale scopo, fare clic sull'icona **Modifica** e modificare il mapping.

    ![image](common/edit-attribute.png)

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il file **XML dei metadati della federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura NetDocuments** copiare gli URL appropriati in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a NetDocuments.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **NetDocuments**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-netdocuments-sso"></a>Configurare l'accesso Single Sign-On per NetDocuments

1. In un'altra finestra del Web browser accedere al sito aziendale di NetDocuments come amministratore.

2. Passare alla pagina **Admin**.

3. Fare clic su **Aggiungi e rimuovi utenti e gruppi**.
   
    ![Repository](./media/netdocuments-tutorial/ic795047.png "Repository")

4. Fare clic su **Configura opzioni di autenticazione avanzata**.
    
    ![Configurare opzioni di autenticazione avanzata](./media/netdocuments-tutorial/ic795048.png "Configurare opzioni di autenticazione avanzata")

5. Nella finestra di dialogo della **identità federata** eseguire la procedura seguente:
   
    ![Identità federata](./media/netdocuments-tutorial/ic795049.png "Identità federata")
   
    a. Come **Federated identity server type** (Tipo di server identità federata) selezionare **Active Directory Federation Services**.
   
    b. Fare clic su **Choose File** (Scegli file) per caricare il file di metadati scaricato dal portale di Azure.
   
    c. Fare clic su **OK**.

### <a name="create-netdocuments-test-user"></a>Creare un utente di test di NetDocuments

Per consentire agli utenti di Azure AD di accedere a NetDocuments, è necessario effettuarne il provisioning in NetDocuments.  
Nel caso di NetDocuments, il provisioning è un’attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di **NetDocuments** come amministratore.

2. Nel menu in alto fare clic su **Admin**.
   
    ![Amministratore](./media/netdocuments-tutorial/ic795051.png "Amministratore")

3. Fare clic su **Aggiungi e rimuovi utenti e gruppi**.
   
    ![Repository](./media/netdocuments-tutorial/ic795047.png "Repository")

4. Nella casella di testo **Email Address** (Indirizzo di posta elettronica) digitare l'indirizzo di posta elettronica di un account valido di Azure Active Directory di cui eseguire il provisioning, quindi fare clic su **Add User** (Aggiungi utente).
   
    ![Indirizzo di posta elettronica](./media/netdocuments-tutorial/ic795053.png "Indirizzo di posta elettronica")
   
    >[!NOTE]
    >Il titolare dell'account di Azure Active Directory riceverà un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo. È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da NetDocuments per eseguire il provisioning degli account utente di Azure Active Directory.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di NetDocuments nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione NetDocuments per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare NetDocuments con Azure AD](https://aad.portal.azure.com/)

