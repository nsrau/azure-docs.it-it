---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Workware | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Workware.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/21/2020
ms.author: jeedes
ms.openlocfilehash: f4996943fd95252be45f7a270d143f14e7f2d48a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96181272"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workware"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Workware

Questa esercitazione descrive come integrare Workware con Azure Active Directory (Azure AD). Integrando Workware con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Workware.
* Abilitare gli utenti per l'accesso automatico a Workware con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Workware abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Workware supporta l'accesso SSO avviato da **IDP**

## <a name="adding-workware-from-the-gallery"></a>Aggiunta di Workware dalla raccolta

Per configurare l'integrazione di Workware in Azure AD, è necessario aggiungere Workware dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Workware** nella casella di ricerca.
1. Selezionare **Workware** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-sso-for-workware"></a>Configurare e testare l'accesso SSO di Azure AD per Workware

Configurare e testare l'accesso SSO di Azure AD con Workware usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Workware.

Per configurare e testare l'accesso SSO di Azure AD con Workware, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    * **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    * **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Workware](#configure-workware-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    * **[Creare l'utente di test di Workware](#create-workware-test-user)** : per avere una controparte di B.Simon in Workware collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Workware** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella pagina **Configura l'accesso Single Sign-On con SAML** immettere i valori per i campi seguenti:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `<WORKWARE_URL>/WW/AuthServices`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `<WORKWARE_URL>/WW/AuthServices/Acs`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di risposta effettivi. Per ottenere tali valori contattare il [team di supporto client di Workware](mailto:support@activeops.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il file **XML dei metadati della federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura Workware** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Workware.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Workware**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-workware-sso"></a>Configurare l'accesso SSO per Workware

Per usare la funzionalità SSO in Workware, è necessario completare la configurazione seguente:

#### <a name="enable-sso-permissions-for-workware-system-administrators"></a>Abilitare le autorizzazioni per l'accesso SSO per gli amministratori di sistema di Workware

* Per consentire agli amministratori di sistema di Workware di configurare e abilitare l'apposita autorizzazione nella categoria **Administration > System configuration (Amministrazione > Configurazione di sistema) nella schermata System Settings > Permissions to Role** (Impostazioni di sistema > Autorizzazioni per il ruolo).

    ![Autorizzazione per l'autenticazione SSO](./media/workware-tutorial/permission.png)

#### <a name="set-up-sso-authentication-in-workware"></a>Configurare l'autenticazione SSO in Workware

1. Passare alla pagina **System Settings** (Impostazioni di sistema) e fare clic su **SSO Authentication** (Autenticazione SSO)

1. Nella sezione **SSO Authentication** (Autenticazione SSO) fare clic sul pulsante **Add SSO Authentication** (Aggiungi autenticazione SSO) e seguire questa procedura: 

    ![Autenticazione SSO](./media/workware-tutorial/authentication.png)

    1. In **External Identity Provider** (Provider di identità esterno) specificare il nome del provider di identità.
    1. Per **Authentication Type** (Tipo di autenticazione) selezionare **SAML2.0**
    1. Nella casella di testo **Identity Provider SignIn URL** (URL di accesso del provider di identità) incollare il valore di **URL di accesso** copiato dal portale di Azure.
    1. Nella casella di testo **Identity Provider Issuer URL** (URL autorità di certificazione provider di identità) immettere il valore di **Identificatore Azure AD** copiato dal portale di Azure.
    1. Nella casella di testo **Identity Provider Logout URL** (URL di disconnessione del provider di identità) incollare il valore di **URL di disconnessione** copiato dal portale di Azure.
    1. Fare clic su **Enable** (Abilita).
    1. Caricare in **Identity Provider Certificate** (Certificato del provider di identità) il **certificato** scaricato dal portale di Azure.
    1. Fare clic su **Save** (Salva).


### <a name="create-workware-test-user"></a>Creare un utente di test di Workware

1. Accedere al sito Web Workware come amministratore.

1. Selezionare **Admin > Create / View > User Accounts > Add New** (Amministratore > Crea/Visualizza > Account utente > Aggiungi nuovo)

1. Nella pagina successiva seguire questa procedura.

    ![Test user](./media/workware-tutorial/create-user.png)

    a. Specificare un nome valido nel campo **Name** (Nome).

    b. Selezionare **SSO** per **Authentication Type** (Tipo di autenticazione).

    c. Compilare i campi obbligatori e fare clic su **Save** (Salva).

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti.

* Dopo aver fatto clic su Test this application (Testa questa applicazione) nel portale di Azure, si dovrebbe accedere automaticamente all'istanza di Workware per cui si è configurato l'accesso SSO

* È possibile usare il Pannello di accesso Microsoft. Quando si fa clic sul riquadro di Workware nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Workware per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Workware, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).