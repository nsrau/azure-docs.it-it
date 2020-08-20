---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con WEDO | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e WEDO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/22/2020
ms.author: jeedes
ms.openlocfilehash: 364bb71ff9ec1cc948f9631f50ac4444c16ea4d1
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88523389"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-wedo"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con WEDO

Questa esercitazione descrive come integrare WEDO con Azure Active Directory (Azure AD). Integrando WEDO with Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a WEDO.
* Abilitare gli utenti per l'accesso automatico a WEDO con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di WEDO abilitata per l'accesso Single Sign-On (SSO). Per ottenere una sottoscrizione SSO, contattare il [team di supporto clienti di WEDO](mailto:info@wedo.swiss).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* WEDO supporta l'accesso SSO avviato da **SP e IDP**

* [Dopo aver configurato WEDO, è possibile applicare i controlli sessione che consentono di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. I controlli sessione costituiscono un'estensione dell'accesso condizionale. Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-wedo-from-the-gallery"></a>Aggiunta di WEDO dalla raccolta

Per configurare l'integrazione di WEDO in Azure AD, è necessario aggiungere WEDO dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **WEDO** nella casella di ricerca.
1. Selezionare **WEDO** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-wedo"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per WEDO

Configurare e testare l'accesso SSO di Azure AD con WEDO usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in WEDO.

Per configurare e testare l'accesso SSO di Azure AD con WEDO, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    * **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    * **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di WEDO](#configure-wedo-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    * **[Creare l'utente di test di WEDO](#create-wedo-test-user)** : per avere una controparte di B.Simon in WEDO collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **WEDO** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<SUBDOMAIN>.wedo.swiss/sp/acs`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<SUBDOMAIN>.wedo.swiss/sp/acs`

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<SUBDOMAIN>.wedo.swiss/`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori contattare il [team di supporto clienti di WEDO](mailto:info@wedo.swiss). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. L'applicazione WEDO prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    | Nome | Attributo di origine|
    | ------------ | --------- |
    | email | user.email |
    | firstName | user.firstName |
    | lastName | user.lasttName |
    | userName | user.userName |

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il file **XML dei metadati della federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura WEDO** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a WEDO.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **WEDO**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-wedo-sso"></a>Configurare l'accesso Single Sign-On di WEDO

Per abilitare l'accesso Single Sign-On di Azure AD in WEDO, seguire questa procedura.

1. Accedere a [WEDO](https://login.wedo.swiss/). È necessario disporre del **ruolo di amministratore**.
1. Nelle impostazioni del profilo selezionare il menu **Authentication** (Autenticazione) nella sezione **Network settings** (Impostazioni di rete).
1. Nella pagina **SAML Authentication** (Autenticazione SAML) seguire questa procedura:

   ![Collegamento per SAML Authentication](media/wedo-tutorial/network-security-authentification.png)

   a. Abilitare **SAML Authentication** (Autenticazione SAML).

   b. Selezionare la scheda **Identity Provider metadata (XML)** (Metadati provider di identità (XML)).

   c. Aprire nel Blocco note il file **XML dei metadati della federazione**, scaricato dal portale di Azure, quindi copiarne il contenuto e incollarlo nella casella di testo **X.509 Certificate** (Certificato X.509).

   d. Fare clic su **Salva**

### <a name="create-wedo-test-user"></a>Creare l'utente di test di WEDO

In questa sezione verrà creato un utente di test di nome Bob Simon in WEDO. Le informazioni devono corrispondere a quelle specificate in *Creare un utente di test di Azure AD*.

1. Nelle impostazioni del profilo in WEDO selezionare **Users** (Utenti) nella sezione *Network settings* (Impostazioni di rete).
1. Fare clic su **Add User**.
1. Nella finestra popup Add user (Aggiungi utente) inserire le informazioni sull'utente.

    a. Nome: `B`.

    b. Cognome: `Simon`.

    c. Immettere l'indirizzo di posta elettronica `username@companydomain.extension`. Ad esempio: `B.Simon@contoso.com`. Il dominio dell'indirizzo di posta elettronica deve essere identico al nome breve della società.

    d. Tipo di utente: `User`.

    e. Fare clic su **Create User** (Crea utente).

    f. Nella pagina *Select teams* (Seleziona team) fare clic su **Save** (Salva).

    g.  Nella pagina *Invite user* (Invita utente) fare clic su **Yes** (Sì).

1. Convalidare l'utente usando il collegamento ricevuto tramite posta elettronica

> [!NOTE]
> Se si vuole creare un utente fittizio (l'indirizzo di posta elettronica specificato in precedenza non esiste nella propria rete), contattare il [supporto](mailto:info@wedo.swiss) per convalidare l'utente*.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di WEDO nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di WEDO per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare WEDO con Azure AD](https://aad.portal.azure.com/)

- [Informazioni sul controllo sessioni in Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Come proteggere WEDO con visibilità e controlli avanzati](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
