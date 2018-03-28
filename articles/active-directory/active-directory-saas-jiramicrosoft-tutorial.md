---
title: 'Esercitazione: Integrazione di Azure Active Directory con Microsoft Azure Active Directory single sign-on for JIRA | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Microsoft Azure Active Directory single sign-on for JIRA.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4b663047-7f88-443b-97bd-54224b232815
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2018
ms.author: jeedes
ms.openlocfilehash: ceb36b78b72c45e9af59724d1f1c79789ef24b24
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="tutorial-azure-active-directory-integration-with-microsoft-azure-active-directory-single-sign-on-for-jira"></a>Esercitazione: Integrazione di Azure Active Directory con Microsoft Azure Active Directory single sign-on for JIRA

Questa esercitazione descrive come integrare Microsoft Azure Active Directory single sign-on for JIRA con Azure Active Directory (Azure AD).

L'integrazione di Microsoft Azure Active Directory single sign-on for JIRA con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi ha accesso a Microsoft Azure Active Directory single sign-on for JIRA.
- È possibile abilitare gli utenti per l'accesso automatico a Microsoft Azure Active Directory single sign-on for JIRA con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="description"></a>DESCRIZIONE

Usare l'account di Microsoft Azure Active Directory con il server Atlassian JIRA per abilitare l'accesso Single Sign-On. In questo modo gli utenti dell'organizzazione possono usare le credenziali di Azure AD per accedere all'applicazione JIRA. Questo plug-in usa SAML 2.0 per la federazione.

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con Microsoft Azure Active Directory single sign-on for JIRA, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- JIRA Core e Software versioni da 6.0 a 7.2.0 o JIRA Service Desk versioni da 3.0 a 3.2 deve essere installato e configurato nella versione di Windows a 64 bit
- Server JIRA abilitato per HTTPS
- Si noti che le versioni supportate per il plug-in JIRA sono indicate nella sezione seguente.
- Il server JIRA deve essere raggiungibile da Internet, in particolare per l'autenticazione nella pagina di accesso di Azure AD e deve essere in grado di ricevere il token da Azure AD
- Credenziali di amministratore configurate in JIRA
- WebSudo disabilitato in JIRA
- Utente test creato nell'applicazione server JIRA

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione JIRA per testare i passaggi di questa esercitazione. Testare prima di tutto l'integrazione nell'ambiente di sviluppo o di gestione temporanea dell'applicazione e poi usare l'ambiente di produzione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese: [offerta prova](https://azure.microsoft.com/pricing/free-trial/).

## <a name="supported-versions-of-jira"></a>Versioni di JIRA supportate

*   Core e Software JIRA: da 6.0 a 7.2.0
*   JIRA Service Desk: da 3.0 a 3.2
*   JIRA supporta anche la versione 5.2. Per altre informazioni, fare clic su [Microsoft Azure Active Directory single sign-on for JIRA 5.2](./active-directory-saas-jira52microsoft-tutorial.md)

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Microsoft Azure Active Directory single sign-on for JIRA dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-microsoft-azure-active-directory-single-sign-on-for-jira-from-the-gallery"></a>Aggiunta di Microsoft Azure Active Directory single sign-on for JIRA dalla raccolta
Per configurare l'integrazione di Microsoft Azure Active Directory single sign-on for JIRA in Azure AD, è necessario aggiungere Microsoft Azure Active Directory single sign-on for JIRA dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Microsoft Azure Active Directory single sign-on for JIRA dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Microsoft Azure Active Directory single sign-on for JIRA**, selezionare **Microsoft Azure Active Directory single sign-on for JIRA** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Microsoft Azure Active Directory single sign-on for JIRA nell'elenco risultati](.\media\active-directory-saas-msaadssojira-tutorial\tutorial_singlesign-onforjira_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Microsoft Azure Active Directory single sign-on for JIRA tramite un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere quale utente di Microsoft Azure Active Directory single sign-on for JIRA corrisponde a un determinato utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Microsoft Azure Active Directory single sign-on for JIRA.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Microsoft Azure Active Directory single sign-on for JIRA, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di Microsoft Azure Active Directory single sign-on for JIRA](#create-a-microsoft-azure-active-directory-single-sign-on-for-jira-test-user)**: per avere un utente che corrisponde a Britta Simon in Microsoft Azure Active Directory single sign-on for JIRA collegato alla rappresentazione di Azure AD dell'utente.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Microsoft Azure Active Directory single sign-on for JIRA.

**Per configurare l'accesso Single Sign-On di Azure AD con Microsoft Azure Active Directory single sign-on for JIRA, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Microsoft Azure Active Directory single sign-on for JIRA** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](.\media\active-directory-saas-msaadssojira-tutorial\tutorial_singlesign-onforjira_samlbase.png)

3. Nella sezione **URL e dominio Microsoft Azure Active Directory single sign-on for JIRA** seguire questa procedura:

    ![Informazioni Single Sign-On in URL e dominio Microsoft Azure Active Directory single sign-on for JIRA](.\media\active-directory-saas-msaadssojira-tutorial\tutorial_singlesign-onforjira_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<domain:port>/plugins/servlet/saml/auth`

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<domain:port>/`

    c. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. La porta è facoltativa nel caso di un URL denominato. Questi valori vengono ricevuti durante la configurazione del plug Jira descritta più avanti nell'esercitazione.
 
4. Per generare l'URL dei **metadati**, seguire questa procedura:

    a. Fare clic su **Registrazioni per l'app**.
    
    ![Configure Single Sign-On](.\media\active-directory-saas-msaadssojira-tutorial\appregistrations.png)
   
    b. Fare clic su **Endpoint** per aprire la finestra di dialogo **Endpoint**.  
    
    ![Configure Single Sign-On](.\media\active-directory-saas-msaadssojira-tutorial\endpointicon.png)

    c. Fare clic sul pulsante Copia per copiare l'URL del **DOCUMENTO METADATI FEDERAZIONE** e incollarlo nel Blocco note.
    
    ![Configure Single Sign-On](.\media\active-directory-saas-msaadssojira-tutorial\endpoint.png)
     
    d. Passare ora alla pagina delle proprietà di **Microsoft Azure Active Directory single sign-on for JIRA**, copiare l'**ID applicazione** usando il pulsante **Copia** e incollarlo nel Blocco note.
 
    ![Configure Single Sign-On](.\media\active-directory-saas-msaadssojira-tutorial\appid.png)

    e. Generare l'**URL dei metadati** usando il modello seguente `<FEDERATION METADATA DOCUMENT url>?appid=<application id>` e copiare il valore nel Blocco note, perché verrà usato in seguito per la configurazione del plug-in.

5. Fare clic sul pulsante **Salva** .

    ![Configure Single Sign-On](.\media\active-directory-saas-msaadssojira-tutorial\tutorial_general_400.png)

6. In un'altra finestra del Web browser accedere all'istanza di JIRA come amministratore.

7. Passare il puntatore del mouse sulla rotellina e scegliere **Add-ons** (Componenti aggiuntivi).
    
    ![Configure Single Sign-On](.\media\active-directory-saas-msaadssojira-tutorial\addon1.png)

8. Scaricare il plug-in dall'[Area download Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=56506). Caricare manualmente il plug-in fornito da Microsoft tramite il menu **Upload add-on** (Carica componente aggiuntivo). Il download del plug-in è coperto dal [Contratto di Servizi Microsoft](https://www.microsoft.com/en-us/servicesagreement/).

    ![Configure Single Sign-On](.\media\active-directory-saas-msaadssojira-tutorial\addon12.png)

9. Dopo aver installato il plug-in, questo comparirà nella sezione dei componenti aggiuntivi **User Installed** (Installati dall'utente) della sezione **Manage Add-on** (Gestisci componente aggiuntivo). Fare clic su **Configure** (Configura) per configurare il nuovo plug-in.

    ![Configure Single Sign-On](.\media\active-directory-saas-msaadssojira-tutorial\addon13.png)

10. Seguire questa procedura nella pagina di configurazione:

    ![Configure Single Sign-On](.\media\active-directory-saas-msaadssojira-tutorial\addon52.png)

    > [!TIP]
    > Assicurarsi che esista un solo certificato mappato per l'app in modo che non si verifichino errori durante la risoluzione dei metadati. Se sono presenti più certificati, durante la risoluzione dei metadati, l'amministratore riceve un errore.
 
    a. In **Metadata URL** (URL metadati) incollare l'**URL dei metadati** generato da Azure AD e fare clic sul pulsante **Resolve** (Risolvi). Viene letto l'URL dei metadati IdP e vengono compilate tutte le informazioni dei campi.

    b. Copiare i valori di **identificatore, URL di risposta e URL di accesso** e incollarli rispettivamente nelle caselle **Identificatore, URL di risposta e URL di accesso** nella sezione **URL e dominio Microsoft Azure Active Directory single sign-on for JIRA** nel portale di Azure.

    c. In **Login Button Name** (Nome pulsante di accesso) digitare il nome del pulsante che l'organizzazione vuole mostrare agli utenti nella schermata di accesso.

    d. In **SAML User ID Locations** (Posizioni ID utente SAML) selezionare **User ID is in the NameIdentifier element of the Subject statement** (ID utente nell'elemento NameIdentifier dell'istruzione Subject) oppure **User ID is in an Attribute element** (ID utente in un elemento Attribute).  Questo ID deve essere l'ID utente di JIRA. Se non viene trovata una corrispondenza per l'ID utente, il sistema non consentirà agli utenti di accedere. 

    > [!Note]
    > La posizione predefinita dell'ID utente SAML è nell'identificatore del nome. È possibile sostituirlo con un attributo e immettere il nome dell'attributo appropriato.

    e. Se si seleziona l'opzione **User ID is in an Attribute element** (ID utente in un elemento Attribute), nella casella di testo **Attribute name** (Nome attributo) digitare il nome dell'attributo per cui è previsto l'ID utente. 

    f. Se si usa un dominio federato, come AD FS o altri, con Azure AD fare clic sull'opzione **Enable Home Realm Discovery** (Abilita individuazione area di autenticazione principale) e configurare **Domain Name** (Nome dominio).
    
    g. In **Domain Name** (Nome dominio) digitare il nome del dominio in caso di accesso basato su AD FS.

    h. Selezionare **Enable Single Sign out** (Abilita Single Sign-Out) per impostare la disconnessione da Azure AD quando un utente si disconnette da JIRA. 

    i. Fare clic sul pulsante **Save** (Salva) per salvare le impostazioni.

    > [!NOTE]
    > Per altre informazioni sull'installazione e la risoluzione dei problemi, vedere [MS JIRA SSO Connector Admin Guide](ms-confluence-jira-plugin-adminguide.md) (Guida all'amministrazione dei connettori Microsoft per SSO in JIRA), che contiene anche una sezione di [domande frequenti](ms-confluence-jira-plugin-faq.md) per informazioni utili

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](.\media\active-directory-saas-msaadssojira-tutorial\create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](.\media\active-directory-saas-msaadssojira-tutorial\create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](.\media\active-directory-saas-msaadssojira-tutorial\create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](.\media\active-directory-saas-msaadssojira-tutorial\create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-a-microsoft-azure-active-directory-single-sign-on-for-jira-test-user"></a>Creare un utente di test di Microsoft Azure Active Directory single sign-on for JIRA

Per consentire agli utenti di Azure AD di accedere a un server JIRA locale, è necessario effettuarne il provisioning in Microsoft Azure Active Directory single sign-on for JIRA. Per Microsoft Azure Active Directory single sign-on for JIRA, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al server locale JIRA come amministratore.

2. Passare il puntatore del mouse e fare clic su **User management** (Gestione utenti).

    ![Aggiungere un dipendente](.\media\active-directory-saas-msaadssojira-tutorial\user1.png) 

3. Si verrà reindirizzati alla pagina Administrator Access (Accesso amministratore) per inserire la **Password** e fare clic sul pulsante **Confirm** (Conferma).

    ![Aggiungere un dipendente](.\media\active-directory-saas-msaadssojira-tutorial\user2.png) 

4. Nella sezione della scheda **User management** (Gestione utenti) fare clic su **Create user** (Crea utente).

    ![Aggiungere un dipendente](.\media\active-directory-saas-msaadssojira-tutorial\user3.png) 

5. Nella pagina della finestra di dialogo **"Create New User"** (Crea nuovo utente) effettuare le operazioni seguenti:

    ![Aggiungere un dipendente](.\media\active-directory-saas-msaadssojira-tutorial\user4.png) 

    a. Nella casella di testo **Email address** (Indirizzo di posta elettronica) digitare l'indirizzo di posta elettronica dell'utente, ad esempio Brittasimon@contoso.com.

    b. Nella casella di testo **Full Name** (Nome completo) digitare il nome completo dell'utente, ad esempio Britta Simon.

    c. Nella casella di testo **Username** (Nome utente) digitare l'indirizzo di posta elettronica di un utente, ad esempio Brittasimon@contoso.com.

    d. Nella casella di testo **Password** digitare la password dell'utente.

    e. Fare clic su **Create User** (Crea utente).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Microsoft Azure Active Directory single sign-on for JIRA.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Microsoft Azure Active Directory single sign-on for JIRA dalla raccolta, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **Microsoft Azure Active Directory single sign-on for JIRA**.

    ![Collegamento Microsoft Azure Active Directory single sign-on for JIRA nell'elenco di applicazioni](.\media\active-directory-saas-msaadssojira-tutorial\tutorial_singlesign-onforjira_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Microsoft Azure Active Directory single sign-on for JIRA nel riquadro di accesso, si accederà automaticamente all'applicazione Microsoft Azure Active Directory single sign-on for JIRA.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: .\media\active-directory-saas-msaadssojira-tutorial\tutorial_general_01.png
[2]: .\media\active-directory-saas-msaadssojira-tutorial\tutorial_general_02.png
[3]: .\media\active-directory-saas-msaadssojira-tutorial\tutorial_general_03.png
[4]: .\media\active-directory-saas-msaadssojira-tutorial\tutorial_general_04.png

[100]: .\media\active-directory-saas-msaadssojira-tutorial\tutorial_general_100.png

[200]: .\media\active-directory-saas-msaadssojira-tutorial\tutorial_general_200.png
[201]: .\media\active-directory-saas-msaadssojira-tutorial\tutorial_general_201.png
[202]: .\media\active-directory-saas-msaadssojira-tutorial\tutorial_general_202.png
[203]: .\media\active-directory-saas-msaadssojira-tutorial\tutorial_general_203.png
