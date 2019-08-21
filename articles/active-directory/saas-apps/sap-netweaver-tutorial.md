---
title: "Esercitazione: Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con SAP NetWeaver | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SAP NetWeaver.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 1b9e59e3-e7ae-4e74-b16c-8c1a7ccfdef3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b002a9d5385d6cee3f22da7a1ddcf1f0864311ec
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989031"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-netweaver"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con SAP NetWeaver

Questa esercitazione descrive come integrare SAP NetWeaver con Azure Active Directory (Azure AD). Integrando SAP NetWeaver con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a SAP NetWeaver.
* Abilitare gli utenti per l'accesso automatico a SAP NetWeaver con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di SAP NetWeaver abilitata per l'accesso Single Sign-On (SSO).
* SAP NetWeaver V7.20 minimo

## <a name="scenario-description"></a>Descrizione dello scenario

SAP NetWeaver supporta sia **SAML** (**accesso SSO avviato da SP**) che **OAuth**. In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. 

> [!NOTE]
> Configurare l'applicazione in SAML o in OAuth in base ai requisiti dell'organizzazione. 

## <a name="adding-sap-netweaver-from-the-gallery"></a>Aggiunta di SAP NetWeaver dalla raccolta

Per configurare l'integrazione di SAP NetWeaver in Azure AD, è necessario aggiungere SAP NetWeaver dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **SAP NetWeaver** nella casella di ricerca.
1. Selezionare **SAP NetWeaver** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-netweaver"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per SAP NetWeaver

Configurare e testare l'accesso SSO di Azure AD con SAP NetWeaver usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SAP NetWeaver.

