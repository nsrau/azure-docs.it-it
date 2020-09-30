---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Elium | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory ed Elium.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: jeedes
ms.openlocfilehash: e7fc8f68bf344b689d6d4ebb49fde8d43ebcaccd
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/13/2020
ms.locfileid: "90053657"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-elium"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Elium

Questa esercitazione descrive come integrare Elium con Azure Active Directory (Azure AD). Integrando Elium con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Elium.
* Abilitare gli utenti per l'accesso automatico a Elium con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Elium abilitata per l'accesso Single Sign-On (SSO).

> [!NOTE]
> È possibile usare questa integrazione anche dall'ambiente cloud US Government di Azure AD. Questa applicazione è disponibile nella raccolta di applicazioni cloud US Government di Azure AD e la procedura di configurazione è analoga a quella eseguita dal cloud pubblico.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Elium supporta l'accesso SSO avviato da **SP e IDP**
* Elium supporta il provisioning utenti **JIT**

## <a name="adding-elium-from-the-gallery"></a>Aggiunta di Elium dalla raccolta

Per configurare l'integrazione di Elium in Azure AD, è necessario aggiungere Elium dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Elium** nella casella di ricerca.
1. Selezionare **Elium** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-elium"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Elium

Configurare e testare l'accesso SSO di Azure AD con Elium usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Elium.

Per configurare e testare l'accesso SSO di Azure AD con Elium, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    * **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    * **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Elium](#configure-elium-sso)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
    * **[Creare l'utente di test di Elium](#create-elium-test-user)**: per avere una controparte di B.Simon in Elium collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Elium** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<platform-domain>.elium.com/login/saml2/metadata`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<platform-domain>.elium.com/login/saml2/acs`

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<platform-domain>.elium.com/login/saml2/login`

    > [!NOTE]
    > Poiché questi non sono i valori reali, È possibile ottenere questi valori dal **file di metadati SP** che è possibile scaricare all'indirizzo `https://<platform-domain>.elium.com/login/saml2/metadata` e che viene illustrato più avanti in questa esercitazione.

1. L'applicazione Elium prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/default-attributes.png)

1. Oltre quelli elencati in precedenza, l'applicazione Elium prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.

    | Nome | Attributo di origine|
    | ---------------| ----------------|
    | email   |user.mail |
    | first_name| user.givenname |
    | last_name| user.surname|
    | job_title| user. jobtitle|
    | company| user.companyname|

    > [!NOTE]
    > Queste sono le attestazioni predefinite. **Solo l'attestazione email è obbligatoria**. Anche per il provisioning JIT è obbligatoria solo l'attestazione email. Altre attestazioni personalizzate possono variare in base alla piattaforma del cliente.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il file **XML dei metadati della federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura Elium** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Elium.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Elium**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-elium-sso"></a>Configurare l'accesso Single Sign-On di Elium

1. Per automatizzare la configurazione all'interno di Elium, è necessario installare l'**estensione del browser per l'accesso sicuro ad App personali** facendo clic su **Installa l'estensione**.

    ![Estensione MyApps](common/install-myappssecure-extension.png)

1. Dopo aver aggiunto l'estensione al browser, fare clic su **Configura Elium** per passare direttamente all'applicazione Elium. Nell'applicazione specificare le credenziali di amministratore per accedere a Elium. L'estensione del browser configurerà automaticamente l'applicazione e automatizzerà i passaggi da 3 a 6.

    ![Eseguire la configurazione](common/setup-sso.png)

1. Se si vuole configurare manualmente Elium, aprire una nuova finestra del Web browser, accedere al sito aziendale di Elium come amministratore e seguire questa procedura:

1. Fare clic sul **profilo utente** nell'angolo in alto a destra e quindi selezionare **Settings** (Impostazioni).

    ![Configurare l'accesso Single Sign-On - Elium 01](./media/elium-tutorial/elium-01.png)

1. Selezionare **Security** (Sicurezza) in **Advanced** (Avanzate).

    ![Configurare l'accesso Single Sign-On - Elium 02](./media/elium-tutorial/elium-02.png)

1. Scorrere fino alla sezione **Single sign-on (SSO)** e seguire questa procedura:

    ![Configurare l'accesso Single Sign-On - Elium 03](./media/elium-tutorial/elium-03.png)

    a. Copiare il valore di **Verify that SAML2 authentication works for your account** (Verificare il funzionamento dell'autenticazione SAML2 per l'account) e incollarlo nella casella di testo **URL accesso** nella sezione **Configurazione SAML di base** del portale di Azure.

    > [!NOTE]
    > Dopo avere configurato SSO, è sempre possibile accedere alla pagina di accesso remoto all'URL seguente: `https://<platform_domain>/login/regular/login` 

    b. Selezionare la casella di controllo **Enable SAML2 federation** (Abilita federazione SAML2).

    c. Selezionare la casella di controllo **JIT Provisioning** (Provisioning JIT).

    d. Aprire **SP Metadata** (Metadati SP) facendo clic su **Download**.

    e. Cercare **entityID** nel file **SP Metadata** (Metadati SP), copiare il valore di **entityID** e incollarlo nella casella di testo **Identificatore** nella sezione **Configurazione SAML di base** del portale di Azure. 

    ![Configurare l'accesso Single Sign-On - Elium 04](./media/elium-tutorial/elium-04.png)

    f. Cercare **AssertionConsumerService** nel file **SP Metadata** (Metadati SP), copiare il valore di **Location** e incollarlo nella casella di testo **URL di risposta** nella sezione **Configurazione SAML di base** del portale di Azure.

    ![Configurare l'accesso Single Sign-On - Elium 05](./media/elium-tutorial/elium-05.png)

    g. Copiare il contenuto del file dei metadati scaricato dal portale di Azure nel Blocco note e incollarlo nella casella di testo **IdP Metadata** (Metadati IdP).

    h. Fare clic su **Salva**.

### <a name="create-elium-test-user"></a>Creare l'utente di test di Elium

In questa sezione viene creato un utente di nome B.Simon in Elium. Elium supporta il **provisioning JIT**, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in Elium, ne viene creato uno nuovo quando si prova ad accedere a Elium.

> [!Note]
> Se è necessario creare un utente manualmente, contattare il [team di supporto di Elium](mailto:support@elium.com).

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 
 
#### <a name="sp-initiated"></a>Avviato da SP:
 
* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di Elium, in cui è possibile avviare il flusso di accesso.  
 
* Passare direttamente all'URL di accesso di Elium e avviare il flusso di accesso da questa posizione.
 
#### <a name="idp-initiated"></a>Avviato da IDP:
 
* Dopo aver fatto clic su **Test this application** (Testa questa applicazione) nel portale di Azure, si dovrebbe accedere automaticamente all'istanza di Elium per cui si è configurato l'accesso SSO 
 
È anche possibile usare il pannello di accesso Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro di Elium nel pannello di accesso, se è stato configurato in modalità SP, si dovrebbe essere reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso; se invece è configurato in modalità IDP, si dovrebbe accedere automaticamente all'istanza di Elium per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Glint Inc, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
