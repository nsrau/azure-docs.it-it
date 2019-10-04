---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Harness | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Harness.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 82367f62-173e-4e14-bf84-d8f611706086
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 709bf2e413ddb40ae1ed84672cea0c0b71be2ce6
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70305935"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-harness"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Harness

Questa esercitazione descrive come integrare Harness con Azure Active Directory (Azure AD). Integrando Harness con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Harness.
* Abilitare gli utenti per l'accesso automatico a Harness con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Harness abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Harness supporta l'accesso SSO avviato da **SP e IDP**

## <a name="adding-harness-from-the-gallery"></a>Aggiunta di Harness dalla raccolta

Per configurare l'integrazione di Harness in Azure AD, è necessario aggiungere Harness dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Harness** nella casella di ricerca.
1. Selezionare **Harness** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-harness"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Harness

Configurare e testare l'accesso SSO di Azure AD con Harness usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Harness.

Per configurare e testare l'accesso SSO di Azure AD con Harness, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Harness](#configure-harness-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Harness](#create-harness-test-user)** : per avere una controparte di B.Simon in Harness collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Harness** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://app.harness.io/gateway/api/users/saml-login?accountId=<harness_account_id>`

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL di accesso** digitare un URL: `https://app.harness.io/`

    > [!NOTE]
    > Il valore di URL di risposta non è reale. Si otterrà l'URL di risposta effettivo dalla sezione **Configurare l'accesso Single Sign-On di Harness**, illustrata più avanti nell'esercitazione. È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il file **XML dei metadati della federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura Harness** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Harness.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Harness**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-harness-sso"></a>Configurare l'accesso Single Sign-On di Harness

1. Per automatizzare la configurazione all'interno di Harness, è necessario installare l'**estensione del browser per l'accesso sicuro ad App personali** facendo clic su **Installa l'estensione**.

    ![Estensione per App personali](common/install-myappssecure-extension.png)

2. Dopo aver aggiunto l'estensione al browser, fare clic su **Configura Harness** per passare direttamente all'applicazione Harness. Nell'applicazione fornire le credenziali di amministratore per accedere ad Harness. L'estensione del browser configurerà automaticamente l'applicazione e automatizzerà i passaggi da 3 a 6.

    ![Eseguire la configurazione](common/setup-sso.png)

3. Se si vuole configurare manualmente Harness, aprire una nuova finestra del Web browser, accedere al sito aziendale di Harness come amministratore e seguire questa procedura:

4. In alto a destra della pagina fare clic su  **Continuous Security** (Sicurezza continua) > **Access Management** (Gestione accesso)  > **Authentication Settings** (Impostazioni di autenticazione).

    ![Configurazione di Harness](./media/harness-tutorial/configure01.png)

5. Nella sezione **SSO Providers** (Provider SSO) fare clic su  **+ Add SSO Providers** (+ Aggiungi provider SSO) > **SAML**.

    ![Configurazione di Harness](./media/harness-tutorial/configure03.png)

6. Nel popup **SAML Provider** (Provider SAML) seguire questa procedura:

    ![Configurazione di Harness](./media/harness-tutorial/configure02.png)

    a. Copiare l'istanza **In your SSO Provider, please enable SAML-based login, then enter the following URL** (Nel provider SSO abilitare l'accesso basato su SAML, quindi inserire l'URL seguente) e incollarla nella casella di testo URL di risposta nella sezione **Configurazione SAML di base** del portale di Azure.

    b. Nella casella di testo **Display Name** (Nome visualizzato) digitare il nome visualizzato.

    c. Fare clic su **Choose File** (Scegli file) per caricare il file XML dei metadati della federazione scaricato dal portale di Azure.

    d. Fare clic su **SUBMIT** (INVIA).

### <a name="create-harness-test-user"></a>Creare l'utente di test di Harness

Per consentire agli utenti di Azure AD di accedere a Harness, è necessario effettuarne il provisioning in Harness. Nel caso di Harness il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere a Harness come amministratore.

1. In alto a destra della pagina fare clic su  **Continuous Security** (Sicurezza continua) > **Access Management** (Gestione accesso)  > **Users** (Utenti).

    ![Configurazione di Harness](./media/harness-tutorial/configure04.png)

1. Sul lato destro della pagina fare clic su  **+ Add User** (+ Aggiungi utente).

    ![Configurazione di Harness](./media/harness-tutorial/configure05.png)

1. Nel popup **Add user** (Aggiungi utente) seguire questa procedura:

    ![Configurazione di Harness](./media/harness-tutorial/configure06.png)

    a. Nella casella di testo **Email address(es)** (Indirizzo di posta elettronica) immettere l'indirizzo di posta elettronica dell'utente, ad esempio `B.simon@contoso.com`.

    b. Selezionare i **gruppi utente**.

    c. Fare clic su **Submit** (Invia).

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Harness nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Harness per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare Harness con Azure AD](https://aad.portal.azure.com/)

