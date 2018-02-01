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
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: jeedes
ms.openlocfilehash: 22189c3d2d2164ba0fa3c2d790c36361fb0f5854
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2018
---
# <a name="tutorial-azure-active-directory-integration-with-confluence-saml-sso-by-microsoft"></a>Esercitazione: Integrazione di Azure Active Directory con Confluence SAML SSO by Microsoft

Questa esercitazione descrive come integrare Confluence SAML SSO by Microsoft con Azure Active Directory (Azure AD).

L'integrazione di Confluence SAML SSO by Microsoft con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi ha accesso a Confluence SAML SSO by Microsoft
- È possibile abilitare gli utenti per l'accesso automatico a Confluence SAML SSO by Microsoft, ovvero per il Single Sign-On, con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="description"></a>Descrizione:

Usare l'account di Microsoft Azure Active Directory con il server Atlassian Confluence per abilitare l'accesso Single Sign-On. In questo modo gli utenti dell'organizzazione possono usare le credenziali di Azure AD per accedere all'applicazione Confluence. Questo plug-in usa SAML 2.0 per la federazione.

## <a name="prerequisites"></a>prerequisiti

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

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Confluence SAML SSO by Microsoft dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-confluence-saml-sso-by-microsoft-from-the-gallery"></a>Aggiunta di Confluence SAML SSO by Microsoft dalla raccolta
Per configurare l'integrazione di Confluence SAML SSO by Microsoft in Azure AD è necessario aggiungere Confluence SAML SSO by Microsoft dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Confluence SAML SSO by Microsoft dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![APPLICAZIONI][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![APPLICAZIONI][3]

4. Nella casella di ricerca digitare **Confluence SAML SSO by Microsoft**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_search.png)

