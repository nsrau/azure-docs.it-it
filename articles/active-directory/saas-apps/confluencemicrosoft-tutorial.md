---
title: 'Esercitazione: integrazione di Azure Active Directory con Confluence SAML SSO by Microsoft | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Confluence SAML SSO by Microsoft.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1ad1cf90-52bc-4b71-ab2b-9a5a1280fb2d
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/05/2018
ms.author: jeedes
ms.openlocfilehash: 2e254faae0289cd00c7e66d430ec3148fccb364a
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2018
ms.locfileid: "51288153"
---
# <a name="tutorial-azure-active-directory-integration-with-confluence-saml-sso-by-microsoft"></a>Esercitazione: Integrazione di Azure Active Directory con Confluence SAML SSO by Microsoft

Questa esercitazione descrive come integrare Confluence SAML SSO by Microsoft con Azure Active Directory (Azure AD).

L'integrazione di Confluence SAML SSO by Microsoft con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi ha accesso a Confluence SAML SSO by Microsoft.
- È possibile abilitare gli utenti per l'accesso automatico a Confluence SAML SSO by Microsoft (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

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
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese: [offerta prova](https://azure.microsoft.com/pricing/free-trial/).

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

In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Confluence SAML SSO by Microsoft dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-confluence-saml-sso-by-microsoft-from-the-gallery"></a>Aggiunta di Confluence SAML SSO by Microsoft dalla raccolta

Per configurare l'integrazione di Confluence SAML SSO by Microsoft in Azure AD è necessario aggiungere Confluence SAML SSO by Microsoft dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Confluence SAML SSO by Microsoft dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Confluence SAML SSO by Microsoft**, selezionare **Confluence SAML SSO by Microsoft** dal pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Confluence SAML SSO by Microsoft nell'elenco dei risultati](./media/confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Confluence SAML SSO by Microsoft mediante un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere quale utente di Confluence SAML SSO by Microsoft corrisponde a un determinato utente di Azure AD. In altre parole è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Confluence SAML SSO by Microsoft.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Confluence SAML SSO by Microsoft è necessario completare le operazioni di base seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test di Confluence SAML SSO by Microsoft](#creating-confluence-saml-sso-by-microsoft-test-user)**: per avere una controparte di Britta Simon in Confluence SAML SSO by Microsoft collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Confluence SAML SSO by Microsoft.

**Per configurare l'accesso Single Sign-On di Azure AD con Confluence SAML SSO by Microsoft, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Confluence SAML SSO by Microsoft** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** fare clic su **Seleziona** per la modalità **SAML** per abilitare l'accesso Single Sign-On.

    ![Configure Single Sign-On](common/tutorial_general_301.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Configure Single Sign-On](common/editconfigure.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni sull'accesso Single Sign-On per il dominio e gli URL di Confluence SAML SSO by Microsoft](./media/confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<domain:port>/plugins/servlet/saml/auth`

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<domain:port>/`

    c. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. La porta è facoltativa nel caso di un URL denominato. Questi valori vengono ricevuti durante la configurazione del plug-in Confluence, illustrata più avanti nell'esercitazione.

5. Nella pagina **Certificato di firma SAML** nella sezione **Certificato di firma SAML** fare clic sul pulsante di copia per copiare il valore di **URL dei metadati di federazione dell'app** e incollarlo in Blocco note.

    ![Collegamento di download del certificato](./media/confluencemicrosoft-tutorial/tutorial_metadataurl.png)

6. In un'altra finestra del Web browser accedere all'istanza di Confluence come amministratore.

7. Passare il puntatore del mouse sulla rotellina e scegliere **Add-ons** (Componenti aggiuntivi).

    ![Configure Single Sign-On](./media/confluencemicrosoft-tutorial/addon1.png)

8. Scaricare il plug-in dall'[Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=56503). Caricare manualmente il plug-in fornito da Microsoft tramite il menu **Upload add-on** (Carica componente aggiuntivo). Il download del plug-in è coperto dal [Contratto di Servizi Microsoft](https://www.microsoft.com/servicesagreement/).

    ![Configure Single Sign-On](./media/confluencemicrosoft-tutorial/addon12.png)

9. Dopo aver installato il plug-in, questo comparirà nella sezione dei componenti aggiuntivi **User Installed** (Installati dall'utente) della sezione **Manage Add-on** (Gestisci componente aggiuntivo). Fare clic su **Configure** (Configura) per configurare il nuovo plug-in.

    ![Configure Single Sign-On](./media/confluencemicrosoft-tutorial/addon13.png)

10. Seguire questa procedura nella pagina di configurazione:

    ![Configure Single Sign-On](./media/confluencemicrosoft-tutorial/addon52.png)

    > [!TIP]
    > Assicurarsi che esista un solo certificato mappato per l'app in modo che non si verifichino errori durante la risoluzione dei metadati. Se sono presenti più certificati, l'amministratore riceve un errore durante la risoluzione dei metadati.

    a. Nella casella di testo **Metadata URL** (URL dei metadati) incollare il valore dell'**URL dei metadati di federazione dell'app** copiato dal portale di Azure e fare clic sul pulsante **Resolve** (Risolvi). Viene letto l'URL dei metadati IdP e vengono compilate tutte le informazioni dei campi.

    b. Copiare i valori di **identificatore, URL di risposta e URL di accesso** e incollarli rispettivamente nelle caselle **Identificatore, URL di risposta e URL di accesso** nella sezione **Dominio e URL Confluence SAML SSO by Microsoft** nel portale di Azure.

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

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Creare un utente di Azure AD][100]

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Creazione di un utente test di Azure AD](common/create_aaduser_01.png) 

3. In Proprietà utente seguire questa procedura.

    ![Creazione di un utente test di Azure AD](common/create_aaduser_02.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon@yourcompanydomain.extension**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare **Proprietà**, selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Selezionare **Create**.

### <a name="creating-confluence-saml-sso-by-microsoft-test-user"></a>Creazione di un utente di test di Confluence SAML SSO by Microsoft

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

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure AD concedendole l'accesso a Confluence SAML SSO by Microsoft.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Assegna utente][201]

2. Nell'elenco delle applicazioni selezionare **Confluence SAML SSO by Microsoft**.

    ![Configure Single Sign-On](./media/confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_app.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Nella finestra di dialogo **Aggiungi assegnazione** selezionare il pulsante **Assegna**.

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Confluence SAML SSO by Microsoft nel pannello di accesso si accede automaticamente all'applicazione Confluence SAML SSO by Microsoft.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
