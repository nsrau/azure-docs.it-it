---
title: 'Esercitazione: Integrazione di Azure Active Directory con SAML SSO for Jira by resolution GmbH | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SAML SSO for Jira di resolution GmbH.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 20e18819-e330-4e40-bd8d-2ff3b98e035f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/03/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 67be6251b1500ff85b833bbb7c7348a76045496f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67092073"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Esercitazione: Integrazione di Azure Active Directory con SAML SSO for Jira by resolution GmbH

Questa esercitazione descrive come integrare SAML SSO for Jira by resolution GmbH con Azure Active Directory (Azure AD).
L'integrazione di SAML SSO for Jira di resolution GmbH con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Jira con il plug-in SAML SSO by resolution GmbH.
* È possibile abilitare gli utenti per l'accesso automatico a Jira (Single Sign-On) con gli account Azure AD personali usando SAML SSO for Jira by resolution GmbH.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con SAML SSO for Jira by resolution GmbH, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di SAML SSO for Jira by resolution GmbH abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* SAML SSO for Jira by resolution GmbH supporta l'accesso SSO avviato da **SP** e **IDP**

## <a name="adding-an-enterprise-application-for-single-sign-on"></a>Aggiunta di un'applicazione aziendale per l'accesso SSO

Per configurare l'accesso Single Sign-On di Azure AD, è necessario aggiungere una nuova applicazione aziendale. Nella raccolta è disponibile un set di impostazioni per l'applicazione preconfigurato a questo scopo, **SAML SSO for Jira by resolution GmbH**.

