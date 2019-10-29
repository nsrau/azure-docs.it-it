---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Elium | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory ed Elium.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: fae344b3-5bd9-40e2-9a1d-448dcd58155f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0900f730c287586725722f0b8baaeb0c22f850c2
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791234"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-elium"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Elium

Questa esercitazione descrive come integrare Elium con Azure Active Directory (Azure AD). Integrando Elium con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Elium.
* Abilitare gli utenti per l'accesso automatico a Elium con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Elium abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Elium supporta l'accesso SSO avviato da **SP e IDP**
* Elium supporta il provisioning utenti **JIT**

## <a name="adding-elium-from-the-gallery"></a>Aggiunta di Elium dalla raccolta

Per configurare l'integrazione di Elium in Azure AD, è necessario aggiungere Elium dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Elium** nella casella di ricerca.
1. Selezionare **Elium** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-elium"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Elium

Configurare e testare l'accesso SSO di Azure AD con Elium usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Elium.

Per configurare e testare l'accesso SSO di Azure AD con Elium, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    * **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    * **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Elium](#configure-elium-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    * **[Creare l'utente di test di Elium](#create-elium-test-user)** : per avere una controparte di B.Simon in Elium collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Elium** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<platform-domain>.elium.com/login/saml2/metadata`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<platform-domain>.elium.com/login/saml2/acs`

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<platform-domain>.elium.com/login/saml2/login`

    > [!NOTE]
    > Poiché questi non sono i valori reali, È possibile ottenere questi valori dal **file di metadati SP** che è possibile scaricare all'indirizzo `https://<platform-domain>.elium.com/login/saml2/metadata` e che viene illustrato più avanti in questa esercitazione.

1. L'applicazione Elium prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/default-attributes.png)

1. Oltre quelli elencati in precedenza, l'applicazione Elium prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.

    | Nome | Attributo di origine|
    | ---------------| ----------------|
    | email   |user.mail |
    | first_name| user.givenname |
    | last_name| user.surname|
    | job_title| user. jobtitle|
    | company| user.companyname|

    > [!NOTE]
    > Queste sono le attestazioni predefinite. **Solo l'attestazione email è obbligatoria**. Anche per il provisioning JIT è obbligatoria solo l'attestazione email. Altre attestazioni personalizzate possono variare in base alla piattaforma del cliente.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il file **XML dei metadati della federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura Elium** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Elium.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Elium**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-elium-sso"></a>Configurare l'accesso Single Sign-On di Elium

1. Per automatizzare la configurazione all'interno di Elium, è necessario installare l'**estensione del browser per l'accesso sicuro ad App personali** facendo clic su **Installa l'estensione**.

    ![Estensione per App personali](common/install-myappssecure-extension.png)

1. Dopo aver aggiunto l'estensione al browser, fare clic su **Configura Elium** per passare direttamente all'applicazione Elium. Nell'applicazione specificare le credenziali di amministratore per accedere a Elium. L'estensione del browser configurerà automaticamente l'applicazione e automatizzerà i passaggi da 3 a 6.

    ![Eseguire la configurazione](common/setup-sso.png)

1. Se si vuole configurare manualmente Elium, aprire una nuova finestra del Web browser, accedere al sito aziendale di Elium come amministratore e seguire questa procedura:

1. Fare clic **sul profilo**utente nell'angolo in alto a destra e quindi selezionare **Administration** (Amministrazione).

    ![Configure Single Sign-On](./media/elium-tutorial/user1.png)

1. Selezionare la scheda **Security** (Sicurezza).

    ![Configure Single Sign-On](./media/elium-tutorial/user2.png)

1. Scorrere fino alla sezione **Single sign-on (SSO)** e seguire questa procedura:

    ![Configure Single Sign-On](./media/elium-tutorial/user3.png)

    a. Copiare il valore di **Verify that SAML2 authentication works for your account** (Verificare il funzionamento dell'autenticazione SAML2 per l'account) e incollarlo nella casella di testo **URL accesso** nella sezione **Configurazione SAML di base** del portale di Azure.

    > [!NOTE]
    > Dopo avere configurato SSO, è sempre possibile accedere alla pagina di accesso remoto all'URL seguente: `https://<platform_domain>/login/regular/login` 

    b. Selezionare la casella di controllo **Enable SAML2 federation** (Abilita federazione SAML2).

    c. Selezionare la casella di controllo **JIT Provisioning** (Provisioning JIT).

    d. Aprire **SP Metadata** (Metadati SP) facendo clic su **Download**.

    e. Cercare **entityID** nel file **SP Metadata** (Metadati SP), copiare il valore di **entityID** e incollarlo nella casella di testo **Identificatore** nella sezione **Configurazione SAML di base** del portale di Azure. 

    ![Configure Single Sign-On](./media/elium-tutorial/user4.png)

    f. Cercare **AssertionConsumerService** nel file **SP Metadata** (Metadati SP), copiare il valore di **Location** e incollarlo nella casella di testo **URL di risposta** nella sezione **Configurazione SAML di base** del portale di Azure.

    ![Configure Single Sign-On](./media/elium-tutorial/user5.png)

    g. Copiare il contenuto del file dei metadati scaricato dal portale di Azure nel Blocco note e incollarlo nella casella di testo **IdP Metadata** (Metadati IdP).

    h. Fare clic su **Save**.

### <a name="create-elium-test-user"></a>Creare l'utente di test di Elium

In questa sezione viene creato un utente di nome B.Simon in Elium. Elium supporta il **provisioning JIT**, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in Elium, ne viene creato uno nuovo quando si prova ad accedere a Elium.

> [!Note]
> Se è necessario creare un utente manualmente, contattare il [team di supporto di Elium](mailto:support@elium.com).

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Elium nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Elium per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare Elium con Azure AD](https://aad.portal.azure.com/)