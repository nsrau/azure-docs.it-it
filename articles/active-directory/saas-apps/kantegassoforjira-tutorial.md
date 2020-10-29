---
title: 'Esercitazione: Integrazione di Azure Active Directory con Kantega SSO for JIRA | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Kantega SSO for JIRA.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: 7d32fdd77fded90dc998db645aa6dcac2b502eb2
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92459170"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-jira"></a>Esercitazione: Integrazione di Azure Active Directory con Kantega SSO for JIRA

Questa esercitazione descrive come integrare Kantega SSO for JIRA con Azure Active Directory (Azure AD).
L'integrazione di Kantega SSO for JIRA con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Kantega SSO for JIRA.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Kantega SSO for JIRA con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisites

Per configurare l'integrazione di Azure AD con Kantega SSO for JIRA, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Kantega SSO for JIRA abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Kantega SSO for JIRA supporta l'accesso SSO avviato da **SP e IDP**

## <a name="adding-kantega-sso-for-jira-from-the-gallery"></a>Aggiunta di Kantega SSO for JIRA dalla raccolta

Per configurare l'integrazione di Kantega SSO for JIRA in Azure AD, è necessario aggiungere Kantega SSO for JIRA dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Kantega SSO for JIRA dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni** .

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Kantega SSO for JIRA** , selezionare **Kantega SSO for JIRA** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Kantega SSO for JIRA nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Kantega SSO for JIRA usando un utente di test di nome **Britta Simon** .
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Kantega SSO for JIRA.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Kantega SSO for JIRA, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Kantega SSO for JIRA](#configure-kantega-sso-for-jira-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di Kantega SSO for JIRA](#create-kantega-sso-for-jira-test-user)** : per avere una controparte di Britta Simon in Kantega SSO for JIRA collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Kantega SSO for JIRA, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Kantega SSO for JIRA** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On** .

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base** .

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP** :

    ![Screenshot che mostra la sezione "Configurazione SAML di base" con le caselle di testo "Identificatore" e "U R L di risposta" evidenziate e il pulsante "Salva" selezionato.](common/idp-intiated.png)

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP** :

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Kantega SSO for JIRA](common/metadata-upload-additional-signon.png)

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Questi valori vengono ricevuti durante la configurazione del plug Jira descritta più avanti nell'esercitazione.

6. Nella pagina **Configura l'accesso Single Sign-On con SAML** , nella sezione **Certificato di firma SAML** , fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

7. Nella sezione **Configura Kantega SSO for JIRA** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-kantega-sso-for-jira-single-sign-on"></a>Configurare l'accesso Single Sign-On di Kantega SSO for JIRA

1. In un'altra finestra del Web browser accedere al server locale JIRA come amministratore.

1. Passare il puntatore del mouse sulla rotellina e scegliere **Add-ons** (Componenti aggiuntivi).

    ![Screenshot che mostra l'icona della rotellina selezionata e l'opzione "Add-ons" selezionata nel menu a discesa.](./media/kantegassoforjira-tutorial/addon1.png)

1. Nella sezione della scheda Add-ons (Componenti aggiuntivi) fare clic su **Find new add-ons** (Trova nuovi componenti aggiuntivi). Cercare **Kantega SSO for JIRA (SAML & Kerberos)** e fare clic su **Install** (Installa) per installare il nuovo plug-in di SAML.

    ![Screenshot che mostra la sezione "Find new Add-ons" con "Kantega S S O for JIRA (S A M L & Kerberos)" nella casella di ricerca e il pulsante "Install" selezionato.](./media/kantegassoforjira-tutorial/addon2.png)

1. Viene avviata l'installazione del plug-in.

    ![Screenshot che mostra la finestra di dialogo di installazione del plug-in.](./media/kantegassoforjira-tutorial/addon3.png)