Per configurare e testare l'accesso SSO di Azure AD con SAP NetWeaver, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** per consentire a B.Simon di usare l'accesso Single Sign-On di Azure AD.
1. **[Configurare SAP NetWeaver con SAML](#configure-sap-netweaver-using-saml)** : per configurare le impostazioni dell'accesso Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di SAP NetWeaver ](#create-sap-netweaver-test-user)** : per avere una controparte di B.Simon in SAP NetWeaver collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** per verificare se la configurazione funziona.
1. **[Configurare SAP NetWeaver per OAuth](#configure-sap-netweaver-for-oauth)** : per configurare le impostazioni di OAuth sul lato applicazione.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con SAP NetWeaver, seguire questa procedura:

1. Aprire una nuova finestra del Web browser e accedere al sito aziendale di SAP NetWeaver come amministratore.

1. Assicurarsi che i servizi **http** e **https** siano attivi e che le porte appropriate siano assegnate nel codice di transazione **SMICM**.

1. Accedere al client aziendale del sistema SAP (T01) in cui è richiesto l'accesso SSO e attivare la gestione della sessione di sicurezza HTTP.

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
1. Passare al codice di transazione **SAML2** nel client aziendale del sistema SAP [T01/122]. Verrà aperta un'interfaccia utente in un browser. In questo esempio si presuppone che il client aziendale SAP sia 122.

    ![Collegamento di download del certificato](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_sapbusinessclient.png)

1. Fornire il nome utente e la password da immettere nell'interfaccia utente e fare clic su **Edit** (Modifica).

    ![Collegamento di download del certificato](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_userpwd.png)

1. Sostituire **Provider Name** (Nome provider) T01122 con `http://T01122` e fare clic su **Save** (Salva).

    > [!NOTE]
    > Per impostazione predefinita il nome del provider è nel formato `<sid><client>`, mentre in Azure AD è richiesto il formato `<protocol>://<name>`. È quindi consigliabile mantenere il nome del provider come `https://<sid><client>` per consentire la configurazione di più motori ABAP SAP NetWeaver in Azure AD.

    ![Collegamento di download del certificato](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_providername.png)

1. **Generazione di metadati del provider di servizi**: una volta completata la configurazione delle impostazioni di **Local Provider** (Provider locale) e **Trusted Providers** (Provider attendibili) nell'interfaccia utente di SAML 2.0, il passaggio successivo consiste nel generare il file di metadati del provider di servizi (che contiene tutte le impostazioni, i contesti di autenticazione e altre configurazioni di SAP). Una volta generato il file, è necessario caricarlo in Azure AD.

    ![Collegamento di download del certificato](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_generatesp.png)

    a. Passare alla scheda **Local Provider** (Provider locale).

    b. Fare clic su **Metadata** (Metadati).

    c. Salvare il **file XML dei metadati** nel computer e caricarlo nella sezione **Configurazione SAML di base** per inserire automaticamente i valori di **Identificatore** e **URL di risposta** nel portale di Azure.

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **SAP NetWeaver** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Fare clic su **Carica file di metadati** per caricare il **file di metadati del provider di servizi** ottenuto in precedenza.

    b. Fare clic su **logo cartella** per selezionare il file di metadati e fare quindi clic su **Upload**.

    c. Al termine del caricamento del file di metadati, i valori di **Identificatore** e **URL di risposta** vengono inseriti automaticamente nella casella di testo della sezione **Configurazione SAML di base**, come illustrato di seguito:

    d. Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<your company instance of SAP NetWeaver>`

    > [!NOTE]
    > Alcuni clienti hanno segnalato un errore di configurazione non valida dell'URL di risposta per la propria istanza. Se viene visualizzato un tale errore, è possibile usare lo script di PowerShell seguente come metodo alternativo per impostare l'URL di risposta corretto per l'istanza:
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > È prima necessario impostare manualmente l'ID oggetto entità servizio; in alternativa è possibile passarlo anche qui.

1. L'applicazione SAP NetWeaver prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti. Fare clic su  **Modifica** per aprire la finestra di dialogo Attributi utente.

    ![image](common/edit-attribute.png)

1. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** configurare l'attributo del token SAML come mostrato nell'immagine precedente e seguire questa procedura:

    a. Fare clic sull'**icona Modifica** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![image](./media/sapnetweaver-tutorial/nameidattribute.png)

    ![image](./media/sapnetweaver-tutorial/nameidattribute1.png)

    b. Nell'elenco **Trasformazione** selezionare **ExtractMailPrefix()** .

    c. Nell'elenco **Parametro 1** selezionare **user.userprinicipalname**.

    d. Fare clic su **Save**.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **XML metadati federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer in uso.

   ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura SAP NetWeaver** copiare gli URL appropriati in base alle esigenze.

   ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
    1. Nel campo **Nome** immettere `B.Simon`.  
    1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
    1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
    1. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a SAP NetWeaver.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **SAP NetWeaver**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-sap-netweaver-using-saml"></a>Configurare SAP NetWeaver con SAML

1. Accedere al sistema SAP e passare al codice transazione SAML2. Verrà visualizzata una nuova finestra del browser con la schermata di configurazione SAML.

2. Per la configurazione degli endpoint per i provider di identità attendibili (Azure AD), passare alla scheda **Trusted Providers** (Provider attendibili).

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_samlconfig.png)

3. Premere **Add** (Aggiungi) e scegliere **Upload Metadata File** (Carica file di metadati) dal menu di scelta rapida.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_uploadmetadata.png)

4. Caricare il file di metadati scaricato dal portale di Azure.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_metadatafile.png)

5. Nella schermata successiva digitare il nome dell'alias. Digitare ad esempio aadsts e premere **Next** (Avanti) per continuare.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_aliasname.png)

6. Assicurarsi che il valore di **Digest Algorithm** (Algoritmo digest) sia **SHA-256** e che non siano necessarie modifiche e premere **Next** (Avanti).

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_identityprovider.png)

7. In **Single Sign-On Endpoints** (Endpoint Single Sign-On) selezionare **HTTP POST** e fare clic su **Next** (Avanti) per continuare.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect.png)

8. In **Single Logout Endpoints** (Endpoint punto di disconnessione singolo) selezionare **HTTPRedirect** e fare clic su **Next** (Avanti) per continuare.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect1.png)

9. In **Artifact Endpoints** (Endpoint artefatto) premere **Next** (Avanti) per continuare.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_artifactendpoint.png)

10. In **Authentication Requirements** (Requisiti di autenticazione) fare clic su **Finish** (Fine).

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_authentication.png)

11. Passare alla scheda **Trusted Providers** > **Identity Federation** (Provider attendibili > Federazione identità), nella parte inferiore dello schermo. Fare clic su **Modifica**.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_trustedprovider.png)

12. Fare clic su **Add** (Aggiungi) sotto **Identity Federation** (Federazione identità), nella finestra inferiore.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_addidentityprovider.png)

13. Nella finestra popup selezionare **Unspecified** (Non specificato) in **Supported NameID formats** (Formati NameID supportati) e fare clic su OK.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameid.png)

14. Si noti che i valori **user ID Source** (Origine ID utente) e **user ID mapping mode** (Modalità mapping ID utente) determinano il collegamento tra l'utente SAP e l'attestazione di Azure AD.  

    #### <a name="scenario-sap-user-to-azure-ad-user-mapping"></a>Scenario: Mapping tra utente SAP e utente di Azure AD.

    a. Screenshot dei dettagli di NameID in SAP.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/nameiddetails.png)

    b. Screenshot con l'indicazione delle attestazioni necessarie in Azure AD.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/claimsaad1.png)

    #### <a name="scenario-select-sap-user-id-based-on-configured-email-address-in-su01-in-this-case-email-id-should-be-configured-in-su01-for-each-user-who-requires-sso"></a>Scenario: Selezionare l'ID utente SAP usando l'indirizzo di posta elettronica configurato in SU01. In questo caso l'ID posta elettronica deve essere configurato in SU01 per ogni utente che richiede l'accesso SSO.

    a.  Screenshot dei dettagli di NameID in SAP.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameiddetails1.png)

    b. Screenshot con l'indicazione delle attestazioni necessarie in Azure AD.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/claimsaad2.png)

15. Fare clic su **Save** (Salva) e quindi su **Enable** (Abilita) per abilitare il provider di identità.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/configuration1.png)

16. Quando richiesto, fare clic su **OK**.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/configuration2.png)

    ### <a name="create-sap-netweaver-test-user"></a>Creare un utente di test di SAP NetWeaver

    In questa sezione viene creato un utente di nome B.Simon in SAP NetWeaver. Collaborare con il team di esperti SAP interno o con il partner SAP dell'organizzazione per aggiungere gli utenti nella piattaforma SAP NetWeaver.

## <a name="test-sso"></a>Testare l'accesso SSO

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

## <a name="configure-sap-netweaver-for-oauth"></a>Configurare SAP NetWeaver per OAuth

1. La documentazione del processo SAP è disponibile qui: [NetWeaver Gateway Service Enabling and OAuth 2.0 Scope Creation](https://wiki.scn.sap.com/wiki/display/Security/NetWeaver+Gateway+Service+Enabling+and+OAuth+2.0+Scope+Creation) (Abilitazione del servizio gateway di NetWeaver e creazione dell'ambito di OAuth 2.0)

2. Passare a SPRO e individuare la voce **Activate and Maintain services** (Attiva e gestisci i servizi).

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/oauth01.png)

3. In questo esempio si vuole connettere il servizio OData `DAAG_MNGGRP` con OAuth all'accesso SSO di Azure AD. Usare la ricerca del nome del servizio tecnico per il servizio `DAAG_MNGGRP` e attivarlo se non è ancora attivo (cercare lo stato `green` nella scheda dei nodi ICF). Assicurarsi che l'alias di sistema (ovvero il sistema back-end connesso, in cui è in esecuzione il servizio) sia corretto.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/oauth02.png)

    * Fare quindi clic sul pulsante **OAuth** sulla barra dei pulsanti in alto e assegnare `scope` (non modificare il nome predefinito visualizzato).

4. Per questo esempio l'ambito è `DAAG_MNGGRP_001` e viene generato dal nome del servizio aggiungendo automaticamente un numero. È possibile usare il report `/IWFND/R_OAUTH_SCOPES` per modificare il nome dell'ambito o per crearlo manualmente.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/oauth03.png)

    > [!NOTE]
    > Il messaggio `soft state status is not supported` può essere ignorato perché non costituisce un problema. Per altri dettagli, vedere [qui](https://help.sap.com/doc/saphelp_nw74/7.4.16/1e/c60c33be784846aad62716b4a1df39/content.htm?no_cache=true)

### <a name="create-a-service-user-for-the-oauth-20-client"></a>Creare un utente del servizio per il client OAuth 2.0

1. OAuth2 usa un `service ID` per ottenere il token di accesso per l'utente finale per suo conto. La progettazione di OAuth prevede un'importante restrizione: `OAuth 2.0 Client ID` deve essere identico al valore di `username` che il client OAuth 2.0 usa per l'accesso quando viene richiesto un token di accesso. Di conseguenza, per questo esempio, si registrerà il client OAuth 2.0 con il nome CLIENT1. Come prerequisito nel sistema SAP deve esistere un utente con lo stesso nome (CLIENT1) che verrà configurato per poterlo usare con l'applicazione cui viene fatto riferimento. 

2. Durante la registrazione di un client OAuth si usato il `SAML Bearer Grant type`.

    >[!NOTE]
    >Per altri dettagli, vedere la procedura di registrazione del client di OAuth 2.0 per il tipo di concessione bearer SAML disponibile [qui](https://wiki.scn.sap.com/wiki/display/Security/OAuth+2.0+Client+Registration+for+the+SAML+Bearer+Grant+Type).

3. tcod: SU01 / creare l'utente CLIENT1 come `System type` e assegnare una password, salvarlo come necessario per fornire le credenziali al programmatore API, che deve inserirlo con il nome utente nel codice chiamante. Non devono essere assegnati alcun profilo o ruolo.

### <a name="register-the-new-oauth-20-client-id-with-the-creation-wizard"></a>Registrare il nuovo ID client OAuth 2.0 con la creazione guidata

1. Per registrare un nuovo **client OAuth 2.0**, avviare la transazione **SOAUTH2**. La transazione visualizzerà una panoramica sui client OAuth 2.0 già registrati. Scegliere **Create** (Crea) per avviare la procedura guidata per il nuovo client OAuth denominato CLIENT1 in questo esempio.

2. Passare a T-Code: **SOAUTH2** e fornire la descrizione, quindi fare clic su **next** (avanti).

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/oauth04.png)

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/oauth05.png)

3. Selezionare la voce **SAML2 IdP - Azure AD** dall'elenco a discesa e salvare.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/oauth06.png)

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/oauth07.png)

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/oauth08.png)

4. Fare clic su **Add** (Aggiungi) sotto l'assegnazione dell'ambito per aggiungere l'ambito creato in precedenza: `DAAG_MNGGRP_001`

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/oauth09.png)

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/oauth10.png)

5. Fare clic su **finish** (fine).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare SAP NetWeaver con Azure AD](https://aad.portal.azure.com/)