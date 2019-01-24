---
title: 'Esercitazione: Integrazione di Azure Active Directory con LinkedIn Learning| Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e LinkedIn Learning.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: d5857070-bf79-4bd3-9a2a-4c1919a74946
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/18/2018
ms.author: jeedes
ms.openlocfilehash: be5dd0a0fd1eb45c505a2f0ddf1489d21bfd38e6
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54821186"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-learning"></a>Esercitazione: Integrazione di Azure Active Directory con LinkedIn Learning

Questa esercitazione descrive come integrare LinkedIn Learning con Azure Active Directory (Azure AD).
L'integrazione di LinkedIn Learning con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a LinkedIn Learning.
* È possibile abilitare gli utenti per l'accesso automatico a LinkedIn Learning (Single Sign-On) con i propri account Azure AD.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con LinkedIn Learning sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di LinkedIn Learning abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* LinkedIn Learning supporta il Single Sign-On avviato da **provider di servizi e IDP**
* LinkedIn Learning supporta il provisioning utenti **Just-in-time**

## <a name="adding-linkedin-learning-from-the-gallery"></a>Aggiungere LinkedIn Learning dalla raccolta

Per configurare l'integrazione di LinkedIn Learning in Azure AD è necessario aggiungere LinkedIn Learning dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere LinkedIn Learning dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **LinkedIn Learning**, selezionare **LinkedIn Learning** dal pannello dei risultati quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![LinkedIn Learning nell'elenco dei risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con LinkedIn Learning in base a un utente test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in LinkedIn Learning.

Per configurare e testare l'accesso Single Sign-On di Azure AD con LinkedIn Learning è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di LinkedIn Learning](#configure-linkedin-learning-single-sign-on)**: per configurare le impostazioni dell'accesso Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di LinkedIn Learning](#create-linkedin-learning-test-user)**: per avere una controparte di Britta Simon in LinkedIn Learning collegata alla relativa rappresentazione in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con LinkedIn Learning seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **LinkedIn Learning** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità **avviata da IDP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di LinkedIn Learning](common/idp-intiated.png)

    a. Nella casella di testo **Identificatore** immettere l'**ID entità** copiato dal portale di LinkedIn. 

    b. Nella casella di testo **URL di risposta** immettere l'**URL del servizio consumer di asserzione** copiato dal portale di LinkedIn.

    c. Se si vuole configurare l'applicazione in modalità **avviata da provider di servizi**, fare clic sull'opzione **Impostare URL aggiuntivi** nella sezione **Configurazione SAML di base**, in cui verrà specificato l'URL di accesso. Per creare l'URL di accesso, copiare l'**URL del servizio consumer di asserzione** e sostituire /saml/ con /login/. Dopo aver completato questa operazione, il formato dell'URL di accesso dovrebbe essere il seguente:

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=learning&applicationInstanceId=<InstanceId>`

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di LinkedIn Learning](common/metadata-upload-additional-signon.png)

    > [!NOTE]
    > Dal momento che questi non sono i valori reali, è necessario aggiornarli con i valori reali di identificatore e URL di risposta. La procedura è descritta più avanti nella sezione **Configurare l'accesso Single Sign-On di LinkedIn Learning** dell'esercitazione.

5. L'applicazione LinkedIn Learning richiede le asserzioni SAML in un formato specifico. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. La schermata seguente illustra un esempio relativo a questa operazione. Il valore predefinito dell'**identificatore utente univoco** è **user.userprincipalname** ma LinkedIn Learning prevede che venga mappato all'indirizzo di posta elettronica dell'utente. A tale scopo è possibile usare l'attributo **user.mail** dall'elenco oppure usare il valore di attributo appropriato in base alla configurazione dell'organizzazione. 

    ![image](common/edit-attribute.png)

6. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** modificare le attestazioni usando l'**icona Modifica** o aggiungere le attestazioni usando l'opzione **Aggiungi nuova attestazione** per configurare l'attributo del token SAML come mostrato nell'immagine precedente e seguire questa procedura:
    
    | NOME | Source Attribute |
    | ---------------| --------------- |
    | email  | user.mail  |
    | department  | user.department  |
    | firstname  | user.givenname  |
    | lastname  | user.surname  |

    a. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![image](./media/linkedinlearning-tutorial/tutorial_usermail.png)

    ![image](./media/linkedinlearning-tutorial/tutorial_usermailedit.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Lasciare vuota la casella **Spazio dei nomi**.

    d. Per Origine selezionare **Attributo**.

    e. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo indicato per la riga.

    f. Fare clic su **Ok**

    g. Fare clic su **Save**.

7. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

### <a name="configure-linkedin-learning-single-sign-on"></a>Configurare l'accesso Single Sign-On di LinkedIn Learning

1. In un'altra finestra del Web browser accedere al tenant LinkedIn Learning come amministratore.

2. In **Account Center** (Centro account) fare clic su **Global Settings** (Impostazioni globali) in **Settings** (Impostazioni). Selezionare anche **Learning - Default** (Learning - Predefinito) nell'elenco a discesa.

    ![Configure Single Sign-On](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_01.png)

3. Fare clic su **OR Click Here to load and copy individual fields from the form** (O fare clic qui per caricare e copiare i singoli file dal modulo), copiare l'**ID entità** e l'**URL del servizio consumer di asserzione** e incollarli nella sezione **Configurazione SAML di base** nel portale di Azure.

    ![Configure Single Sign-On](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_03.png)

4. Accedere alla sezione **LinkedIn Admin Settings** (Impostazioni di amministrazione LinkedIn). Caricare il file XML scaricato dal portale di Azure facendo clic sull'opzione **Upload XML file** (Carica file XML).

    ![Configure Single Sign-On](./media/linkedinlearning-tutorial/tutorial_linkedin_metadata_03.png)

5. Fare clic su **On** per abilitare SSO. Lo stato SSO passa da **Not Connected** (Non connesso) a **Connected** (Connesso)

    ![Configure Single Sign-On](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD 

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

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

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a LinkedIn Learning.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **LinkedIn Learning**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni digitare e selezionare **LinkedIn Learning**.

    ![Collegamento a LinkedIn Learning nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-linkedin-learning-test-user"></a>Creare un utente test di LinkedIn Learning

L'applicazione LinkedIn Learning supporta il provisioning dell'utente just-in-time e dopo l'autenticazione gli utenti sono automaticamente creati nell'applicazione. Nella pagina delle impostazioni di amministrazione del portale di LinkedIn Learning spostare il selettore dell'opzione **Automatically Assign licenses** (Assegna automaticamente le licenze) per attivareil provisioning just-in-time, assegnando una licenza all'utente.

   ![Creazione di un utente test di Azure AD](./media/linkedinlearning-tutorial/LinkedinUserprovswitch.png)

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro LinkedIn Learning nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione LinkedIn Learning per cui è stato configurato l'accesso Single Sign-On. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

