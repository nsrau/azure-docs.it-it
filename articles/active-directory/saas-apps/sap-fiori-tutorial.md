---
title: 'Esercitazione: Integrazione di Azure Active Directory con SAP Fiori | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SAP Fiori.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 77ad13bf-e56b-4063-97d0-c82a19da9d56
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: jeedes
ms.openlocfilehash: 9e7993ee1cb439ebeaa9f64bee55429aa54f9cee
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65903954"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-fiori"></a>Esercitazione: Integrazione di Azure Active Directory con SAP Fiori

Questa esercitazione descrive come integrare SAP Fiori con Azure Active Directory (Azure AD).

L’integrazione di SAP Fiori con Azure AD offre i vantaggi seguenti:

* È possibile usare Azure AD per controllare chi può accedere a SAP Fiori.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a SAP Fiori con gli account Azure AD personali.
* È possibile gestire gli account da una posizione centrale, il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS (Software as a Service) con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con SAP Fiori, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di una sottoscrizione di Azure AD, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Una sottoscrizione a SAP Fiori con accesso Single Sign-On abilitato.
* È necessario SAP Fiori 7.20 o versioni successive.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test e viene integrato SAP Fiori con Azure AD.

SAP Fiori supporta le funzionalità seguenti:

* **Accesso Single Sign-On avviato da SP**

## <a name="add-sap-fiori-in-the-azure-portal"></a>Aggiungere SAP Fiori nel portale di Azure

Per integrare SAP Fiori con Azure AD, è necessario aggiungerlo all'elenco delle app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Nel menu a sinistra, selezionare **Azure Active Directory**.

    ![Opzione Azure Active Directory](common/select-azuread.png)

1. Selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.

    ![Riquadro Applicazioni aziendali](common/enterprise-applications.png)

1. Per aggiungere un'applicazione, selezionare **Nuova applicazione**.

    ![Opzione Applicazione nuova](common/add-new-app.png)

