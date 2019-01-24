---
title: 'Esercitazione: Integrazione di Azure Active Directory con Confluence SAML SSO by Microsoft | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Confluence SAML SSO by Microsoft.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 1ad1cf90-52bc-4b71-ab2b-9a5a1280fb2d
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.openlocfilehash: 3140b3851d1429c6d6a99f9c109725b5f09338c0
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54813832"
---
# <a name="tutorial-azure-active-directory-integration-with-confluence-saml-sso-by-microsoft"></a>Esercitazione: Integrazione di Azure Active Directory con Confluence SAML SSO by Microsoft

Questa esercitazione descrive come integrare Confluence SAML SSO by Microsoft con Azure Active Directory (Azure AD).
L'integrazione di Confluence SAML SSO by Microsoft con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi ha accesso a Confluence SAML SSO by Microsoft.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Confluence SAML SSO by Microsoft con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="description"></a>Descrizione:

Usare l'account di Microsoft Azure Active Directory con il server Atlassian Confluence per abilitare l'accesso Single Sign-On. In questo modo gli utenti dell'organizzazione possono usare le credenziali di Azure AD per accedere all'applicazione Confluence. Questo plug-in usa SAML 2.0 per la federazione.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Confluence SAML SSO by Microsoft sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Applicazione server Confluence installata in un server di Windows a 64 bit (in locale o nell'infrastruttura IaaS cloud)
- Server Confluence abilitato per HTTPS
- Si noti che le versioni supportate per il plug-in Confluence sono indicate nella sezione seguente.
- Il server Confluence deve essere raggiungibile da Internet, in particolare per la pagina di accesso di Azure AD per l'autenticazione e deve essere in grado di ricevere il token da Azure AD
- Credenziali di amministratore configurate in Confluence
- WebSudo disabilitato in Confluence
- Utente di test creato nell'applicazione server Confluence

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione di Confluence per testare i passaggi di questa esercitazione. Testare prima l'integrazione nell'ambiente di sviluppo o di gestione temporanea dell'applicazione e successivamente usare l'ambiente di produzione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si ha un ambiente di valutazione di Azure AD, è possibile ottenere una versione di valutazione di un mese qui: [Offerta per la versione di valutazione](https://azure.microsoft.com/pricing/free-trial/).

## <a name="supported-versions-of-confluence"></a>Versioni di Confluence supportate

A tutt'oggi sono supportate le versioni seguenti di Confluence:

- Confluence: da 5.0 a 5.10
- Confluence: 6.0.1
- Confluence: 6.1.1
- Confluence: 6.2.1
- Confluence: 6.3.4
- Confluence: 6.4.0
- Confluence: 6.5.0
- Confluence: 6.6.2
- Confluence: 6.7.0
- Confluence: 6.8.1
- Confluence: 6.9.0
- Confluence: 6.10.0
- Confluence: 6.11.0
- Confluence: 6.12.0

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Confluence SAML SSO by Microsoft supporta l'accesso SSO avviato da **SP**

## <a name="adding-confluence-saml-sso-by-microsoft-from-the-gallery"></a>Aggiunta di Confluence SAML SSO by Microsoft dalla raccolta

Per configurare l'integrazione di Confluence SAML SSO by Microsoft in Azure AD è necessario aggiungere Confluence SAML SSO by Microsoft dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Confluence SAML SSO by Microsoft dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Confluence SAML SSO by Microsoft**, selezionare **Confluence SAML SSO by Microsoft** dal pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Confluence SAML SSO by Microsoft nell'elenco dei risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Confluence SAML SSO by Microsoft mediante un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Confluence SAML SSO by Microsoft.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Confluence SAML SSO by Microsoft è necessario completare le operazioni di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Confluence SAML SSO by Microsoft](#configure-confluence-saml-sso-by-microsoft-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Confluence SAML SSO by Microsoft](#create-confluence-saml-sso-by-microsoft-test-user)**: per avere una controparte di Britta Simon in Confluence SAML SSO by Microsoft collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Confluence SAML SSO by Microsoft, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Confluence SAML SSO by Microsoft** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni sull'accesso Single Sign-On per il dominio e gli URL di Confluence SAML SSO by Microsoft](common/sp-identifier-reply.png)

    a. Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<domain:port>/plugins/servlet/saml/auth`

    b. Nella casella di testo **Identificatore** digitare un URL usando il modello seguente: `https://<domain:port>/`

    c. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. La porta è facoltativa nel caso di un URL denominato. Questi valori vengono ricevuti durante la configurazione del plug-in Confluence, illustrata più avanti nell'esercitazione.

4. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/copy-metadataurl.png)

### <a name="configure-confluence-saml-sso-by-microsoft-single-sign-on"></a>Configurare l'accesso Single Sign-On di Confluence SAML SSO by Microsoft

1. In un'altra finestra del Web browser accedere all'istanza di Confluence come amministratore.

2. Passare il puntatore del mouse sulla rotellina e scegliere **Add-ons** (Componenti aggiuntivi).

    ![Configure Single Sign-On](./media/confluencemicrosoft-tutorial/addon1.png)

3. Scaricare il plug-in dall'[Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=56503). Caricare manualmente il plug-in fornito da Microsoft tramite il menu **Upload add-on** (Carica componente aggiuntivo). Il download del plug-in è coperto dal [Contratto di Servizi Microsoft](https://www.microsoft.com/servicesagreement/).

    ![Configure Single Sign-On](./media/confluencemicrosoft-tutorial/addon12.png)

4. Dopo aver installato il plug-in, questo comparirà nella sezione dei componenti aggiuntivi **User Installed** (Installati dall'utente) della sezione **Manage Add-on** (Gestisci componente aggiuntivo). Fare clic su **Configure** (Configura) per configurare il nuovo plug-in.

    ![Configure Single Sign-On](./media/confluencemicrosoft-tutorial/addon13.png)

5. Seguire questa procedura nella pagina di configurazione:

    ![Configure Single Sign-On](./media/confluencemicrosoft-tutorial/addon52.png)

    > [!TIP]
    > Assicurarsi che esista un solo certificato mappato per l'app in modo che non si verifichino errori durante la risoluzione dei metadati. Se sono presenti più certificati, l'amministratore riceve un errore durante la risoluzione dei metadati.

    a. Nella casella di testo **Metadata URL** (URL dei metadati) incollare il valore dell'**URL dei metadati di federazione dell'app** copiato dal portale di Azure e fare clic sul pulsante **Resolve** (Risolvi). Viene letto l'URL dei metadati IdP e vengono compilate tutte le informazioni dei campi.

    b. Copiare i valori di **identificatore, URL di risposta e URL di accesso** e incollarli rispettivamente nelle caselle di testo **Identificatore, URL di risposta e URL di accesso** nella sezione **Configurazione SAML di base** del portale di Azure.

    c. In **Login Button Name** (Nome pulsante di accesso) digitare il nome del pulsante che l'organizzazione vuole mostrare agli utenti nella schermata di accesso.

    d. In **SAML User ID Locations** (Posizioni ID utente SAML) selezionare **User ID is in the NameIdentifier element of the Subject statement** (ID utente nell'elemento NameIdentifier dell'istruzione Subject) oppure **User ID is in an Attribute element** (ID utente in un elemento Attribute).  Questo ID deve essere l'ID utente di Confluence. Se non viene trovata una corrispondenza per l'ID utente, il sistema non consentirà agli utenti di accedere. 

    > [!Note]
    > La posizione predefinita dell'ID utente SAML è nell'identificatore del nome. È possibile sostituirlo con un attributo e immettere il nome dell'attributo appropriato.
    
    e. Se si seleziona l'opzione **User ID is in an Attribute element** (ID utente in un elemento Attribute), nella casella di testo **Attribute name** (Nome attributo) digitare il nome dell'attributo per cui è previsto l'ID utente. 

    f. Se si usa un dominio federato, come AD FS o altri, con Azure AD fare clic sull'opzione **Enable Home Realm Discovery** (Abilita individuazione area di autenticazione principale) e configurare **Domain Name** (Nome dominio).
    
    g. In **Domain Name** (Nome dominio) digitare il nome del dominio in caso di accesso basato su AD FS.

    h. Selezionare **Enable Single Sign out** (Abilita Single Sign-Out) per impostare la disconnessione da Azure AD quando un utente si disconnette da Confluence. 

    i. Fare clic sul pulsante **Save** (Salva) per salvare le impostazioni.

    > [!NOTE]
    > Per altre informazioni sull'installazione e la risoluzione dei problemi, vedere [MS Confluence SSO Connector Admin Guide](../ms-confluence-jira-plugin-adminguide.md) (Guida all'amministrazione dei connettori Microsoft per SSO in Confluence), che contiene anche una sezione di [domande frequenti](../ms-confluence-jira-plugin-faq.md) per informazioni utili

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

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure AD concedendole l'accesso a Confluence SAML SSO by Microsoft.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Confluence SAML SSO by Microsoft**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Confluence SAML SSO by Microsoft**.

    ![Collegamento di Confluence SAML SSO by Microsoft nell'elenco Applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-confluence-saml-sso-by-microsoft-test-user"></a>Creare un utente di test di Confluence SAML SSO by Microsoft

Per consentire agli utenti di Azure AD di accedere a un server Confluence locale, è necessario effettuarne il provisioning in Confluence SAML SSO by Microsoft. In Confluence SAML SSO by Microsoft il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al server locale Confluence come amministratore.

2. Passare il puntatore del mouse e fare clic su **User management** (Gestione utenti).

    ![Aggiungere un dipendente](./media/confluencemicrosoft-tutorial/user1.png) 

3. Nella sezione Users (Utenti) fare clic sula scheda **Add users** (Aggiungi utenti). Nella pagina della finestra di dialogo **Add a User** (Aggiungi un utente) eseguire la procedura seguente:

    ![Aggiungere un dipendente](./media/confluencemicrosoft-tutorial/user2.png) 

    a. Nella casella di testo **Username** (Nome utente) digitare l'indirizzo di posta elettronica di un utente come Britta Simon.

    b. Nella casella di testo **Full Name** (Nome completo) digitare il nome completo dell'utente, ad esempio Britta Simon.

    c. Nella casella di testo **Email** digitare l'indirizzo di posta elettronica dell'utente, ad esempio Brittasimon@contoso.com.

    d. Nella casella di testo **Password** digitare la password di Britta Simon.

    e. Fare clic sul pulsante **Confirm** (Conferma) e immettere di nuovo la password.

    f. Fare clic sul pulsante **Aggiungi**.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Confluence SAML SSO by Microsoft nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Confluence SAML SSO by Microsoft per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

