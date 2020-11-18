---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Sonarqube | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Sonarqube.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/29/2020
ms.author: jeedes
ms.openlocfilehash: 455c15ec97d5621b51a4d8af87cc3a2968dd65dd
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93095976"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sonarqube"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Sonarqube

Questa esercitazione descrive come integrare Sonarqube con Azure Active Directory (Azure AD). Integrando Sonarqube con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Sonarqube.
* Abilitare gli utenti per l'accesso automatico a Sonarqube con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Sonarqube abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Sonarqube supporta l'accesso SSO avviato da **SP**

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.

## <a name="adding-sonarqube-from-the-gallery"></a>Aggiunta di Sonarqube dalla raccolta

Per configurare l'integrazione di Sonarqube in Azure AD, è necessario aggiungere Sonarqube dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Sonarqube** nella casella di ricerca.
1. Selezionare **Sonarqube** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-sonarqube"></a>Configurare e testare l'accesso SSO di Azure AD per Sonarqube

Configurare e testare l'accesso SSO di Azure AD con Sonarqube usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Sonarqube.

Per configurare e testare l'accesso SSO di Azure AD con Sonarqube, completare la procedura seguente:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Sonarqube](#configure-sonarqube-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Sonarqube](#create-sonarqube-test-user)** : per avere una controparte di B.Simon in Sonarqube collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Sonarqube** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    Nella casella di testo **URL di accesso** digitare un URL:

    * **Per l'ambiente di produzione**

        `https://servicessonar.corp.microsoft.com/`

    * **Per l'ambiente di sviluppo**

        `https://servicescode-dev.westus.cloudapp.azure.com`

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura Sonarqube** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Sonarqube.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Sonarqube**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".

1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-sonarqube-sso"></a>Configurare l'accesso Single Sign-On di Sonarqube

1. Aprire una nuova finestra del Web browser e accedere al sito aziendale di Sonarqube come amministratore.

1. Fare clic su **Administration > Configuration > Security** (Amministrazione > Configurazione > Sicurezza), passare a **SAML Plugin** (Plug-in SAML) e quindi seguire questa procedura.

1. Copiare i dettagli seguenti dai metadati dell'IdP e incollarli nei campi di testo corrispondenti nel plug-in SonarQube.
    1. IdP Entity ID (ID entità IdP)
    2. URL di accesso
    3. Certificato X.509 
1. Salvare tutti i dettagli.
    ![IDP plug-in SAML](./media/sonarqube-tutorial/sso-idp-metadata.png)

1. Nella pagina **SAML** seguire questa procedura:

    ![Configurazione di Sonarqube](./media/sonarqube-tutorial/config01.png)

    a. Impostare l'opzione **Enabled** (Abilitato) **yes** (sì).

    b. Nella casella di testo **Application ID** (ID applicazione) immettere il nome, ad esempio **sonarqube**.

    c. Nella casella di testo **Provider Name** (Nome provider) immettere il nome, ad esempio **SAML**.

    d. Nella casella di testo **Provider ID** (ID Provider) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    e. Nella casella di testo **SAML login url** (URL di accesso SAML) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    f. Aprire il certificato con codifica Base64, copiarne il contenuto e incollarlo nella casella di testo **Provider Certificate** (Certificato Provider).

    g. Nella casella di testo **SAML user login attribute** (Attributo accesso utente SAML) immettere il valore `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    h. Nella casella di testo **SAML user name attribute** (Attributo nome utente SAML) immettere il valore `http://schemas.microsoft.com/identity/claims/displayname`.

    i. Nella casella di testo **SAML user email attribute** (Attributo posta elettronica utente SAML) immettere il valore `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    j. Fare clic su **Salva**.

### <a name="create-sonarqube-test-user"></a>Creare l'utente di test di Sonarqube

In questa sezione viene creato un utente di nome B.Simon in Sonarqube. Collaborare con il [team di supporto clienti di Sonarqube](https://www.sonarsource.com/support/) per aggiungere gli utenti alla piattaforma Sonarqube. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On. 

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

1. Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di Sonarqube, in cui è possibile avviare il flusso di accesso. 

2. Passare direttamente all'URL di accesso di Sonarqube e avviare il flusso di accesso da questa posizione.

3. È possibile usare il Pannello di accesso Microsoft. Quando si fa clic sul riquadro di Sonarqube nel pannello di accesso, si verrà reindirizzati all'URL di accesso di Sonarqube. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Passaggi successivi

* Dopo aver configurato Sonarqube, è possibile applicare i controlli sessione che consentono di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. I controlli sessione costituiscono un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
