---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Smart Global Governance"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Smart Global Governance.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/04/2020
ms.author: jeedes
ms.openlocfilehash: 5a2169db47ca5a6adcddbcc9558161370b896a62
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92509557"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-smart-global-governance"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Smart Global Governance

Questa esercitazione descrive come integrare Smart Global Governance con Azure Active Directory (Azure AD). Integrando Smart Global Governance con Azure AD, è possibile:

* Usare Azure AD per controllare chi può accedere a Smart Global Governance.
* Abilitare gli utenti per l'accesso automatico a Smart Global Governance con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Smart Global Governance abilitata per l'accesso Single Sign-On (SSO).

## <a name="tutorial-description"></a>Descrizione dell'esercitazione

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

Smart Global Governance supporta l'accesso SSO avviato da SP e da IDP.

Dopo aver configurato Smart Global Governance, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. I controlli sessione costituiscono un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-smart-global-governance-from-the-gallery"></a>Aggiungere Smart Global Governance dalla raccolta

Per configurare l'integrazione di Smart Global Governance in Azure AD, è necessario aggiungere Smart Global Governance dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro sinistro selezionare **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere un'applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Smart Global Governance** nella casella di ricerca.
1. Selezionare **Smart Global Governance** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-smart-global-governance"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Smart Global Governance

Verrà configurato e testato l'accesso SSO di Azure AD con Smart Global Governance usando un utente di test di nome B.Simon. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente corrispondente in Smart Global Governance.

Per configurare e testare l'accesso SSO di Azure AD con Smart Global Governance, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** per consentire agli utenti di usare la funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** per testare l'accesso Single Sign-On di Azure AD.
    1. **[Concedere l'accesso all'utente di test](#grant-access-to-the-test-user)** per consentire all'utente di usare l'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Smart Global Governance](#configure-smart-global-governance-sso)** sul lato applicazione.
    1. **[Creare l'utente di test di Smart Global Governance](#create-a-smart-global-governance-test-user)** come controparte della rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso SSO](#test-sso)** per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Smart Global Governance** del [portale di Azure](https://portal.azure.com/) passare alla sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante a forma di matita relativo a **Configurazione SAML di base** per modificare le impostazioni:

   ![Pulsante a forma di matita per Configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione nella modalità avviata da IDP.

    a. Nella casella **Identificatore** immettere uno degli URL seguenti:

    - `https://eu-fr-south.console.smartglobalprivacy.com/platform/authentication-saml2/metadata`
    - `https://eu-fr-south.console.smartglobalprivacy.com/dpo/authentication-saml2/metadata`

    b. Nella casella **URL di risposta** immettere uno degli URL seguenti:

    - `https://eu-fr-south.console.smartglobalprivacy.com/platform/authentication-saml2/acs`
    - `https://eu-fr-south.console.smartglobalprivacy.com/dpo/authentication-saml2/acs`

1. Per configurare l'applicazione in modalità avviata da SP, selezionare **Impostare URL aggiuntivi** e completare il passaggio seguente.

   - Nella casella **URL di accesso** immettere uno degli URL seguenti:

    - `https://eu-fr-south.console.smartglobalprivacy.com/dpo`
    - `https://eu-fr-south.console.smartglobalprivacy.com/platform`

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** selezionare il collegamento **Scarica** per **Certificato (base)** per scaricare il certificato e salvarlo nel computer:

    ![Collegamento di download del certificato](common/certificateraw.png)

1. Nella sezione **Configura Smart Global Governance** copiare l'URL o gli URL appropriati in base alle esigenze:

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**. Selezionare **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** completare questa procedura:
   1. Nella casella **Nome** immettere **B.Simon**.  
   1. Nella casella **Nome utente** immettere \<username>@\<companydomain>.\<extension>. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Selezionare **Crea**.

### <a name="grant-access-to-the-test-user"></a>Concedere l'accesso all'utente di test

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendo a tale utente l'accesso a Smart Global Governance.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Smart Global Governance**.
1. Nella sezione **Gestione** della pagina di panoramica dell'app selezionare **Utenti e gruppi** :

   ![Selezionare Utenti e gruppi](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione** :

    ![Selezionare Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** nell'elenco **Utenti** e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Assegna**.

## <a name="configure-smart-global-governance-sso"></a>Configurare l'accesso Single Sign-On di Smart Global Governance

Per configurare l'accesso Single Sign-On sul lato Smart Global Governance, è necessario inviare il certificato base scaricato e gli URL appropriati, copiati dal portale di Azure, al [team di supporto di Smart Global Governance](mailto:support.tech@smartglobal.com), che configurerà la connessione SSO SAML in modo che sia corretta su entrambi i lati.

### <a name="create-a-smart-global-governance-test-user"></a>Creare l'utente di test di Smart Global Governance

Collaborare con il [team di supporto di Smart Global Governance](mailto:support.tech@smartglobal.com) per aggiungere un utente di nome B.Simon in Smart Global Governance. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso SSO di Azure AD usando il pannello di accesso.

Quando si seleziona il riquadro di Smart Global Governance nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Smart Global Governance per cui si è configurato l'accesso SSO. Per altre informazioni sul Pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Esercitazioni su come integrare le app SaaS con Azure Active Directory](./tutorial-list.md)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)

- [Provare Smart Global Governance con Azure AD](https://aad.portal.azure.com/)

- [Informazioni sul controllo sessioni in Microsoft Cloud App Security](/cloud-app-security/proxy-intro-aad)

- [Come proteggere Smart Global Governance con visibilità e controlli avanzati](/cloud-app-security/proxy-intro-aad)