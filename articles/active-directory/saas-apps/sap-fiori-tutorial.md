---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con SAP Fiori | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SAP Fiori.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 77ad13bf-e56b-4063-97d0-c82a19da9d56
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/05/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50d1875ce2529222e8ff7472c48bf6d4dd878667
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772863"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-fiori"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con SAP Fiori

Questa esercitazione descrive come integrare SAP Fiori con Azure Active Directory (Azure AD). Integrando SAP Fiori con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a SAP Fiori.
* Abilitare gli utenti per l'accesso automatico a SAP Fiori con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di SAP Fiori abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* SAP Fiori supporta l'accesso SSO avviato da **SP**

> [!NOTE]
> Per l'autenticazione iFrame avviata da SAP Fiori, è consigliabile usare il parametro **IsPassive** in SAML AuthnRequest per l'autenticazione invisibile all'utente. Per altri dettagli sul parametro **IsPassive**, vedere le informazioni relative all'[accesso Single Sign-On SAML di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol).

## <a name="adding-sap-fiori-from-the-gallery"></a>Aggiunta di SAP Fiori dalla raccolta

Per configurare l'integrazione di SAP Fiori in Azure AD, è necessario aggiungere SAP Fiori dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **SAP Fiori** nella casella di ricerca.
1. Selezionare **SAP Fiori** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-fiori"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per SAP Fiori

Configurare e testare l'accesso SSO di Azure AD con SAP Fiori usando un utente di test di nome **B.Simon**. Per il corretto funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SAP Fiori.