1. Nella casella di ricerca, immettere **SAP Fiori**. Nei risultati della ricerca, selezionare **SAP Fiori**, quindi **Aggiungi**.

    ![SAP Fiori nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con SAP Fiori usando un utente di test di nome **Britta Simon**. Per il corretto funzionamento dell’accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l’utente correlato in SAP Fiori.

Per configurare e testare l'accesso Single Sign-On di Azure AD con SAP Fiori, è necessario completare le procedure di base seguenti:

| Attività | DESCRIZIONE |
| --- | --- |
| **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** | Consente agli utenti di usare questa funzionalità. |
| **[Configurare l'accesso Single Sign-On a SAP Fiori](#configure-sap-fiori-single-sign-on)** | Consente di configurare le impostazioni di accesso Single Sign-On nell'applicazione. |
| **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** | Consente di verificare l’accesso Single Sign-On di Azure AD per un utente chiamato Britta Simon. |
| **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** | Consente a Britta Simon di usare l'accesso Single Sign-On di Azure AD. |
| **[Creare un utente di test di SAP Fiori](#create-an-sap-fiori-test-user)** | Consente di creare una controparte di Britta Simon in SAP Fiori collegata alla rappresentazione dell'utente in Azure AD. |
| **[Testare l'accesso Single Sign-On](#test-single-sign-on)** | Verifica se la configurazione funziona. |

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione si configura l'accesso Single Sign-On di Azure AD con SAP Fiori nel portale di Azure.

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

1. Nel riquadro per l’integrazione delle applicazioni [SAP Fiori](https://portal.azure.com/) del **portale di Azure**, selezionare **Single Sign-On**.

    ![Opzione Single Sign-On](common/select-sso.png)

1. Nel riquadro **Selezionare un metodo di accesso Single Sign-On**, selezionare la modalità **SAML** o **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

1. Nel riquadro **Configura l'accesso Single Sign-On con SAML**, selezionare **Modifica** (l'icona a forma di matita) per aprire il riquadro **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** completare la procedura seguente:

    1. Selezionare **Carica il file di metadati**.

        ![Opzione Carica il file di metadati](common/upload-metadata.png)

   1. Per selezionare il file di metadati, selezionare l'icona della cartella, quindi **Carica**.

       ![Selezionare il file di metadati, quindi il pulsante Carica](common/browse-upload-metadata.png)

1. Dopo il caricamento del file di metadati, i valori di **Identificatore** e **URL di risposta** vengono inseriti automaticamente nel riquadro **Configurazione SAML di base**. Nella casella **URL di accesso**, immettere un URL con il modello seguente: https:\//\<istanza aziendale di SAP Fiori\>.

    ![Informazioni sul dominio SAP Fiori e sull’accesso Single Sign-On con URL](common/sp-identifier-reply.png)

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


1. Nel riquadro **Configura l’accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, selezionare **Scarica** accanto a **XML metadati federazione**. Selezionare un'opzione di download in base alle esigenze. Salvare il certificato nel computer.

    ![Opzione Download certificato](common/metadataxml.png)

1. Nella sezione **Configura SAP Fiori**, copiare gli URL seguenti in base alle esigenze:

    * URL di accesso
    * Identificatore di Azure AD
    * URL di chiusura sessione

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="configure-sap-fiori-single-sign-on"></a>Configurare l'accesso Single Sign-On di SAP Fiori

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

        ![Finestra di dialogo Details of NameID Format "Unspecified" (Dettagli formato NameID “Non specificato”) in SAP](./media/sapfiori-tutorial/nameiddetails.png)

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

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

In questa sezione viene creato un utente di test chiamato Britta Simon nel portale di Azure.

1. Nel portale di Azure selezionare **Azure Active Directory** > **Utenti** > **Tutti gli utenti**.

    ![Opzioni Utenti e Tutti gli utenti](common/users.png)

1. Selezionare **Nuovo utente**.

    ![Opzione Nuovo utente](common/new-user.png)

1. Nel riquadro **Utente** completare la procedura seguente:

    1. Nella casella **Nome** immettere **BrittaSimon**.
  
    1. Nella casella **Nome utente**, immettere **brittasimon\@\<dominio-azienda>.\<estensione>** . Ad esempio, **brittasimon\@contoso.com**.

    1. Selezionare la casella di controllo **Mostra password**. Prendere nota del valore visualizzato nella casella **Password**.

    1. Selezionare **Create**.

    ![Riquadro Utente](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si concede a Britta Simon l'accesso a SAP Fiori in modo che possa usare l'accesso Single Sign-On di Azure.

1. Nel portale di Azure, selezionare **Applicazioni aziendali** > **Tutte le applicazioni** > **SAP Fiori**.

    ![Riquadro Applicazioni aziendali](common/enterprise-applications.png)

1. Nell'elenco delle applicazioni selezionare **SAP Fiori**.

    ![SAP Fiori nell'elenco delle applicazioni](common/all-applications.png)

1. Nel menu selezionare **Utenti e gruppi**.

    ![Opzione Utenti e gruppi](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente**. Nel riquadro **Aggiungi assegnazione** selezionare **Utenti e gruppi**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

1. Nel riquadro **Utenti e gruppi**, selezionare **Britta Simon** nell'elenco degli utenti. Scegliere **Seleziona**.

1. Se si prevede un valore di ruolo nell'asserzione SAML, nel riquadro **Seleziona ruolo**, selezionare il ruolo dell'utente dall'elenco. Scegliere **Seleziona**.

1. Nel riquadro **Aggiungi assegnazione** selezionare **Assegna**.

### <a name="create-an-sap-fiori-test-user"></a>Creare un utente di test di SAP Fiori

In questa sezione viene creato un utente di nome Britta Simon in SAP Fiori. Collaborare con il team di esperti SAP interno o con il partner SAP dell'organizzazione per aggiungere l’utente alla piattaforma SAP Fiori.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

1. Dopo aver attivato l’istanza Azure AD del provider di identità in SAP Fiori, accedere a uno degli URL seguenti per testare l'accesso Single Sign-On (non dovrebbero essere richiesti nome utente e password):

    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm
    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm

    > [!NOTE]
    > Sostituire *sapurl* con il nome host SAP effettivo.

1. L'URL di test consente di accedere alla pagina di test successiva in SAP. Se la pagina si apre, l’accesso Single Sign-On di Azure AD è configurato correttamente.

    ![Pagina di test standard in SAP](./media/sapfiori-tutorial/testingsso.png)

1. Se viene chiesto di immettere nome utente e password, abilitare l’analisi per agevolare la diagnosi del problema. Per l’analisi, usare il seguente URL: https:\//\<sapurl\>/sap/bc/webdynpro/sap/sec_diag_tool?client-sap=122&lingua-sap=EN#.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere questi articoli:

- [Elenco di esercitazioni per l'integrazione di app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
