---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Pulse Secure PCS | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Pulse Secure PCS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/11/2020
ms.author: jeedes
ms.openlocfilehash: c46565f7eaa060a060183cf321bb1df1903b945c
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90992499"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pulse-secure-pcs"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Pulse Secure PCS

Questa esercitazione descrive come integrare Pulse Secure PCS con Azure Active Directory (Azure AD). Integrando Pulse Secure PCS con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Pulse Secure PCS.
* Abilitare gli utenti per l'accesso automatico a Pulse Secure PCS con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Pulse Secure PCS abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Pulse Secure PCS supporta l'accesso SSO avviato da **SP**

## <a name="adding-pulse-secure-pcs-from-the-gallery"></a>Aggiunta di Pulse Secure PCS dalla raccolta

Per configurare l'integrazione di Pulse Secure PCS in Azure AD, è necessario aggiungere Pulse Secure PCS dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Pulse Secure PCS** nella casella di ricerca.
1. Selezionare **Pulse Secure PCS** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-sso-for-pulse-secure-pcs"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Pulse Secure PCS

Configurare e testare l'accesso SSO di Azure AD con Pulse Secure PCS usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Pulse Secure PCS.

Per configurare e testare l'accesso SSO di Azure AD con Pulse Secure PCS, completare i passaggi seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Pulse Secure PCS](#configure-pulse-secure-pcs-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Pulse Secure PCS](#create-pulse-secure-pcs-test-user)** : per avere una controparte di B.Simon in Pulse Secure PCS collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Pulse Secure PCS** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<FQDN of PCS>/dana-na/auth/saml-consumer.cgi`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `https://<FQDN of PCS>/dana-na/auth/saml-endpoint.cgi?p=sp1`

    c. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://[FQDN of PCS]/dana-na/auth/saml-consumer.cgi`


    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con i valori effettivi di URL di accesso, URL di risposta e identificatore. Per ottenere questi valori, contattare il [team di supporto clienti di Pulse Secure PCS](mailto:support@pulsesecure.net). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura Pulse Secure PCS** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Pulse Secure PCS.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Pulse Secure PCS**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-pulse-secure-pcs-sso"></a>Configurare l'accesso Single Sign-On di Pulse Secure PCS

Questa sezione descrive le configurazioni SAML necessarie per configurare PCS come provider di servizi SAML. Le altre configurazioni di base, ad esempio la creazione di ruoli e aree autenticazione, non vengono illustrate.

**Le configurazioni di Pulse Connect Secure includono:**

* Configurazione di Azure AD come provider di metadati SAML
* Configurazione del server di autenticazione SAML
* Assegnazione ai rispettivi ruoli e aree autenticazione

#### <a name="configuring-azure-ad-as-saml-metadata-provider"></a>Configurazione di Azure AD come provider di metadati SAML

Nella pagina successiva seguire questa procedura:

![Configurazione di Pulse Connect Secure](./media/pulse-secure-pcs-tutorial/saml-configuration.png)

1. Accedere alla console di amministrazione di Pulse Connect Secure
1. Passare a **System -> Configuration -> SAML** (Sistema -> Configurazione -> SAML)
1. Fare clic su **New Metadata Provider** (Nuovo provider di metadati)
1. Specificare un nome valido nella casella di testo **Name** (Nome)
1. Caricare il file XML dei metadati scaricato dal portale di Azure in **Azure AD metadata file** (File di metadati di Azure AD).
1. Selezionare **Accept Unsigned Metadata** (Accetta metadati non firmati)
1. Per Roles (Ruoli) selezionare **Identity Provider** (Provider di identità)
1. Fare clic su **Save changes** (Salva modifiche).

#### <a name="steps-to-create-a-saml-auth-server"></a>Procedura per la creazione di un server di autenticazione SAML:

1. Passare a **Authentication -> Auth Servers** (Autenticazione -> Server di autenticazione)
1. Selezionare **New: SAML Server** (Nuovo: Server SAML) e fare clic su **New Server** (Nuovo server)

    ![Server di autenticazione di Pulse Connect Secure](./media/pulse-secure-pcs-tutorial/new-saml-server.png)

1. Nella pagina delle impostazioni seguire questa procedura:

    ![Impostazioni del server di autenticazione di Pulse Connect Secure](./media/pulse-secure-pcs-tutorial/server-settings.png)

    a. Specificare un nome valido nella casella di testo **Server Name** (Nome del server).

    b. Selezionare 2.0 come valore di **SAML Version** (Versione SAML) e **Metadata** (Metadati) come valore di **Configuration Mode** (Modalità di configurazione).

    c. Copiare il valore di **Connect Secure Entity Id** (ID entità di Connect Secure) e incollarlo nella casella **Identifier URL** (URL identificatore) nella finestra di dialogo **Configurazione SAML di base** nel portale di Azure.

    d. Selezionare il valore Azure AD Entity Id (ID entità di Azure AD) nell'elenco a discesa **Identity Provider Entity Id** (ID entità provider di identità).

    e. Selezionare il valore Azure AD Login URL (URL di accesso di Azure AD) nell'elenco a discesa **Identity Provider Single Sign-On Service URL** (URL del servizio SSO del provider di identità).

    f. **Single Logout** (Disconnessione singola) è un'impostazione facoltativa. Se questa opzione è selezionata, dopo la disconnessione viene richiesta una nuova autenticazione. Se questa opzione non è selezionata e il browser non è stato chiuso, è possibile riconnettersi senza autenticazione.

    g. Selezionare **Password** come valore di **Requested Authn Context Class** (Classe del contesto di autenticazione richiesto) e **exact** (esatto) come valore di **Comparison Method** (Metodo di confronto).

    h. Impostare un numero di giorni per **Metadata Validity** (Validità dei metadati).
    
    i. Fare clic su **Save Changes** (Salva modifiche).

### <a name="create-pulse-secure-pcs-test-user"></a>Creare l'utente di test di Pulse Secure PCS

In questa sezione viene creato un utente di nome Britta Simon in Pulse Secure PCS. Collaborare con il [team di supporto di Pulse Secure PCS](mailto:support@pulsesecure.net) per aggiungere gli utenti alla piattaforma Pulse Secure PCS. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

1. Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di Pulse Secure PCS, in cui è possibile avviare il flusso di accesso. 

2. Passare direttamente all'URL di accesso di Pulse Secure PCS e avviare il flusso di accesso da questa posizione.

3. È possibile usare il Pannello di accesso Microsoft. Quando si fa clic sul riquadro di Pulse Secure PCS nel pannello di accesso, si verrà reindirizzati all'URL di accesso di Pulse Secure PCS. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Pulse Secure PCS, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