**Per aggiungere SAML SSO for Jira di resolution GmbH dalla raccolta, eseguire la procedura seguente:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi fare clic su **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere la nuova applicazione, fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **SAML SSO for Jira by resolution GmbH**, selezionare **SAML SSO for Jira by resolution GmbH** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione. È anche possibile modificare il nome dell'app aziendale.

     ![SAML SSO for Jira by resolution GmbH nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-single-sign-on-with-the-saml-sso-plugin-and-azure-ad"></a>Configurare e testare l'accesso Single Sign-On con il plug-in SAML SSO e Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On a Jira per un utente di Azure AD. L'operazione viene eseguita usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SAML SSO for Jira by resolution GmbH.

Per configurare e testare l'accesso Single Sign-On, è necessario completare i passaggi seguenti:

1. **[Configurare l'applicazione aziendale di Azure AD per l'accesso Single Sign-On](#configure-the-azure-ad-enterprise-application-for-single-sign-on)** : configurare l'applicazione aziendale di Azure AD per l'accesso Single Sign-On.
2. **[Configurare il plug-in SAML SSO dell'istanza di Jira](#configure-the-saml-sso-plugin-of-your-jira-instance)** : configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : creare un utente di test in Azure AD.
1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : consentire all'utente di test di usare l'accesso Single Sign-On sul lato Azure.
1. **[Creare l'utente di test in Jira](#create-the-test-user-also-in-jira)** : creare un utente di test controparte in Jira per l'utente di test di Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** : verificare se la configurazione funziona.

### <a name="configure-the-azure-ad-enterprise-application-for-single-sign-on"></a>Configurare l'applicazione aziendale di Azure AD per l'accesso Single Sign-On

In questa sezione si configura l'accesso Single Sign-On nel portale di Azure.

Per configurare l'accesso Single Sign-On con SAML SSO for Jira by resolution GmbH, seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com/), nell'applicazione aziendale **SAML SSO for Jira by resolution GmbH** appena creata, selezionare **Single Sign-On** nel pannello a sinistra.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. In **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML** per abilitare l'accesso Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. In seguito, fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura, se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di SAML SSO for Jira by resolution GmbH](common/idp-intiated.png)

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<server-base-url>/plugins/servlet/samlsso`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura, se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di SAML SSO for Jira by resolution GmbH](common/metadata-upload-additional-signon.png)

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Per l'identificatore, l'URL di risposta e l'URL di accesso, sostituire **\<server-base-url>** con l'URL di base dell'istanza di Jira. È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure. In caso di problemi, contattare il [team di supporto del client di SAML SSO for Jira by resolution GmbH](https://www.resolution.de/go/support).

4. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, scaricare il file **XML dei metadati della federazione** e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/metadataxml.png)

### <a name="configure-the-saml-sso-plugin-of-your-jira-instance"></a>Configurare il plug-in SAML SSO dell'istanza di Jira 

1. In un'altra finestra del Web browser accedere all'istanza di Jira come amministratore.

2. Passare il puntatore sull'icona dell'ingranaggio a destra e fare clic su **Manage apps** (Gestisci app).
    
    ![Configure Single Sign-On](./media/samlssojira-tutorial/addon1.png)

3. Se si viene reindirizzati alla pagina Administrator Access (Accesso amministratore), immettere la **password** e fare clic sul pulsante **Confirm** (Conferma).

    ![Configure Single Sign-On](./media/samlssojira-tutorial/addon2.png)

4. In genere, Jira reindirizza l'utente al Marketplace di Atlassian. In caso contrario, fare clic su **Find new apps** (Trova nuove app) nel pannello a sinistra. Cercare **SAML Single Sign On (SSO) for JIRA** e fare clic sul pulsante **Install** (Installa) per installare il plug-in di SAML.

    ![Configure Single Sign-On](./media/samlssojira-tutorial/store.png)

5. Viene avviata l'installazione del plug-in. Al termine, fare clic sul pulsante **Close** (Chiudi).

    ![Configure Single Sign-On](./media/samlssojira-tutorial/store-2.png)

    ![Configure Single Sign-On](./media/samlssojira-tutorial/store-3.png)

6. Fare quindi clic su **Manage** (Gestisci).

    ![Configure Single Sign-On](./media/samlssojira-tutorial/store-4.png)
    
8. In seguito, fare clic su **Configure** (Configura) per configurare il plug-in appena installato.

    ![Configure Single Sign-On](./media/samlssojira-tutorial/store-5.png)

9. Nella procedura guidata **SAML Single Sign-On Plugin Configuration** (Configurazione del plug-in SAML Single Sign-On) fare clic sul pulsante **Add new IdP** (Aggiungi nuovo provider di identità) per configurare Azure AD come nuovo provider di identità.

    ![Configure Single Sign-On](./media/samlssojira-tutorial/addon4.png) 

10. Nella pagina **Choose your SAML Identity Provider** (Scegliere il provider di identità SAML) seguire questa procedura:

    ![Configure Single Sign-On](./media/samlssojira-tutorial/addon5a.png)
 
    a. Impostare **AD Azure** come tipo di provider di identità.
    
    b. Aggiungere il **nome** del provider di identità, ad esempio Azure AD.
    
    c. Aggiungere una **descrizione** facoltativa del provider di identità, ad esempio Azure AD.
    
    d. Fare clic su **Avanti**.
    
11. Nella pagina **Identity provider configuration** (Configurazione provider di identità), fare clic su **Next** (Avanti).
 
    ![Configure Single Sign-On](./media/samlssojira-tutorial/addon5b.png)

12. Nella pagina **Import SAML IdP Metadata** (Importa metadati provider di identità SAML) eseguire la procedura seguente:

    ![Configure Single Sign-On](./media/samlssojira-tutorial/addon5c.png)

    a. Fare clic sul pulsante **Select Metadata XML File** (Seleziona file XML dei metadati) e selezionare il file **XML dei metadati della federazione** scaricato in precedenza.

    b. Fare clic sul pulsante **Import** (Importa).
     
    c. Attendere il completamento dell'importazione.  
     
    d. Fare clic sul pulsante **Next** (Avanti).
    
13. Nella pagina **User ID attribute and transformation** (Attributo e trasformazione ID utente) fare clic sul pulsante **Next** (Avanti).

    ![Configure Single Sign-On](./media/samlssojira-tutorial/addon5d.png)
    
14. Nella pagina **User creation and update** (Creazione e aggiornamento utente) fare clic su **Save & Next** (Salva e continua) per salvare le impostazioni.
    
    ![Configure Single Sign-On](./media/samlssojira-tutorial/addon6a.png)
    
15. Nella pagina **Test your settings** (Test impostazioni) fare clic su **Skip test & configure manually** (Ignora test e configura manualmente) per ignorare il test dell'utente. Il test verrà eseguito nella sezione successiva e richiede alcune impostazioni nel portale di Azure.
    
    ![Configure Single Sign-On](./media/samlssojira-tutorial/addon6b.png)
    
16. Fare clic su **OK** per ignorare l'avviso.
    
    ![Configure Single Sign-On](./media/samlssojira-tutorial/addon6c.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure. L'utente verrà usato per testare l'accesso Single Sign-On.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Scegliere **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In **Proprietà utente** seguire questa procedura:

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **Britta Simon**.
  
    b. Nel campo **Nome utente** immettere <b>BrittaSimon@contoso.com</b>.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione aggiungere Britta Simon all'applicazione aziendale per abilitare l'utente all'uso dell'accesso Single Sign-On.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**. 

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni cercare l'applicazione aziendale creata all'inizio di questa esercitazione. Se si sono seguiti i passaggi dell'esercitazione, il nome dell'applicazione è **SAML SSO for Jira by resolution GmbH**. Se si è assegnato un altro nome all'applicazione, cercare tale nome.

    ![Collegamento di SAML SSO for Jira by resolution GmbH nell'elenco Applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** nel pannello a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-the-test-user-also-in-jira"></a>Creare l'utente di test in Jira

Per consentire agli utenti di Azure AD di accedere a SAML SSO for Jira by resolution GmbH, è necessario eseguire il provisioning in SAML SSO for Jira by resolution GmbH. Nel caso specifico di questa esercitazione, è necessario eseguire il provisioning manualmente. Sono tuttavia disponibili anche altri modelli di provisioning per il plug-in SAML SSO by resolution, ad esempio il provisioning **Just-In-Time**. Vedere la relativa documentazione in [SAML SSO di resolution GmbH](https://wiki.resolution.de/doc/saml-sso/latest/all). In caso di domande, contattare il [supporto di resolution](https://www.resolution.de/go/support).

**Per eseguire il provisioning di un account utente manualmente, seguire questa procedura:**

1. Accedere all'istanza di Jira come amministratore.

2. Passare il puntatore sull'icona dell'ingranaggio e selezionare **User management** (Gestione utenti).

   ![Aggiungere un dipendente](./media/samlssojira-tutorial/user1.png)

3. Se si viene reindirizzati alla pagina Administrator Access (Accesso amministratore), immettere la **password** e fare clic sul pulsante **Confirm** (Conferma).

    ![Aggiungere un dipendente](./media/samlssojira-tutorial/user2.png) 

4. Nella sezione della scheda **User management** (Gestione utenti) fare clic su **Create user** (Crea utente).

    ![Aggiungere un dipendente](./media/samlssojira-tutorial/user3-new.png) 

5. Nella pagina della finestra di dialogo **Create New User** (Crea nuovo utente) effettuare le operazioni seguenti. È necessario creare l'utente esattamente come in Azure AD:

    ![Aggiungere un dipendente](./media/samlssojira-tutorial/user4-new.png) 

    a. Nella casella di testo **Email address** (Indirizzo di posta elettronica) digitare l'indirizzo di posta elettronica dell'utente: <b>BrittaSimon@contoso.com</b>.

    b. Nella casella di testo **Full Name** (Nome completo) digitare il nome completo dell'utente: **Britta Simon**.

    c. Nella casella di testo **Username** (Nome utente) digitare l'indirizzo di posta elettronica dell'utente: <b>BrittaSimon@contoso.com</b>. 

    d. Nella casella di testo **Password** immettere la password dell'utente.

    e. Fare clic su **Create user** (Crea utente) per completare la creazione dell'utente.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di SAML SSO for Jira by resolution GmbH nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione SAML SSO for Jira by resolution GmbH per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

È anche possibile testare l'accesso Single Sign-On all'indirizzo [https://\<server-base-url>/plugins/servlet/samlsso](https://\<server-base-url>/plugins/servlet/samlsso). Sostituire **\<server-base-url>** con l'URL di base dell'istanza di Jira.


## <a name="enable-single-sign-on-redirection-for-jira"></a>Abilitare il reindirizzamento Single Sign-On per Jira

Come indicato nella sezione precedente, esistono attualmente due modi per attivare l'accesso Single Sign-On. Tramite il **portale di Azure** o tramite **un collegamento specifico all'istanza di Jira**. Il plug-in SAML SSO di resolution GmbH consente inoltre di attivare l'accesso Single Sign-On semplicemente tramite l'**accesso a qualsiasi URL che punta all'istanza di Jira**.

In sostanza, tutti gli utenti che accedono a Jira verranno reindirizzati all'accesso Single Sign-On dopo aver attivato un'opzione del plug-in.

Per attivare il reindirizzamento SSO, eseguire le operazioni seguenti nell'**istanza di Jira**:

1. Accedere alla pagina di configurazione del plug-in SAML SSO in Jira.
1. Fare clic su **Redirection** (Reindirizzamento) nel riquadro sinistro.
![](./media/samlssojira-tutorial/ssore1.png)

1. Contrassegnare con un segno di spunta **Enable SSO Redirect** (Abilita il reindirizzamento SSO).
![](./media/samlssojira-tutorial/ssore2.png) 

1. Scegliere il pulsante **Save Settings** (Salva impostazioni) nell'angolo in alto a destra.

Dopo aver attivato l'opzione, è comunque possibile tornare al prompt di nome utente/password, se l'opzione **Enable nosso** (Abilita nosso) è contrassegnata con un segno di spunta, passando a [https://\<server-base-url>/login.jsp?nosso](https://\<server-base-url>/login.jsp?nosso). Come nei casi precedenti, sostituire **\<server-base-url>** con l'URL di base.


## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

