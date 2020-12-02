---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con GitHub Enterprise Cloud - Enterprise Account | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e GitHub Enterprise Cloud - Enterprise Account.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/29/2020
ms.author: jeedes
ms.openlocfilehash: d88cbb79b42637721412dd0a35c231782a896721
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96029864"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-github-enterprise-cloud---enterprise-account"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con GitHub Enterprise Cloud - Enterprise Account

Questa esercitazione descrive come integrare GitHub Enterprise Cloud - Enterprise Account con Azure Active Directory (Azure AD). Integrando GitHub Enterprise Cloud - Enterprise Account con Azure AD, è possibile:

* Controllare in Azure AD quali utenti sono autorizzati ad accedere a un'istanza di GitHub Enterprise Account e a eventuali organizzazioni entro Enterprise Account.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Un'istanza di [GitHub Enterprise Account](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-enterprise/about-enterprise-accounts)
* Un account utente GitHub proprietario di Enterprise Account. 

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* GitHub Enterprise Cloud - Enterprise Account supporta l'accesso SSO avviato da **SP** e **IDP**
* GitHub Enterprise Cloud - Enterprise Account supporta il provisioning utenti **JIT**
* Dopo aver configurato GitHub Enterprise Cloud - Enterprise Account, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-github-enterprise-cloud---enterprise-account-from-the-gallery"></a>Aggiunta di GitHub Enterprise Cloud - Enterprise Account dalla raccolta

Per configurare l'integrazione di GitHub Enterprise Cloud - Enterprise Account in Azure AD, è necessario aggiungere GitHub Enterprise Cloud - Enterprise Account dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **GitHub Enterprise Cloud - Enterprise Account** nella casella di ricerca.
1. Selezionare **GitHub Enterprise Cloud - Enterprise Account** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-sso-for-github-enterprise-cloud---enterprise-account"></a>Configurare e testare l'accesso SSO di Azure AD per GitHub Enterprise Cloud - Enterprise Account

Configurare e testare l'accesso SSO di Azure AD con GitHub Enterprise Cloud - Enterprise Account usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in GitHub Enterprise Cloud - Enterprise Account.

