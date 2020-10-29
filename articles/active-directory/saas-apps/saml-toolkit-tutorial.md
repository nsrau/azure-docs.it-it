---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Azure AD SAML Toolkit | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Azure AD SAML Toolkit.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: 26618382223a87f779f95452000a39126f37efbb
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675432"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-azure-ad-saml-toolkit"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Azure AD SAML Toolkit

Questa esercitazione descrive come integrare Azure AD SAML Toolkit con Azure Active Directory (Azure AD). Integrando Azure AD SAML Toolkit con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere ad Azure AD SAML Toolkit.
* Abilitare gli utenti per l'accesso automatico ad Azure AD SAML Toolkit con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Azure AD SAML Toolkit abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Azure AD SAML Toolkit supporta l'accesso SSO avviato da **SP**

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.

## <a name="adding-azure-ad-saml-toolkit-from-the-gallery"></a>Aggiunta di Azure AD SAML Toolkit dalla raccolta

Per configurare l'integrazione di Azure AD SAML Toolkit in Azure AD, è necessario aggiungere Azure AD SAML Toolkit dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory** .
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni** .
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione** .
1. Nella sezione **Aggiungi dalla raccolta** digitare **Azure AD SAML Toolkit** nella casella di ricerca.
1. Selezionare **Azure AD SAML Toolkit** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-azure-ad-saml-toolkit"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Azure AD SAML Toolkit

Configurare e testare l'accesso SSO di Azure AD con Azure AD SAML Toolkit usando un utente di test di nome **B.Simon** . Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Azure AD SAML Toolkit.

Per configurare e testare l'accesso SSO di Azure AD con Azure AD SAML Toolkit, completare i passaggi seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    * **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    * **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Azure AD SAML Toolkit](#configure-azure-ad-saml-toolkit-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    * **[Creare l'utente di test di Azure AD SAML Toolkit](#create-azure-ad-saml-toolkit-test-user)** per avere una controparte di B.Simon in Azure AD SAML Toolkit collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Azure AD SAML Toolkit** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On** .
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML** .
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella pagina **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **URL di accesso** digitare l'URL: `https://samltoolkit.azurewebsites.net/`

    b. Nella casella di testo **URL di risposta** digitare il valore `https://samltoolkit.azurewebsites.net/SAML/Consume`

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (base)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificateraw.png)

1. Nella sezione **Configura Azure AD SAML Toolkit** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory** , **Utenti** e quindi **Tutti gli utenti** .
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password** .
   1. Fare clic su **Crea** .

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad Azure AD SAML Toolkit.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni** .
1. Nell'elenco delle applicazioni selezionare **Azure AD SAML Toolkit** .
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi** .
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione** .
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo** . Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna** .

## <a name="configure-azure-ad-saml-toolkit-sso"></a>Configurare l'accesso Single Sign-On per Azure AD SAML Toolkit

1. Aprire una nuova finestra del Web browser e, se non è ancora stata effettuata la registrazione nel sito Web di Azure AD SAML Toolkit, prima di tutto registrarsi facendo clic su **Register** (Registra). Se è già stata effettuata la registrazione, accedere al sito aziendale di Azure AD SAML Toolkit usando le credenziali di accesso registrate.

    ![Registrazione in Azure AD SAML Toolkit](./media/saml-toolkit-tutorial/register.png)

1. Fare clic su **SAML Configuration** (Configurazione SAML).

    ![Configurazione SAML in Azure AD SAML Toolkit](./media/saml-toolkit-tutorial/saml-configure.png)

1. Fare clic su **Crea** .

    ![Azure AD SAML Toolkit](./media/saml-toolkit-tutorial/createsso.png)

1. Nella pagina **SAML SSO Configuration** (Configurazione SSO SAML) seguire questa procedura:

    ![Configurazione della creazione dell'accesso SSO in Azure AD SAML Toolkit](./media/saml-toolkit-tutorial/fill-details.png)

    1. Nella casella di testo **Login URL** (URL di accesso) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    1. Nella casella di testo **Azure AD Identifier** (Identificatore Azure AD) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    1. Nella casella di testo **Logout URL** (URL disconnessione) incollare il valore di **URL disconnessione** copiato dal portale di Azure.

    1. Fare clic su **Choose File** (Scegli file) e caricare il file del **certificato (base)** scaricato dal portale di Azure.

    1. Fare clic su **Crea** .

    1. Copiare i valori di Sign-on URL (URL di accesso), Identifier (Identificatore) e ACS URL (URL ACS) nella pagina di configurazione dell'accesso SSO di Azure AD SAML Toolkit e incollarli nelle caselle di testo rispettive della **sezione Configurazione SAML di base** nel portale di Azure.

### <a name="create-azure-ad-saml-toolkit-test-user"></a>Creare l'utente di test di Azure AD SAML Toolkit

In questa sezione viene creato un utente di nome B.Simon in Azure AD SAML Toolkit. Per creare un utente di test nello strumento, registrare un nuovo utente e specificarne tutti i dettagli. 

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

1. Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di SAML Toolkit, in cui è possibile avviare il flusso di accesso. 

2. Passare direttamente all'URL di accesso di SAML Toolkit e avviare il flusso di accesso da questa posizione.

3. È possibile usare il Pannello di accesso Microsoft. Quando si seleziona il riquadro di SAML Toolkit nel Pannello di accesso, si dovrebbe accedere automaticamente all'istanza di SAML Toolkit per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Azure AD SAML Toolkit, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)