---
title: 'Esercitazione: Integrazione di Azure Active Directory con NetDocuments | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e NetDocuments.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1a47dc42-1a17-48a2-965e-eca4cfb2f197
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: jeedes
ms.openlocfilehash: d42e266de9c958c8a71cc5fa680a78c2278cec6e
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65956785"
---
# <a name="tutorial-azure-active-directory-integration-with-netdocuments"></a>Esercitazione: Integrazione di Azure Active Directory con NetDocuments

Questa esercitazione descrive come integrare NetDocuments con Azure Active Directory (Azure AD).
L'integrazione di NetDocuments con Azure AD offre i vantaggi seguenti:

* In Azure AD è possibile controllare chi può accedere a NetDocuments.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a NetDocuments con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con NetDocuments, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di NetDocuments abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* NetDocuments supporta l'accesso SSO avviato da **SP**

## <a name="adding-netdocuments-from-the-gallery"></a>Aggiunta di NetDocuments dalla raccolta

Per configurare l'integrazione di NetDocuments in Azure AD, è necessario aggiungere NetDocuments dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere NetDocuments dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **NetDocuments**, selezionare **NetDocuments** dal pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![NetDocuments nell'elenco dei risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con NetDocuments usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in NetDocuments.

Per configurare e testare l'accesso Single Sign-On di Azure AD con NetDocuments, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di NetDocuments](#configure-netdocuments-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di NetDocuments](#create-netdocuments-test-user)** : per avere una controparte di Britta Simon in NetDocuments collegata alla rappresentazione in Azure AD dell'utente.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con NetDocuments, seguire i passaggi seguenti:

1. Nella pagina di integrazione dell'applicazione [NetDocuments](https://portal.azure.com/) nel **portale di Azure**, selezionare l'accesso **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni sul dominio e sugli URL dell'accesso Single Sign-On di NetDocuments](common/sp-reply.png)

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'URL di risposta e l'URL di accesso Single Sign-On effettivi. ID repository è un valore che inizia con CA- seguito da un codice a 8 caratteri associato al repository di NetDocuments. Per altre informazioni, è possibile esaminare il [documento di supporto identità federativa NetDocuments](https://support.netdocuments.com/hc/en-us/articles/205220410-Federated-Identity-Login). In alternativa, è possibile contattare il [team di supporto clienti di NetDocuments](https://support.netdocuments.com/hc/) per ottenere questi valori, in caso di difficoltà nella configurazione riscontrate usando le informazioni sopra riportate. È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

6. Nella sezione **Configura NetDocuments** copiare gli URL appropriati in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-netdocuments-single-sign-on"></a>Configurare l'accesso Single Sign-On di NetDocuments

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

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a NetDocuments.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **NetDocuments**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **NetDocuments**.

    ![Collegamento a NetDocuments nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

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

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di NetDocuments nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione NetDocuments per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