Per configurare e testare l'accesso SSO di Azure AD con SAP Fiori, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso SSO di SAP Fiori](#configure-sap-fiori-sso)** : per configurare le impostazioni dell'accesso Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di SAP Fiori](#create-sap-fiori-test-user)** : per avere una controparte di B.Simon in SAP Fiori collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Aprire una nuova finestra del Web browser e accedere al sito SAP Fiori aziendale come amministratore.

1. Assicurarsi che i servizi **http** e **https** siano attivi e che le relative porte siano assegnate al codice di transazione **SMICM**.

1. Accedere al client SAP aziendale per il sistema SAP **T01**, in cui è obbligatorio l’accesso Single Sign-On. Attivare quindi la gestione della sessione di sicurezza HTTP.

    1. Selezionare il codice di transazione **SICF_SESSIONS**. Verranno visualizzati tutti i parametri di profilo rilevanti con i valori correnti. Il risultato dovrebbe essere simile al seguente:

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
        > Configurare i parametri in base alle esigenze dell'organizzazione. I parametri precedenti sono forniti solo come esempio.

    1. Se necessario, modificare i parametri del profilo predefinito e riavviare il sistema SAP.

    1. Fare doppio clic sul client appropriato per abilitare la sessione di sicurezza HTTP.

        ![Valori correnti della pagina dei parametri del profilo pertinenti in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-profileparameter.png)

    1. Attivare i servizi SICF seguenti:

        ```
        /sap/public/bc/sec/saml2
        /sap/public/bc/sec/cdc_ext_service
        /sap/bc/webdynpro/sap/saml2
        /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
        ```

1. Selezionare il codice di transazione **SAML2** nel client aziendale del sistema SAP [**T01/122**]. L’interfaccia utente di configurazione viene aperta in una nuova finestra del browser. In questo esempio, utilizziamo il client aziendale per il sistema SAP 122.

    ![Pagina di accesso del client SAP Fiori aziendale](./media/sapfiori-tutorial/tutorial-sapnetweaver-sapbusinessclient.png)

1. Immettere nome utente e password, quindi selezionare **Accedi**.

    ![Pagina di configurazione del sistema T01/122 ABAP di SAML 2.0 in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-userpwd.png)

1. Nella casella **Nome del provider**, sostituire **T01122** con **http:\//T01122**, quindi selezionare **Salva**.

    > [!NOTE]
    > Per impostazione predefinita, il nome del provider è nel formato \<sid>\<client>. Azure AD prevede che il nome sia nel formato \<protocollo>://\<nome>. È consigliabile mantenere il nome del provider come https\://\<sid>\<client> in modo da poter configurare più motori ABAP SAP Fioriin Azure AD.

    ![Nome del provider aggiornato nella pagina di configurazione del sistema T01/122 ABAP di SAML 2.0 in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-providername.png)

1. Selezionare la secheda **Provider locale** > **Metadati**.

1. Nella finestra di dialogo **Metadati SAML 2.0**, scaricare il file XML con i metadati generati e salvarlo nel computer.

    ![Collegamento Scarica metadati nella finestra di dialogo Metadati SAML 2.0 di SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-generatesp.png)

1. Nella pagina di integrazione dell'applicazione **SAP Fiori** del [portale di Azure](https://portal.azure.com/) trovare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base**, se si dispone di un **file di metadati di un provider di servizi**, seguire questa procedura:

    a. Fare clic su **Carica il file di metadati**.

    ![Caricare file di metadati](common/upload-metadata.png)

    b. Fare clic su **logo cartella** per selezionare il file di metadati e fare quindi clic su **Upload**.

    ![Scegliere file di metadati](common/browse-upload-metadata.png)

    c. Dopo il caricamento del file di metadati, i valori di **Identificatore** e **URL di risposta** vengono inseriti automaticamente nel riquadro **Configurazione SAML di base**. Nella casella **URL di accesso** immettere un URL con il formato seguente: `https:\//\<your company instance of SAP Fiori\>`.

    > [!NOTE]
    > Alcuni clienti segnalano errori di configurazione dei valori in **URL di risposta**. In tal caso, è possibile usare il seguente script di PowerShell per impostare l'URL di risposta corretto per l'istanza:
    >
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > 
    > È possibile impostare l’ID oggetto `ServicePrincipal` prima di eseguire lo script oppure in questo momento.

1. L'applicazione SAP Fiori prevede un formato specifico per le asserzioni SAML. Configurare le attestazioni seguenti per questa applicazione. Per gestire questi valori di attributo, nel riquadro **Configura l'accesso Single Sign-On con SAML**, selezionare **Modifica**.

    ![Riquadro Attributi utente](common/edit-attribute.png)

1. Nel riquadro **Attributi utente e attestazioni**, configurare gli attributi del token SAML come illustrato nell'immagine precedente. Completare quindi i passaggi seguenti:

    1. Selezionare **Modifica** per aprire il riquadro **Gestisci attestazioni utente**.

    1. Nell'elenco **Trasformazione**, selezionare **ExtractMailPrefix()** .

    1. Nell’elenco **Parametro 1**, selezionare **utente.nomeprincipaleutente**.

    1. Selezionare **Salva**.

       ![Riquadro Gestisci attestazioni utente](./media/sapfiori-tutorial/nameidattribute.png)

       ![Sezione Trasformazione nel riquadro Gestisci attestazioni utente](./media/sapfiori-tutorial/nameidattribute1.png)
    
1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il file **XML dei metadati della federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura SAP Fiori** copiare gli URL appropriati in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a SAP Fiori.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **SAP Fiori**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-sap-fiori-sso"></a>Configurare l'accesso SSO di SAP Fiori

1. Accedere al sistema SAP e selezionare il codice di transazione **SAML2**. Verrà aperta una nuova finestra del browser con la pagina di configurazione SAML.

1. Per configurare i provider di identità attendibili (Azure AD) negli endpoint, selezionare la scheda **Provider attendibili**.

    ![Scheda Provider attendibili di SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-samlconfig.png)

1. Selezionare **Aggiungi** e scegliere **Carica file di metadati** dal menu di scelta rapida.

    ![Opzioni Aggiungi e Carica file di metadati in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-uploadmetadata.png)

1. Caricare il file di metadati scaricato dal portale di Azure. Selezionare **Avanti**.

    ![Selezionare il file di metadati da caricare in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-metadatafile.png)

1. Nella pagina successiva, nella casella **Alias**, immettere il nome di alias. Ad esempio, **aadsts**. Selezionare **Avanti**.

    ![Casella Alias in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-aliasname.png)

1. Assicurarsi che il valore della casella **Algoritmo con classificazione** sia **SHA-256**. Selezionare **Avanti**.

    ![Verificare il valore di Algoritmo con classificazione in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-identityprovider.png)

1. In **Endpoint Single Sign-On**, selezionare **HTTP POST**, quindi **Avanti**.

    ![Opzioni Endpoint Single Sign-On in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect.png)

1. In **Single Logout Endpoints** (Endpoint di disconnessione singolo), selezionare **Reindirizzamento HTTP**, quindi **Avanti**.

    ![Opzioni Endpoint di disconnessione singolo in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect1.png)

1. In **Artifact Endpoints** (Endpoint artefatto), selezionare **Avanti** per continuare.

    ![Opzioni Artifact Endpoints (Endpoint artefatto) in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-artifactendpoint.png)

1. In **Requisiti per l’autenticazione**, selezionare **Fine**.

    ![Opzioni Requisiti per l’autenticazione e Fine in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-authentication.png)

1. Selezionare **Provider attendibile** > **Federazione delle identità** nella parte inferiore della pagina. Selezionare **Modifica**.

    ![Schede Provider attendibile e Federazione delle identità in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-trustedprovider.png)

1. Selezionare **Aggiungi**.

    ![Opzione Aggiungi nella scheda Federazione delle identità](./media/sapfiori-tutorial/tutorial-sapnetweaver-addidentityprovider.png)

1. Nella finestra di dialogo **Supported NameID Formats** (Formati NameID supportati), selezionare **Non specificato**. Selezionare **OK**.

    ![Finestra di dialogo e opzioni Supported NameID Formats (Formati NameID supportati) in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameid.png)

    I valori **user ID Source** (Origine ID utente) e **User ID Mapping Mode** (Modalità di mapping ID utente) determinano il collegamento tra l'utente SAP e l'attestazione di Azure AD.  

    **Scenario 1:** Mapping tra utente SAP e utente di Azure AD

    1. In SAP, sotto **Details of NameID Format "Unspecified"** (Dettagli formato NameID “Non specificato”), annotare i dettagli:

        ![Finestra di dialogo Details of NameID Format "Unspecified" (Dettagli formato NameID "Non specificato") in SAP](./media/sapfiori-tutorial/nameiddetails.png)

    1. Nel portale di Azure, sotto **Attributi utente e attestazioni**, annotare le attestazioni necessarie da Azure AD.

        ![Finestra di dialogo Attributi utente e attestazioni nel portale di Azure](./media/sapfiori-tutorial/claimsaad1.png)

    **Scenario 2**: Selezionare l'ID utente SAP usando l'indirizzo di posta elettronica configurato in SU01. In questo caso l'ID posta elettronica deve essere configurato in SU01 per ogni utente che richiede l'accesso SSO.

    1.  In SAP, sotto **Details of NameID Format "Unspecified"** (Dettagli formato NameID "Non specificato"), annotare i dettagli:

        ![Finestra di dialogo Details of NameID Format "Unspecified" (Dettagli formato NameID "Non specificato") in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameiddetails1.png)

    1. Nel portale di Azure, sotto **Attributi utente e attestazioni**, annotare le attestazioni necessarie da Azure AD.

       ![Finestra di dialogo Attributi utente e attestazioni nel portale di Azure](./media/sapfiori-tutorial/claimsaad2.png)

1. Fare clic su **Salva**, quindi su **Abilita** per abilitare il provider di identità.

    ![Opzioni Salva e Abilita in SAP](./media/sapfiori-tutorial/configuration1.png)

1. Quando richiesto, fare clic su **OK**.

    ![Opzione OK nella finestra di dialogo SAML 2.0 Configuration (Configurazione di SAML 2.0) in SAP](./media/sapfiori-tutorial/configuration2.png)

### <a name="create-sap-fiori-test-user"></a>Creare l'utente di test di SAP Fiori

In questa sezione viene creato un utente di nome Britta Simon in SAP Fiori. Collaborare con il team di esperti SAP interno o con il partner SAP dell'organizzazione per aggiungere l’utente alla piattaforma SAP Fiori.

## <a name="test-sso"></a>Testare l'accesso SSO

1. Dopo aver attivato l’istanza Azure AD del provider di identità in SAP Fiori, accedere a uno degli URL seguenti per testare l'accesso Single Sign-On (non dovrebbero essere richiesti nome utente e password):

    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm
    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm

    > [!NOTE]
    > Sostituire *sapurl* con il nome host SAP effettivo.

1. L'URL di test consente di accedere alla pagina di test successiva in SAP. Se la pagina si apre, l’accesso Single Sign-On di Azure AD è configurato correttamente.

    ![Pagina di test standard in SAP](./media/sapfiori-tutorial/testingsso.png)

1. Se viene chiesto di immettere nome utente e password, abilitare l’analisi per agevolare la diagnosi del problema. Per l’analisi, usare il seguente URL: https:\//\<sapurl\>/sap/bc/webdynpro/sap/sec_diag_tool?client-sap=122&lingua-sap=EN#.

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare SAP Fiori con Azure AD](https://aad.portal.azure.com/)
