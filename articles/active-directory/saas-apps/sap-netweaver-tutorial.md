---
title: 'Esercitazione: Integrazione di Azure Active Directory con SAP NetWeaver | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SAP NetWeaver.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1b9e59e3-e7ae-4e74-b16c-8c1a7ccfdef3
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: jeedes
ms.openlocfilehash: fac22508e679c1e1c93ec62a5b120ba9c7c52317
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2018
ms.locfileid: "52162359"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-netweaver"></a>Esercitazione: Integrazione di Azure Active Directory con SAP NetWeaver

Questa esercitazione descrive come integrare SAP NetWeaver con Azure Active Directory (Azure AD).

L'integrazione di SAP NetWeaver con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a SAP NetWeaver.
- È possibile abilitare gli utenti per l'accesso automatico a SAP NetWeaver (Single Sign-On) con gli account Azure AD personali.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con SAP NetWeaver, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di SAP NetWeaver abilitata per l'accesso Single Sign-On
- SAP NetWeaver V7.20 minimo

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.
Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di SAP NetWeaver dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-sap-netweaver-from-the-gallery"></a>Aggiunta di SAP NetWeaver dalla raccolta

Per configurare l'integrazione di SAP NetWeaver in Azure AD, è necessario aggiungere SAP NetWeaver dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere SAP NetWeaver dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **SAP NetWeaver**, selezionare **SAP NetWeaver** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![SAP NetWeaver nell'elenco risultati](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con SAP NetWeaver con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di SAP NetWeaver che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SAP NetWeaver.

Per configurare e testare l'accesso Single Sign-On di Azure AD con SAP NetWeaver, è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test di SAP NetWeaver](#creating-sapnetweaver-test-user)**: per avere una controparte di Britta Simon in SAP NetWeaver collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale Azure e viene configurato l'accesso Single Sign-On nell'applicazione SAP NetWeaver.

**Per configurare l'accesso Single Sign-On di Azure AD con SAP NetWeaver, seguire questa procedura:**

1. Aprire una nuova finestra del Web browser e accedere al sito aziendale di SAP NetWeaver come amministratore

2. Assicurarsi che i servizi **http** e **https** siano attivi e che le porte appropriate siano assegnate nel codice di transazione **SMICM**.

3. Accedere al client aziendale del sistema SAP (T01) in cui è richiesto l'accesso SSO e attivare la gestione della sessione di sicurezza HTTP.

    a. Passare al codice di transazione **SICF_SESSIONS**. Questo codice mostra tutti i parametri di profilo rilevanti con i valori correnti. L'aspetto è simile al seguente:
    ```
    login/create_sso2_ticket = 2
    login/accept_sso2_ticket = 1
    login/ticketcache_entries_max = 1000
    login/ticketcache_off = 0  login/ticket_only_by_https = 0 
    icf/set_HTTPonly_flag_on_cookies = 3
    icf/user_recheck = 0  http/security_session_timeout = 1800
    http/security_context_cache_size = 2500
    rdisp/plugin_auto_logout = 1800
    rdisp/autothtime = 60
    ```
    >[!NOTE]
    > Modificare i parametri indicati sopra in base alle esigenze dell'organizzazione. I valori precedenti sono solo a scopo illustrativo.

    b. Se necessario, modificare i parametri nel profilo predefinito/dell'istanza per il sistema SAP e riavviare il sistema SAP.

    c. Fare doppio clic sul client appropriato per abilitare la sessione di sicurezza HTTP.

    ![Collegamento di download del certificato](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_profileparameter.png)

    d. Attivare i servizi SICF seguenti:
    ```
    /sap/public/bc/sec/saml2
    /sap/public/bc/sec/cdc_ext_service
    /sap/bc/webdynpro/sap/saml2
    /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
    ```
4. Passare al codice di transazione **SAML2** nel client aziendale del sistema SAP [T01/122]. Verrà aperta un'interfaccia utente in un browser. In questo esempio si presuppone che il client aziendale SAP sia 122.

    ![Collegamento di download del certificato](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_sapbusinessclient.png)

5. Fornire il nome utente e la password da immettere nell'interfaccia utente e fare clic su **Edit** (Modifica).

    ![Collegamento di download del certificato](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_userpwd.png)

6. Modificare **Provider Name** (Nome provider) da T01122 a **http://T01122** e fare clic su **Save** (Salva).

    > [!NOTE]
    > Per impostazione predefinita il nome del provider è nel formato <sid><client>, mentre in Azure AD è richiesto il formato <protocol>://<name>, quindi è consigliabile mantenere il nome del provider come https://<sid><client> per consentire la configurazione di più motori ABAP SAP NetWeaver in Azure AD.

    ![Collegamento di download del certificato](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_providername.png)

7. **Generazione di metadati del provider di servizi**: una volta completata la configurazione delle impostazioni di **Local Provider** (Provider locale) e **Trusted Providers** (Provider attendibili) nell'interfaccia utente di SAML 2.0, il passaggio successivo consiste nel generare il file di metadati del provider di servizi (che contiene tutte le impostazioni, i contesti di autenticazione e altre configurazioni di SAP). Una volta generato il file, è necessario caricarlo in Azure AD.

    ![Collegamento di download del certificato](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_generatesp.png)

    a. Passare alla scheda **Local Provider** (Provider locale).

    b. Fare clic su **Metadata** (Metadati).

    c. Salvare il **file XML di metadati** generato nel computer e caricarlo nella sezione **Configurazione SAML di base** per popolare automaticamente i valori di **Identificatore** e **URL di risposta** nel portale di Azure.

8. Nella pagina di integrazione dell'applicazione **SAP NetWeaver** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

9. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** fare clic su **Seleziona** per la modalità **SAML** per abilitare l'accesso Single Sign-On.

    ![Configure Single Sign-On](common/tutorial_general_301.png)

10. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Configure Single Sign-On](common/editconfigure.png)

11. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    a. Fare clic su **Carica file di metadati** per caricare il **file di metadati del provider di servizi** ottenuto in precedenza.

    ![Caricare file di metadati](common/editmetadataupload.png)

    b. Fare clic su **logo cartella** per selezionare il file di metadati e fare quindi clic su **Upload**.

    ![Caricare file di metadati](common/uploadmetadata.png)

    c. Dopo che è stato caricato il file di metadati, i valori **Identificatore** e **URL di risposta** vengono popolati automaticamente nella casella di testo della sezione **Configurazione SAML di base**, come illustrato di seguito:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di SAP NetWeaver](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_url.png)

    d. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<your company instance of SAP NetWeaver>`

12. L'applicazione SAP NetWeaver richiede che le asserzioni SAML abbiano un formato specifico. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente** nella pagina di integrazione dell'applicazione. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Attributi utente**.

    ![Sezione relativa all'attributo](./media/sapnetweaver-tutorial/edit_attribute.png)

13. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** configurare l'attributo del token SAML come mostrato nell'immagine precedente e seguire questa procedura:

    a. Fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Gestisci attestazioni utente**.
    
    ![Sezione relativa all'attributo](./media/sapnetweaver-tutorial/nameidattribute.png)

    b. Nella scheda **Gestisci attestazioni utente** seguire questa procedura:

    ![Sezione relativa all'attributo](./media/sapnetweaver-tutorial/nameidattribute1.png)

    * Selezionare **Trasformazione**.
  
    * Nell'elenco **Trasformazione** selezionare `ExtractMailPrefix()`.
  
    * Nell'elenco **Parametro 1** selezionare `user.userprincipalname`.

    * Fare clic su **Save**.

14. Nella pagina **Certificato di firma SAML**, nella sezione **Certificato di firma SAML** fare clic su **Scarica** per scaricare **Metadati federativi XML** e salvare il file di metadati nel computer in uso.

    ![Collegamento di download del certificato](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_certificate.png)

15. Nella sezione **Configura SAP NetWeaver** copiare l'URL appropriato in base alle proprie esigenze.

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

    ![Configurazione di SAP NetWeaver](common/configuresection.png)

16. Accedere al sistema SAP e passare al codice di transazione SAML2. Verrà visualizzata una nuova finestra del browser con la schermata di configurazione SAML.

17. Per la configurazione degli endpoint per i provider di identità attendibili (Azure AD), passare alla scheda **Trusted Providers** (Provider attendibili).

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_samlconfig.png)

18. Premere **Add** (Aggiungi) e scegliere **Upload Metadata File** (Carica file di metadati) dal menu di scelta rapida.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_uploadmetadata.png)

19. Caricare il file di metadati scaricato dal portale di Azure.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_metadatafile.png)

20. Nella schermata successiva digitare il nome dell'alias. Digitare ad esempio aadsts e premere **Next** (Avanti) per continuare.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_aliasname.png)

21. Assicurarsi che il valore di **Digest Algorithm** (Algoritmo digest) sia **SHA-256** e che non siano necessarie modifiche e premere **Next** (Avanti).

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_identityprovider.png)

22. In **Single Sign-On Endpoints** (Endpoint Single Sign-On) selezionare **HTTP POST** e fare clic su **Next** (Avanti) per continuare.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect.png)

23. In **Single Logout Endpoints** (Endpoint punto di disconnessione singolo) selezionare **HTTPRedirect** e fare clic su **Next** (Avanti) per continuare.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect1.png)

24. In **Artifact Endpoints** (Endpoint artefatto) premere **Next** (Avanti) per continuare.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_artifactendpoint.png)

25. In **Authentication Requirements** (Requisiti di autenticazione) fare clic su **Finish** (Fine).

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_authentication.png)

26. Passare alla scheda **Trusted Providers** > **Identity Federation** (Provider attendibili > Federazione identità), nella parte inferiore dello schermo. Fare clic su **Modifica**.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_trustedprovider.png)

27. Fare clic su **Add** (Aggiungi) sotto **Identity Federation** (Federazione identità), nella finestra inferiore.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_addidentityprovider.png)

28. Nella finestra popup selezionare **Unspecified** (Non specificato) in **Supported NameID formats** (Formati NameID supportati) e fare clic su OK.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameid.png)

29. Si noti che i valori di **user ID Source** (Origine ID utente) e **user id mapping mode** (Modalità mapping ID utente) determinano il collegamento tra l'utente SAP e l'attestazione di Azure AD.  

    ####<a name="scenario-sap-user-to-azure-ad-user-mapping"></a>Scenario: Mapping tra utente SAP e utente di Azure AD.

    a. Screenshot dei dettagli di NameID in SAP.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/nameiddetails.png)

    b. Screenshot con l'indicazione delle attestazioni richieste da Azure AD.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/claimsaad1.png)

    ####<a name="scenario-select-sap-user-id-based-on-configured-email-address-in-su01-in-this-case-email-id-should-be-configured-in-su01-for-each-user-who-requires-sso"></a>Scenario: Selezionare l'ID utente SAP in base all'indirizzo di posta elettronica configurato in SU01. In questo caso, l'ID di posta elettronica deve essere configurato in su01 per ogni utente che richiede l'accesso Single Sign-On.

    a.  Screenshot dei dettagli di NameID in SAP.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameiddetails1.png)

    b. Screenshot con l'indicazione delle attestazioni richieste da Azure AD.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/claimsaad2.png)

30. Fare clic su **Save** (Salva) e quindi su **Enable** (Abilita) per abilitare il provider di identità.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/configuration1.png)

31. Quando richiesto, fare clic su **OK**.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/configuration2.png)

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

### <a name="creating-sap-netweaver-test-user"></a>Creazione di un utente di test di SAP NetWeaver

In questa sezione viene creato un utente chiamato Britta Simon in SAP NetWeaver. Collaborare con il team di esperti SAP interno o con il partner SAP dell'organizzazione per aggiungere gli utenti nella piattaforma SAP NetWeaver.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a SAP NetWeaver.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Assegna utente][201]

2. Nell'elenco delle applicazioni selezionare **SAP NetWeaver**.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Nella finestra di dialogo **Aggiungi assegnazione** selezionare il pulsante **Assegna**.

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

1. Una volta attivato il provider di identità Azure AD, provare ad accedere all'URL seguente per controllare l'accesso Single Sign-On (non verranno richiesti nome utente e password)

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    (oppure) usare l'URL seguente

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    > [!NOTE]
    > Sostituire sapurl con il nome host SAP effettivo.

2. L'URL precedente dovrebbe consentire di visualizzare la schermata illustrata sotto. Se viene visualizzata la pagina seguente, la configurazione dell'accesso Single Sign-On di Azure AD è stata eseguita correttamente.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/testingsso.png)

3. Se vengono richiesti nome utente e password, diagnosticare il problema abilitando la traccia tramite l'URL seguente

    `https://<sapurl>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#`

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
