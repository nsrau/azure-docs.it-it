---
title: 'Esercitazione: Integrazione di Azure Active Directory con Wdesk | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Wdesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 06900a91-a326-4663-8ba6-69ae741a536e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/02/2020
ms.author: jeedes
ms.openlocfilehash: 349a9920adc0d9353ee8e8e821f54bc2ea2f2616
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2020
ms.locfileid: "80666974"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-wdesk"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Wdesk

Questa esercitazione descrive come integrare Wdesk con Azure Active Directory (Azure AD). Integrando Wdesk with Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Wdesk.
* Abilitare gli utenti per l'accesso automatico a Wdesk con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Wdesk abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Wdesk supporta l'accesso SSO avviato da **SP** e **IDP**
* Dopo aver configurato Wdesk, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-wdesk-from-the-gallery"></a>Aggiunta di Wdesk dalla raccolta

Per configurare l'integrazione di Wdesk in Azure AD, è necessario aggiungere Wdesk dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Wdesk** nella casella di ricerca.
1. Selezionare **Wdesk** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Wdesk usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Wdesk.

Per configurare e testare l'accesso SSO di Azure AD con Wdesk, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Wdesk](#configure-wdesk-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Wdesk](#create-wdesk-test-user)** : per avere una controparte di B.Simon in Wdesk collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Wdesk, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Wdesk** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Wdesk](common/idp-intiated.png)

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>`

5. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Wdesk](common/metadata-upload-additional-signon.png)

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<subdomain>.wdesk.com/auth/login/saml/<instancename>`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Si ottengono questi valori dal portale di WDesk quando si configura l'accesso Single Sign-On.

4. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

6. Nella sezione **Configura Wdesk** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD 

Questa sezione descrive come creare un utente di test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare brittasimon@yourcompanydomain.extension. Ad esempio, usare BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Wdesk.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Wdesk**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Wdesk**.

    ![Collegamento di Wdesk nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-wdesk-sso"></a>Configurare l'accesso Single Sign-On di Wdesk

1. In un'altra finestra del Web browser accedere a Wdesk come amministratore della sicurezza.

2. In basso a sinistra fare clic su **Amministratore** e scegliere **Amministratore dell'account**:
 
     ![Configure Single Sign-On](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. In Wdesk Admin passare a **Security** (Sicurezza) e quindi a **SAML** > **SAML Settings** (Impostazioni SAML):

    ![Configure Single Sign-On](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig2.png)

1. In **SAML User ID Settings** (Impostazioni ID utente SAML) selezionare **SAML User ID is Wdesk Username** (L'ID utente SAML corrisponde al nome utente Wdesk).

    ![Configure Single Sign-On](./media/wdesk-tutorial/wdesk-username.png)

4. In **General Settings** (Impostazioni generali) selezionare **Enable SAML Single Sign On** (Abilita SAML Single Sign On):

    ![Configure Single Sign-On](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig3.png)

5. In **Service Provider Details** (Dettagli del provider di servizi) seguire questa procedura:

    ![Configure Single Sign-On](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig4.png)

      a. Copiare l'**URL di accesso** e incollarlo nella casella di testo **URL di accesso** nel portale di Azure.
   
      b. Copiare l'**URL dei metadati** e incollarlo nella casella di testo **Identificatore** nel portale di Azure.
       
      c. Copiare l'**URL del servizio consumer** e incollarlo nella casella di testo **URL di risposta** nel portale di Azure.
   
      d. Fare clic su **Salva** nel portale di Azure per salvare le modifiche.      

6. Fare clic su **Configure IdP Settings** (Configura impostazioni IdP) per aprire la finestra di dialogo **Edit IdP Settings** (Modifica impostazioni IdP). Fare clic su **Choose File** (Scegli file) per trovare il file **Metadata.xml** salvato dal portale di Azure, quindi caricarlo.
    
    ![Configure Single Sign-On](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig5.png)
  
7. Fare clic su **Salva modifiche**.

    ![Configure Single Sign-On](./media/wdesk-tutorial/tutorial_wdesk_ssoconfigsavebutton.png)

### <a name="create-wdesk-test-user"></a>Creare l'utente di test di Wdesk

Per consentire agli utenti di Azure AD di accedere a Wdesk, è necessario effettuarne il provisioning in Wdesk. Nel caso di Wdesk il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere a Wdesk come amministratore della sicurezza.

2. Passare ad **Admin** (Amministratore) > **Account Admin** (Amministratore dell'account).

     ![Configure Single Sign-On](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. Fare clic su **Members** (Membri) in **People** (Persone).

4. Fare clic su **Add Member** (Aggiungi membro) per aprire la finestra di dialogo **Add Member** (Aggiungi membro). 
   
    ![Creazione di un utente di test di Azure AD](./media/wdesk-tutorial/createuser1.png)  

5. Nella casella di testo **User** (Utente) immettere il nome utente dell'utente, ad esempio b.simon@contoso.com, quindi fare clic sul pulsante **Continua**.

    ![Creazione di un utente di test di Azure AD](./media/wdesk-tutorial/createuser3.png)

6.  Immettere i dettagli come mostrato di seguito:
  
    ![Creazione di un utente di test di Azure AD](./media/wdesk-tutorial/createuser4.png)
 
    a. Nella casella di testo **E-mail** (Posta elettronica) immettere l'indirizzo di posta elettronica dell'utente, ad esempio b.simon@contoso.com.

    b. Nella casella di testo **First name** (Nome) immettere il nome dell'utente, ad esempio **B**.

    c. Nella casella di testo **Last Name** (Nome) immettere il cognome dell'utente, ad esempio **Simon**.

7. Fare clic sul pulsante **Save Member** (Salva membro).  

    ![Creazione di un utente di test di Azure AD](./media/wdesk-tutorial/createuser5.png)

### <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Wdesk nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Wdesk per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Informazioni sul controllo sessioni in Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)