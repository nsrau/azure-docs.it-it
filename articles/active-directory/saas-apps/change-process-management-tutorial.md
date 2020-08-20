---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Change Process Management"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Change Process Management.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/07/2020
ms.author: jeedes
ms.openlocfilehash: 501f19b2022d221ad6facd66c483790b13491ba0
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88529679"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-change-process-management"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Change Process Management

Questa esercitazione descrive come integrare Change Process Management con Azure Active Directory (Azure AD). Integrando Change Process Management con Azure AD, è possibile:

* Usare Azure AD per controllare chi può accedere a Change Process Management.
* Abilitare gli utenti per l'accesso automatico a Change Process Management con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Change Process Management abilitata per l'accesso Single Sign-On (SSO).

## <a name="tutorial-description"></a>Descrizione dell'esercitazione

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

Change Process Management supporta l'accesso SSO avviato da IDP.

Dopo aver configurato Change Process Management, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. I controlli sessione costituiscono un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-change-process-management-from-the-gallery"></a>Aggiungere Change Process Management dalla raccolta

Per configurare l'integrazione di Change Process Management in Azure AD, è necessario aggiungere Change Process Management dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro sinistro selezionare **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere un'applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Change Process Management** nella casella di ricerca.
1. Selezionare **Change Process Management** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-change-process-management"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Change Process Management

Verrà configurato e testato l'accesso SSO di Azure AD con Change Process Management usando un utente di test di nome B.Simon. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente corrispondente in Change Process Management.

Per configurare e testare l'accesso SSO di Azure AD con Change Process Management, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** per consentire agli utenti di usare la funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** per testare l'accesso Single Sign-On di Azure AD.
    1. **[Concedere l'accesso all'utente di test](#grant-access-to-the-test-user)** per consentire all'utente di usare l'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Change Process Management](#configure-change-process-management-sso)** sul lato applicazione.
    1. **[Creare l'utente di test di Change Process Management](#create-a-change-process-management-test-user)** come controparte della rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso SSO](#test-sso)** per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Change Process Management** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante a forma di matita relativo a **Configurazione SAML di base** per modificare le impostazioni:

   ![Pulsante a forma di matita per Configurazione SAML di base](common/edit-urls.png)

1. Nella pagina **Configura l'accesso Single Sign-On con SAML** seguire questa procedura:

    a. Nella casella **Identificatore** immettere un URL nel formato seguente: `https://<hostname>:8443/`

    b. Nella casella **URL di risposta** immettere un URL nel formato seguente: `https://<hostname>:8443/changepilot/saml/sso`

    > [!NOTE]
    > I valori di **Identificatore** e **URL di risposta** non sono quelli effettivi da usare. Per ottenere i valori effettivi, contattare il [team di supporto di Change Process Management](mailto:support@realtech-us.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** selezionare il collegamento **Scarica** per **Certificato (Base64)** per scaricare il certificato e salvarlo nel computer:

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura Change Process Management** copiare l'URL o gli URL appropriati in base alle esigenze:

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendo a tale utente l'accesso a Change Process Management.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Change Process Management**.
1. Nella sezione **Gestione** della pagina di panoramica dell'app selezionare **Utenti e gruppi**:

   ![Selezionare Utenti e gruppi](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Selezionare Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** nell'elenco **Utenti** e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Assegna**.

## <a name="configure-change-process-management-sso"></a>Configurare l'accesso Single Sign-On di Change Process Management

Per configurare l'accesso Single Sign-On sul lato Change Process Management, è necessario inviare il certificato Base64 scaricato e gli URL appropriati, copiati dal portale di Azure, al [team di supporto di Change Process Management](mailto:support@realtech-us.com), che configurerà la connessione SSO SAML in modo che sia corretta su entrambi i lati.

### <a name="create-a-change-process-management-test-user"></a>Creare l'utente di test di Change Process Management
 Collaborare con il [team di supporto di Change Process Management](mailto:support@realtech-us.com) per aggiungere un utente di nome B.Simon a Change Process Management. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso SSO di Azure AD usando il pannello di accesso.

Quando si seleziona il riquadro di Change Process Management nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Change Process Management per cui si è configurato l'accesso SSO. Per altre informazioni sul Pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Esercitazioni su come integrare le app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare Change Process Management con Azure AD](https://aad.portal.azure.com/)

- [Informazioni sul controllo sessioni in Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Come proteggere Change Process Management con visibilità e controlli avanzati](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)