5. Nel riquadro dei risultati selezionare **Confluence SAML SSO by Microsoft** e quindi fare clic su **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Confluence SAML SSO by Microsoft mediante un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere quale utente di Confluence SAML SSO by Microsoft corrisponde a un determinato utente di Azure AD. In altre parole è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Confluence SAML SSO by Microsoft.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Confluence SAML SSO by Microsoft è necessario completare le operazioni di base seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test di Confluence SAML SSO by Microsoft](#creating-a-confluence-saml-sso-by-microsoft-test-user)**: per avere una controparte di Britta Simon in Confluence SAML SSO by Microsoft collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Confluence SAML SSO by Microsoft.

**Per configurare l'accesso Single Sign-On di Azure AD con Confluence SAML SSO by Microsoft, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Confluence SAML SSO by Microsoft** del portale di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configure Single Sign-On](./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_samlbase.png)

3. Nella sezione **URL e dominio Confluence SAML SSO by Microsoft** seguire questa procedura:

    ![Configure Single Sign-On](./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<domain:port>/plugins/servlet/saml/auth`

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<domain:port>/`

    c. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. La porta è facoltativa nel caso di un URL denominato. Questi valori vengono ricevuti durante la configurazione del plug-in Confluence, illustrata più avanti nell'esercitazione.
 

4. Per generare l'URL dei **metadati**, seguire questa procedura:

    a. Fare clic su **Registrazioni per l'app**.
    
    ![Configure Single Sign-On](./media/active-directory-saas-Confluencemicrosoft-tutorial/appregistrations.png)
   
    b. Fare clic su **Endpoint** per aprire la finestra di dialogo **Endpoint**.  
    
    ![Configure Single Sign-On](./media/active-directory-saas-Confluencemicrosoft-tutorial/endpointicon.png)

    c. Fare clic sul pulsante Copia per copiare l'URL del **DOCUMENTO METADATI FEDERAZIONE** e incollarlo nel Blocco note.
    
    ![Configure Single Sign-On](./media/active-directory-saas-Confluencemicrosoft-tutorial/endpoint.png)
     
    d. Passare ora alla pagina delle proprietà di **Confluence SAML SSO by Microsoft**, copiare l'**ID applicazione** usando il pulsante **Copia** e incollarlo nel Blocco note.
 
    ![Configure Single Sign-On](./media/active-directory-saas-Confluencemicrosoft-tutorial/appid.png)

    e. Generare l'**URL dei metadati** usando il modello seguente `<FEDERATION METADATA DOCUMENT url>?appid=<application id>` e copiare il valore nel Blocco note, perché verrà usato in seguito per la configurazione del plug-in.

5. Fare clic sul pulsante **Salva** .

    ![Configure Single Sign-On](./media/active-directory-saas-Confluencemicrosoft-tutorial/tutorial_general_400.png)

6. In un'altra finestra del Web browser accedere all'istanza di Confluence come amministratore.

7. Passare il puntatore del mouse sulla rotellina e scegliere **Add-ons** (Componenti aggiuntivi).
    
    ![Configure Single Sign-On](./media/active-directory-saas-Confluencemicrosoft-tutorial/addon1.png)

8. Scaricare il plug-in dall'[Area download Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=56503). Caricare manualmente il plug-in offerto da Microsoft tramite il menu **Upload add-on** (Carica componente aggiuntivo)
    
    ![Configure Single Sign-On](./media/active-directory-saas-Confluencemicrosoft-tutorial/addon12.png)

9. Dopo aver installato il plug-in, questo comparirà nella sezione dei componenti aggiuntivi **User Installed** (Installati dall'utente) della sezione **Manage Add-on** (Gestisci componente aggiuntivo). Fare clic su **Configure** (Configura) per configurare il nuovo plug-in.
    
    ![Configure Single Sign-On](./media/active-directory-saas-Confluencemicrosoft-tutorial/addon13.png)

10. Seguire questa procedura nella pagina di configurazione:

    ![Configure Single Sign-On](./media/active-directory-saas-Confluencemicrosoft-tutorial/addon52.png)
 
    > [!TIP]
    > Assicurarsi che esista un solo certificato mappato per l'app in modo che non si verifichino errori durante la risoluzione dei metadati. Se sono presenti più certificati, l'amministratore riceve un errore durante la risoluzione dei metadati.

    a. In **Metadata URL** (URL metadati) incollare l'**URL dei metadati** generato da Azure AD e fare clic sul pulsante **Resolve** (Risolvi). Viene letto l'URL dei metadati IdP e vengono compilate tutte le informazioni dei campi.

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


> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-confluencemicrosoft-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-confluencemicrosoft-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-confluencemicrosoft-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-confluencemicrosoft-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-confluence-saml-sso-by-microsoft-test-user"></a>Creazione di un utente di test di Confluence SAML SSO by Microsoft

Per consentire agli utenti di Azure AD di accedere a un server Confluence locale, è necessario effettuarne il provisioning in Confluence SAML SSO by Microsoft. In Confluence SAML SSO by Microsoft il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al server locale Confluence come amministratore.

2. Passare il puntatore del mouse e fare clic su **User management** (Gestione utenti).

    ![Aggiungere un dipendente](./media/active-directory-saas-confluencemicrosoft-tutorial/user1.png) 

3. Nella sezione Users (Utenti) fare clic sula scheda **Add users** (Aggiungi utenti). Nella pagina della finestra di dialogo **Add a User** (Aggiungi un utente) eseguire la procedura seguente:

    ![Aggiungere un dipendente](./media/active-directory-saas-confluencemicrosoft-tutorial/user2.png) 

    a. Nella casella di testo **Username** (Nome utente) digitare l'indirizzo di posta elettronica di un utente come Britta Simon.

    b. Nella casella di testo **Full Name** (Nome completo) digitare il nome completo dell'utente, ad esempio Britta Simon.

    c. Nella casella di testo **Email** digitare l'indirizzo di posta elettronica dell'utente, ad esempio Brittasimon@contoso.com.

    d. Nella casella di testo **Password** digitare la password di Britta Simon.

    e. Fare clic sul pulsante **Confirm** (Conferma) e immettere di nuovo la password.
    
    f. Fare clic sul pulsante **Aggiungi**.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure AD concedendole l'accesso a Confluence SAML SSO by Microsoft.

![Assegna utente][200] 

**Per assegnare Britta Simon a Confluence SAML SSO by Microsoft, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Confluence SAML SSO by Microsoft**.

    ![Configure Single Sign-On](./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Confluence SAML SSO by Microsoft nel pannello di accesso si accede automaticamente all'applicazione Confluence SAML SSO by Microsoft.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_203.png