1. Al termine dell'installazione, Fare clic su **Close** .

    ![Screenshot che mostra la finestra di dialogo "Installed and ready to go!" con l'azione "Close" selezionata.](./media/kantegassoforjira-tutorial/addon33.png)

1.  Fare clic su **Manage** .

    ![Screenshot che mostra la pagina dell'app "Kantega S S O" con il pulsante "Manage" selezionato.](./media/kantegassoforjira-tutorial/addon34.png)
    
1. Il nuovo plug-in è visualizzato in **INTEGRATIONS** (INTEGRAZIONI). Fare clic su **Configure** (Configura) per configurare il nuovo plug-in.

    ![Screenshot che mostra "INTEGRATIONS" nel menu di spostamento a sinistra evidenziato e il pulsante "Configure" selezionato nella sezione "Manage add-ons".](./media/kantegassoforjira-tutorial/addon35.png)

1. Nella sezione **SAML** Selezionare **Azure Active Directory (Azure AD)** dall'elenco a discesa **Add identity provider** (Aggiungi provider di identità).

    ![Screenshot che mostra l'elenco a discesa "Add Identity provider" con "Azure Active Directory (Azure AD)" selezionato.](./media/kantegassoforjira-tutorial/addon4.png)

1. Selezionare il livello di sottoscrizione **Basic** (Di base).

    ![Screenshot che mostra la sezione "Preparing Azure A D" con l'opzione "Basic" selezionata.](./media/kantegassoforjira-tutorial/addon5.png)     

1. Nella sezione **App properties** (Proprietà app) seguire questa procedura: 

    ![Screenshot che mostra la sezione "App properties" con la casella di testo "App I D U R L" evidenziata, il pulsante Copy evidenziato e il pulsante "Next" selezionato.](./media/kantegassoforjira-tutorial/addon6.png)

    a. Copiare il valore di **App ID URI** (URI ID App) e usarlo come valore di **Identificatore, URL di risposta e URL di accesso** nella sezione **Configurazione SAML di base** del portale di Azure.

    b. Fare clic su **Avanti** .

1. Nella sezione **Metadata import** (Importazione metadati) seguire questa procedura: 

    ![Screenshot che mostra la sezione "Metadata import" con l'opzione "Metadata file on my computer" selezionata.](./media/kantegassoforjira-tutorial/addon7.png)

    a. Selezionare **Metadata file on my computer** (File metadati in questo computer) e caricare il file di metadati scaricato dal portale di Azure.

    b. Fare clic su **Avanti** .

1. Nella sezione **Name and SSO location** (Nome e percorso SSO) seguire questa procedura:

    ![Screenshot che mostra la sezione "Name and S S O location" con la casella di testo "Identity provider name" evidenziata e il pulsante "Next" selezionato.](./media/kantegassoforjira-tutorial/addon8.png)

    a. Aggiungere il nome del provider di identità nella casella di testo **Identity provider name** (Nome provider di identità), ad esempio Azure AD.

    b. Fare clic su **Avanti** .

1. Verificare il certificato di firma e fare clic su **Next** (Avanti).

    ![Screenshot che mostra la sezione "Signature verification" con il pulsante "Next" selezionato.](./media/kantegassoforjira-tutorial/addon9.png)

1. Nella sezione **JIRA user accounts** (Account utente JIRA) seguire questa procedura:

    ![Screenshot che mostra la sezione "JIRA user accounts" con l'opzione "Create users in JIRA's Internal Directory if needed" evidenziata e il pulsante "Next" selezionato.](./media/kantegassoforjira-tutorial/addon10.png)

    a. Selezionare **Create users in JIRA's internal Directory if needed** (Crea utenti nella directory interna di JIRA se necessario) e immettere il nome appropriato del gruppo per gli utenti (è possibile specificare più gruppi separati da virgola).

    b. Fare clic su **Avanti** .

1. Fare clic su **Fine** .

    ![Screenshot che mostra la sezione "Summary" con il pulsante "Finish" selezionato.](./media/kantegassoforjira-tutorial/addon11.png)

1. Nella sezione **Known domains for Azure AD** (Domini noti per Azure AD) seguire questa procedura:

    ![Configure Single Sign-On](./media/kantegassoforjira-tutorial/addon12.png)

    a. Selezionare **Known domains** (Domini noti) dal pannello sinistro della pagina.

    b. Immettere il nome di dominio nella casella di testo **Known domains** (Domini noti).

    c. Fare clic su **Salva** .

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory** , **Utenti** e quindi **Tutti gli utenti** .

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon** .
  
    b. Nel campo **Nome utente** digitare `brittasimon@yourcompanydomain.extension`. Ad esempio, usare BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Crea** .

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Kantega SSO for JIRA.

1. Nel portale di Azure selezionare **Applicazioni aziendali** , quindi **Tutte le applicazioni** e infine **Kantega SSO for JIRA** .

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **Kantega SSO for JIRA** .

    ![Collegamento di Kantega SSO for JIRA nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione** .

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna** .

### <a name="create-kantega-sso-for-jira-test-user"></a>Creare l'utente di test di Kantega SSO for JIRA

Per consentire agli utenti di Azure AD di accedere a JIRA, è necessario effettuarne il provisioning in JIRA. In Kantega SSO for JIRA il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al server locale JIRA come amministratore.

1. Passare il puntatore del mouse e fare clic su **User management** (Gestione utenti).

    ![Screenshot che mostra l'icona della rotellina selezionata e l'opzione "User management" selezionata nel menu a discesa.](./media/kantegassoforjira-tutorial/user1.png) 

1. Nella sezione della scheda **User management** (Gestione utenti) fare clic su **Create user** (Crea utente).

    ![Screenshot che mostra la sezione "User management" con il pulsante "Create user" selezionato.](./media/kantegassoforjira-tutorial/user2.png) 

1. Nella pagina della finestra di dialogo **"Create New User"** (Crea nuovo utente) effettuare le operazioni seguenti:

    ![Aggiungere un dipendente](./media/kantegassoforjira-tutorial/user3.png) 

    a. Nella casella di testo **Email address** (Indirizzo di posta elettronica) digitare l'indirizzo di posta elettronica dell'utente, ad esempio Brittasimon@contoso.com.

    b. Nella casella di testo **Full Name** (Nome completo) digitare il nome completo dell'utente, ad esempio Britta Simon.

    c. Nella casella di testo **Username** (Nome utente) digitare l'indirizzo di posta elettronica di un utente, ad esempio Brittasimon@contoso.com.

    d. Nella casella di testo **Password** digitare la password dell'utente.

    e. Fare clic su **Create User** (Crea utente).

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Kantega SSO for JIRA nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Kantega SSO for JIRA per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](./tutorial-list.md)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)