Per configurare e testare l'accesso SSO di Azure AD con GitHub Enterprise Cloud - Enterprise Account, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'account utente di Azure AD e l'account utente di test all'app GitHub](#assign-the-azure-ad-test-user)** : per abilitare l'account utente e l'utente di test `B.Simon` per l'uso dell'accesso Single Sign-On di Azure AD.
1. **[Abilitare e testare SAML per Enterprise Account e le rispettive organizzazioni](#enable-and-test-saml-for-the-enterprise-account-and-its-organizations)** : per configurare le impostazioni dell'accesso Single Sign-On sul lato applicazione.
    1. **[Testare l'accesso Single Sign-On con un altro proprietario Enterprise Account o account membro dell'organizzazione](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **GitHub Enterprise Cloud - Enterprise Account** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `https://github.com/enterprises/<ENTERPRISE-SLUG>`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://github.com/enterprises/<ENTERPRISE-SLUG>/saml/consume`

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

     Nella casella di testo **URL di accesso** digitare un URL usando il modello seguente: `https://github.com/enterprises/<ENTERPRISE-SLUG>/sso`

    > [!NOTE]
    > Sostituire `<ENTERPRISE-SLUG>` con il nome effettivo di GitHub Enterprise Account.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificateBase64.png)

1. Nella sezione **Configura GitHub Enterprise Cloud - Enterprise Account** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test denominato `B.Simon` nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Crea**.

<a name="assign-the-azure-ad-test-user"></a>

### <a name="assign-your-azure-ad-user-and-the-test-user-account-to-the-github-app"></a>Assegnare l'account utente di Azure AD e l'account utente di test all'app GitHub

In questa sezione si abiliterà `B.Simon` e l'account utente all'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a GitHub Enterprise Cloud - Enterprise Account.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **GitHub Enterprise Cloud - Enterprise Account**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** e il proprio account utente dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="enable-and-test-saml-for-the-enterprise-account-and-its-organizations"></a>Abilitare e testare SAML per Enterprise Account e le rispettive organizzazioni

Per configurare l'accesso Single Sign-On sul lato **GitHub Enterprise Cloud - Enterprise Account**, seguire la procedura illustrata in [questa documentazione di GitHub](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-enterprise/enforcing-security-settings-in-your-enterprise-account#enabling-saml-single-sign-on-for-organizations-in-your-enterprise-account). 
1. Accedere a GitHub.com con un account utente [proprietario di Enterprise Account](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-enterprise/roles-in-an-enterprise#enterprise-owner). 
1. Copiare il valore dal campo `Login URL` nell'app dal portale di Azure e incollarlo nel campo `Sign on URL` nelle impostazioni SAML di GitHub Enterprise Account. 
1. Copiare il valore dal campo `Azure AD Identifier` nell'app dal portale di Azure e incollarlo nel campo `Issuer` nelle impostazioni SAML di GitHub Enterprise Account. 
1. Copiare i contenuti del file **Certificato (Base64)** scaricato nei passaggi precedenti dal portale di Azure e incollarli nel campo appropriato nelle impostazioni SAML di GitHub Enterprise Account. 
1. Fare clic su `Test SAML configuration` e confermare che è possibile eseguire l'autenticazione di GitHub Enterprise Account in Azure AD.
1. Quando il test ha esito positivo, salvare le impostazioni. 
1. Dopo l'esecuzione dell'autenticazione tramite SAML per la prima volta da GitHub Enterprise Account, verrà creata un'_identità esterna collegata_ in GitHub Enterprise Account che associa l'account utente connesso di GitHub con l'account utente di Azure AD.  
 
Dopo l'abilitazione dell'accesso Single Sign-On SAML per l'account GitHub Enterprise, l'accesso Single Sign-On SAML sarà abilitato per impostazione predefinita per tutte le organizzazioni di proprietà dell'account Enterprise. Tutti i membri dovranno eseguire l'autenticazione usando l'accesso Single Sign-On SAML per ottenere l'accesso alle organizzazioni di cui sono membri e i proprietari di Enterprise Account dovranno eseguire l'autenticazione usando l'accesso Single Sign-On quando accedono a un'istanza di Enterprise Account.

<a name="test-sso"></a>

## <a name="test-sso-with-another-enterprise-account-owner-or-organization-member-account"></a>Testare l'accesso Single Sign-On con un altro proprietario di Enterprise Account o account membro dell'organizzazione

Dopo la configurazione dell'integrazione SAML per GitHub Enterprise Account, applicabile anche alle organizzazioni di GitHub in Enterprise Account, gli altri proprietari di Enterprise Account assegnati all'app in Azure AD dovrebbero riuscire a passare all'URL (`https://github.com/enterprises/<enterprise account>`) di GitHub Enterprise Account, eseguire l'autenticazione tramite SAML e accedere a criteri e impostazioni con GitHub Enterprise Account. 

Un proprietario dell'organizzazione per un'organizzazione in un'istanza di Enterprise Account dovrebbe riuscire a [invitare un utente a entrare nella propria organizzazione di GitHub](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-organizations-and-teams/inviting-users-to-join-your-organization). Accedere a GitHub.com con un account di tipo proprietario dell'organizzazione e seguire la procedura illustrata nell'articolo per invitare `B.Simon` nell'organizzazione. Sarà necessario creare un account utente di GitHub per `B.Simon`, se non ne esiste già uno. 

Per testare l'accesso all'organizzazione di GitHub in Enterprise Account con l'account utente di test `B.Simon`:
1. Invitare `B.Simon` a un'organizzazione in Enterprise Account come proprietario dell'organizzazione. 
1. Accedere a GitHub.com usando l'account utente da collegare all'account utente `B.Simon` di Azure AD.
1. Accedere ad Azure AD usando l'account utente `B.Simon`.
1. Passare all'organizzazione di GitHub. Deve essere chiesto all'utente di eseguire l'autenticazione tramite SAML. Dopo l'autenticazione SAML, `B.Simon` dovrebbe riuscire ad accedere alle risorse dell'organizzazione. 

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](./tutorial-list.md)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)

- [Provare GitHub Enterprise Cloud - Enterprise Account con Azure AD](https://aad.portal.azure.com/)

- [Informazioni sul controllo sessioni in Microsoft Cloud App Security](/cloud-app-security/proxy-intro-aad)

- [Come proteggere GitHub Enterprise Cloud - Enterprise Account con visibilità e controlli avanzati](/cloud-app-security/proxy-intro-aad)
