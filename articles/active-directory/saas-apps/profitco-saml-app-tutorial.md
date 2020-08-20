---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Profit.co | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Profit.co.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/30/2020
ms.author: jeedes
ms.openlocfilehash: 669ab403d738a12a7d4b3093fe50b9cf55a75510
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88553496"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-profitco"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Profit.co

Questa esercitazione descrive come integrare Profit.co con Azure Active Directory (Azure AD). Integrando Profit.co con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Profit.co.
* Abilitare gli utenti per l'accesso automatico a Profit.co con gli account Azure AD personali.
* Gestire gli account in una posizione centrale, il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS (Software as a Service) con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Profit.co abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Profit.co supporta l'accesso SSO avviato da IDP.

* Dopo aver configurato Profit.co, è possibile applicare il controllo sessione, che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-profitco-from-the-gallery"></a>Aggiungere Profit.co dalla raccolta

Per configurare l'integrazione di Profit.co in Azure AD, è necessario aggiungere Profit.co dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Profit.co** nella casella di ricerca.
1. Selezionare **Profit.co** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-profitco"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Profit.co

Configurare e testare l'accesso SSO di Azure AD con Profit.co usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Profit.co.

Ecco la procedura generale per configurare e testare l'accesso SSO di Azure AD con Profit.co:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** per consentire a B.Simon di usare l'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Profit.co](#configure-profitco-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Profit.co](#create-a-profitco-test-user)** : per avere una controparte di B.Simon in Profit.co. Tale controparte è collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Profit.co** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Screenshot della pagina Configura l'accesso Single Sign-On con SAML, in cui è evidenziata l'icona della matita](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** l'applicazione è preconfigurata e gli URL necessari sono già prepopolati con Azure. Gli utenti devono salvare la configurazione facendo clic sul pulsante **Salva**.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** selezionare il pulsante **Copia**. per copiare l'**URL dei metadati di federazione dell'app** e salvarlo nel computer.

    ![Screenshot della sezione Certificato di firma SAML con il pulsante di copia evidenziato](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione viene creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory** > **Utenti** > **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nel campo **Password**.
   1. Selezionare **Create** (Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Profit.co.

1. Nel portale di Azure selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Profit.co**.
1. Nella pagina di panoramica dell'app individuare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Screenshot della sezione Gestione con Utenti e gruppi evidenziato](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente**. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Utenti e gruppi**.

    ![Screenshot della pagina Utenti e gruppi, in cui è evidenziato il pulsante Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** nell'elenco degli utenti. Fare quindi clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco. Fare quindi clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Assegna**.

## <a name="configure-profitco-sso"></a>Configurare l'accesso Single Sign-On di Profit.co

Per configurare l'accesso Single Sign-On sul lato Profit.co, è necessario inviare l'URL dei metadati di federazione dell'app al [team di supporto di Profit.co](mailto:support@profit.co). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-a-profitco-test-user"></a>Creare l'utente di test di Profit.co

In questa sezione viene creato un utente di nome B.Simon in Profit.co. Collaborare con il [team di supporto di Profit.co](mailto:support@profit.co) per aggiungere gli utenti alla piattaforma Profit.co. Prima di poter usare l'accesso Single Sign-On, è necessario creare e attivare gli utenti.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Profit.co nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Profit.co per cui si è configurato l'accesso SSO. Per altre informazioni, vedere l'[introduzione al pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Esercitazioni per l'integrazione di applicazioni SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare Profit.co con Azure AD](https://aad.portal.azure.com/)

- [Informazioni sul controllo sessioni in Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Come proteggere Profit.co con visibilità e controlli avanzati](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)