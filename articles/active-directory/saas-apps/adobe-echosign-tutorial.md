---
title: 'Esercitazione: Integrazione di Azure Active Directory con Adobe Sign | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Adobe Sign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: f9385723-8fe7-4340-8afb-1508dac3e92b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcb27e24e9b53b734a24304a63c8fd91d5e94f5d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67107338"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>Esercitazione: Integrazione di Azure Active Directory con Adobe Sign

Questa esercitazione descrive come integrare Adobe Sign con Azure Active Directory (Azure AD).
L'integrazione di Adobe Sign con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Adobe Sign.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) ad Adobe Sign con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Adobe Sign, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Adobe Sign abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Adobe Sign supporta l'accesso SSO avviato da **SP**

## <a name="adding-adobe-sign-from-the-gallery"></a>Aggiunta di Adobe Sign dalla raccolta

Per configurare l'integrazione di Adobe Sign in Azure AD, è necessario aggiungerla dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Adobe Sign dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Adobe Sign**, selezionare **Adobe Sign** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Adobe Sign nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Adobe Sign usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Adobe Sign.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Adobe Sign, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On per Adobe Sign](#configure-adobe-sign-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Adobe Sign](#create-adobe-sign-test-user)** : per avere una controparte di Britta Simon in Adobe Sign collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Adobe Sign, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Adobe Sign** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Adobe Sign](common/sp-identifier.png)

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<companyname>.echosign.com/`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL usando il modello seguente: `https://<companyname>.echosign.com`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Adobe Sign](https://helpx.adobe.com/in/contact/support.html). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

4. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Set up Adobe Sign** (Configura Adobe Sign) copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-adobe-sign-single-sign-on"></a>Configurare l'accesso Single Sign-On per Adobe

1. Prima della configurazione, contattare il [team di supporto clienti di Adobe Sign](https://helpx.adobe.com/in/contact/support.html) per aggiungere il dominio nell'elenco elementi consentiti di Adobe Sign. Di seguito viene illustrato come aggiungere il dominio:

    a. Il [team di supporto clienti di Adobe Sign](https://helpx.adobe.com/in/contact/support.html) invia un token generato in modo casuale. Il token per il proprio dominio sarà simile al seguente: **adobe-sign-verification= xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx**

    b. Pubblicare il token di verifica in un record DNS TEXT e comunicarlo al [team di supporto clienti di Adobe Sign](https://helpx.adobe.com/in/contact/support.html).
    
    > [!NOTE]
    > L'operazione può richiedere alcuni giorni. Eventuali ritardi nella propagazione dei record DNS indicano che un valore pubblicato nel DNS potrebbe non essere visibile per almeno un'ora. Si suppone che l'amministratore IT sappia come pubblicare questo token in un record DNS TEXT.
    
    c. Una volta avvisato il [team di supporto clienti di Adobe Sign](https://helpx.adobe.com/in/contact/support.html) tramite il ticket di supporto, dopo la pubblicazione del token, il dominio verrà convalidato e aggiunto al proprio account.
    
    d. Procedura da eseguire per pubblicare il token in un record DNS:

    * Accedere al proprio account di dominio.
    * Trovare la pagina per l'aggiornamento del record DNS. Questa pagina potrebbe essere denominata Gestione DNS, Gestione server dei nomi o Impostazioni avanzate.
    * Trovare i record TXT relativi al proprio dominio.
    * Aggiungere un record TXT con il valore completo del token fornito da Adobe.
    * Salvare le modifiche.

1. In un'altra finestra del Web browser accedere al sito aziendale di Adobe Sign come amministratore.

1. Nel menu SAML, selezionare **Impostazioni account** > **Impostazioni SAML**.
   
    ![Schermata della pagina delle impostazioni SAML di Adobe Sign](./media/adobe-echosign-tutorial/ic789520.png "Account")

1. Nella sezione **SAML Settings** (Impostazioni SAML), seguire questa procedura:
  
   ![Schermata delle impostazioni SAML](./media/adobe-echosign-tutorial/ic789521.png "Impostazioni SAML")
   
   ![Schermata delle impostazioni SAML](./media/adobe-echosign-tutorial/ic789522.png "Impostazioni SAML")

   a. Per **SAML Mode** (Modalità SAML), selezionare **SAML Mandatory** (SAML obbligatorio).
   
   b. Selezionare **Allow Echosign Account Administrators to log in using their Echosign Sign Credentials** (Consenti agli amministratori account di Echosign Sign di accedere con le credenziali di Echosign Sign).
   
   c. In **User Creation** (Creazione utente), selezionare **Automatically add users authenticated through SAML** (Aggiungi automaticamente gli utenti autenticati tramite SAML).

   d. Incollare l'**identificatore di Azure AD** copiato dal portale di Azure nella casella di testo **Idp Entity ID** (ID entità IdP).
    
   e. Incollare il valore di **URL di accesso**, copiato dal portale di Azure, nella casella di testo **Idp Login URL** (URL di accesso IdP).
   
   f. Incollare l'**URL di disconnessione** copiato dal portale di Azure nella casella di testo **IdP Logout URL** (ID disconnessione IdP).

   g. Aprire il file del **certificato (Base64)** scaricato nel blocco note. Copiare il contenuto negli Appunti e quindi incollarlo nella casella di testo **IDP Certificate** (Certificato IDP).

   h. Selezionare **Save changes** (Salva modifiche).

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

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad Adobe Sign.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Adobe Sign**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni digitare e selezionare **Adobe Sign**.

    ![Collegamento di Adobe Sign nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-adobe-sign-test-user"></a>Creare l'utente di test di Adobe Sign

Per consentire agli utenti di Azure AD di accedere a Adobe Sign, è necessario eseguirne il provisioning in Adobe Sign. Si tratta di un processo manuale.

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente forniti da Adobe Sign per eseguire il provisioning degli account utente di Azure AD. 

1. Accedere al sito aziendale di **Adobe Sign** come amministratore.

2. Nel menu in alto, selezionare **Account**. Quindi, nel riquadro sinistro, selezionare **Utenti e gruppi** > **Crea un nuovo utente**.
   
    ![Schermata del sito aziendale di Adobe Site, con evidenziate le opzioni relative all'account, agli utenti e ai gruppi, all'azienda e alla creazione di un nuovo utente](./media/adobe-echosign-tutorial/ic789524.png "Account")
   
3. Nella sezione **Create New User** seguire questa procedura:
   
    ![Schermata Crea nuovo utente](./media/adobe-echosign-tutorial/ic789525.png "Crea utente")
   
    a. Digitare l'**indirizzo di posta elettronica**, il **nome** e il **cognome** di un account utente di Azure AD valido di cui si vuole eseguire il provisioning.
   
    b. Selezionare **Crea utente**.

>[!NOTE]
>Il titolare dell'account di Azure Active Directory riceverà un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo. 

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Adobe Sign nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Adobe Sign per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

