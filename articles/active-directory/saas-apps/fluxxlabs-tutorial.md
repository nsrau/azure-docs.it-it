---
title: 'Esercitazione: Integrazione di Azure Active Directory con Fluxx Labs | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Fluxx Labs.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/21/2020
ms.author: jeedes
ms.openlocfilehash: 756bbaab1cbf359def01e371e4370607bee67ce3
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554862"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fluxx-labs"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Fluxx Labs

Questa esercitazione descrive come integrare Fluxx Labs con Azure Active Directory (Azure AD). Integrando Fluxx Labs con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Fluxx Labs.
* Abilitare gli utenti per l'accesso automatico a Fluxx Labs con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Fluxx Labs abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Fluxx Labs supporta l'accesso SSO avviato da **IDP**
* Dopo aver configurato Fluxx Labs, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-fluxx-labs-from-the-gallery"></a>Aggiunta di Fluxx Labs dalla raccolta

Per configurare l'integrazione di Fluxx Labs in Azure AD, è necessario aggiungere Fluxx Labs dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Fluxx Labs** nella casella di ricerca.
1. Selezionare **Fluxx Labs** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-fluxx-labs"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Fluxx Labs

Configurare e testare l'accesso SSO di Azure AD con Fluxx Labs usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Fluxx Labs.

Per configurare e testare l'accesso SSO di Azure AD con Fluxx Labs, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Fluxx Labs](#configure-fluxx-labs-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Fluxx Labs](#create-fluxx-labs-test-user)** : per avere una controparte di B.Simon in Fluxx Labs collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Fluxx Labs** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella pagina **Configura l'accesso Single Sign-On con SAML** eseguire questa procedura:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente:

    | Environment | Modello URL|
    |-------------|------------|
    | Produzione | `https://<subdomain>.fluxx.io` |
    | Preproduzione | `https://<subdomain>.preprod.fluxxlabs.com`|

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente:

    | Environment | Modello URL|
    |-------------|------------|
    | Produzione | `https://<subdomain>.fluxx.io/auth/saml/callback` |
    | Preproduzione | `https://<subdomain>.preprod.fluxxlabs.com/auth/saml/callback`|

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di risposta effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Fluxx Labs](https://fluxx.zendesk.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura Fluxx Labs** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Fluxx Labs.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Fluxx Labs**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-fluxx-labs-sso"></a>Configurare l'accesso Single Sign-On di Fluxx Labs

1. In un'altra finestra del Web browser accedere al sito aziendale di Fluxx Labs come amministratore.

2. Selezionare **Amministrazione** sotto la sezione **Impostazioni**.

    ![Configurazione di Fluxx Labs](./media/fluxxlabs-tutorial/config1.png)

3. Nel pannello Amministrazione selezionare **Plug-in** > **Integrazioni** e quindi selezionare **SAML SSO-(Disabilitato)**

    ![Configurazione di Fluxx Labs](./media/fluxxlabs-tutorial/config2.png)

4. Nella sezione attribute seguire questa procedura:

    ![Configurazione di Fluxx Labs](./media/fluxxlabs-tutorial/config3.png)

    a. Selezionare la casella di controllo **SAML SSO** (SSO SAML).

    b. Nella casella di testo **Request Path** (Percorso richiesta) digitare **/auth/saml**.

    c. Nella casella di testo **Callback Path** (Percorso callback) digitare **/auth/saml/callback**.

    d. Nella casella di testo **Assertion Consumer Service URL (Single Sign-On URL)** (URL del servizio consumer di asserzione - URL Single Sign-On) immettere il valore di **URL di risposta** immesso nel portale di Azure.

    e. Nella casella di testo **Audience (SP Entity ID)** (Destinatari - ID entità provider di servizi) immettere il valore di **Identificatore** immesso nel portale di Azure.

    f. Nella casella di testo **Identity Provider SSO Target URL** (URL di destinazione SSO del provider di identità) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    g. Aprire il certificato con codifica Base 64 nel Blocco note, copiarne il contenuto negli Appunti e quindi incollarlo nella casella di testo **Certificato provider di identità**.

    h. Nella casella di testo **Formato identificatore nome** immettere il valore `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.

    i. Fare clic su **Salva**.

    > [!NOTE]
    > Una volta salvato il contenuto, il campo appare vuoto per motivi di sicurezza, ma il valore è stato salvato nella configurazione.

### <a name="create-fluxx-labs-test-user"></a>Creare l'utente di test di Fluxx Labs

Per consentire agli utenti di Azure AD di accedere a Fluxx Labs, è necessario effettuarne il provisioning in Fluxx Labs. Nel caso di Fluxx Labs il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di Fluxx Labs come amministratore.

2. Fare clic sull'**icona** visualizzata di seguito.

    ![Configurazione di Fluxx Labs](./media/fluxxlabs-tutorial/config6.png)

3. Nel dashboard fare clic sull'icona visualizzata di seguito per aprire la scheda **New PEOPLE** (Nuove PERSONE).

    ![Configurazione di Fluxx Labs](./media/fluxxlabs-tutorial/config4.png)

4. Nella sezione **NEW PEOPLE** (Nuove persone) seguire questa procedura:

    ![Configurazione di Fluxx Labs](./media/fluxxlabs-tutorial/config5.png)

    a. Fluxx Labs usa l'account di posta elettronica come identificatore univoco per l'accesso Single Sign-On. Compilare il campo **SSO UID** (UID SSO) con l'indirizzo di posta elettronica dell'utente, corrispondente all'indirizzo di posta elettronica usato come account di accesso con SSO.

    b. Fare clic su **Salva**.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Fluxx Labs nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Fluxx Labs per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare Fluxx Labs con Azure AD](https://aad.portal.azure.com/)

- [Informazioni sul controllo sessioni in Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Come proteggere Fluxx Labs con visibilità e controlli avanzati](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)