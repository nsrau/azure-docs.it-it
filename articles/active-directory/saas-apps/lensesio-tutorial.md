---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Lenses.io | Microsoft Docs"
description: Questa esercitazione descrive come configurare l'accesso Single Sign-On tra Azure Active Directory e Lenses.io.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/02/2020
ms.author: jeedes
ms.openlocfilehash: 181d58baf128c4848a538e776aea0e43213994dc
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92458609"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-the-lensesio-dataops-portal"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory il portale DataOps di Lenses.io

Questa esercitazione descrive come integrare il portale DataOps di [Lenses.io](https://lenses.io/) con Azure Active Directory (Azure AD). Dopo aver integrato Lenses.io con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere al portale di Lenses.io.
* Abilitare gli utenti per l'accesso automatico a Lenses con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS (Software as a Service) con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con AD](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Un'istanza di un portale di Lenses. È possibile scegliere tra diverse [opzioni di distribuzione](https://lenses.io/product/deployment/).
* Una [licenza](https://lenses.io/product/pricing/) Lenses.io che supporti l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Lenses.io supporta l'accesso SSO avviato dal provider di servizio (SP).

* Dopo aver configurato Lenses.io, è possibile applicare il controllo sessione, che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-lensesio-from-the-gallery"></a>Aggiungere Lenses.io dalla raccolta

Per configurare l'integrazione di Lenses.io in Azure AD, aggiungere Lenses.io all'elenco di app SaaS gestite:

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro sinistro selezionare il servizio **Azure Active Directory** .
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni** .
1. Selezionare **Nuova applicazione** .
1. Nella sezione **Aggiungi dalla raccolta** immettere **Lenses.io** nella casella di ricerca.
1. Selezionare **Lenses.io** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-lensesio"></a>Configurare e testare l'accesso SSO di Azure AD per Lenses.io

Verrà creato un utente di test di nome *B.Simon* per configurare e testare l'accesso SSO di Azure AD SSO con il portale di Lenses.io. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Lenses.io.

Completare i passaggi seguenti:

1. [Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso): per consentire agli utenti di usare questa funzionalità.
    1. [Creare un utente e un gruppo di test di Azure AD](#create-an-azure-ad-test-user-and-group) per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. [Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user) per consentire a B.Simon di usare l'accesso Single Sign-On di Azure AD.
1. [Configurare l'accesso Single Sign-On di Lenses.io](#configure-lensesio-sso) per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. [Creare autorizzazioni del gruppo di test di Lenses.io](#create-lensesio-test-group-permissions) per controllare le risorse a cui può avere accesso B.Simon in Lenses.io (autorizzazione).
1. [Testare l'accesso Single Sign-On](#test-sso): per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Lenses.io** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On** .
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML** .
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona di modifica a forma di penna relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Screenshot che mostra l'icona per la modifica della configurazione SAML di base.](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori nelle caselle seguenti per l'immissione di testo:

    a. **URL di accesso** : immettere un URL nel formato seguente: `https://<CUSTOMER_LENSES_BASE_URL>`. Un esempio è `https://lenses.my.company.com`.

    b. **Identificatore (ID entità)** : immettere un URL nel formato seguente: `https://<CUSTOMER_LENSES_BASE_URL>`. Un esempio è `https://lenses.my.company.com`.

    c. **Reply URL** (URL di risposta): immettere un URL nel formato seguente: `https://<CUSTOMER_LENSES_BASE_URL>/api/v2/auth/saml/callback?client_name=SAML2Client`. Un esempio è `https://lenses.my.company.com/api/v2/auth/saml/callback?client_name=SAML2Client`.

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'URL di accesso, l'URL di risposta e l'identificatore effettivi dell'URL di base dell'istanza del portale di Lenses. Per altre informazioni, vedere la [documentazione di Lenses.io relativa all'accesso SSO](https://docs.lenses.io/install_setup/configuration/security.html#single-sign-on-sso-saml-2-0).

1. Nella pagina **Configura l'accesso Single Sign-On con SAML** passare alla sezione **Certificato di firma SAML** . Individuare **XML metadati federazione** e quindi selezionare **Scarica** per scaricare e salvare il certificato nel computer.

    ![Screenshot che mostra il collegamento di download del certificato.](common/metadataxml.png)

1. Nella sezione **Configura Lenses.io** usare il file XML scaricato per configurare Lenses per l'accesso SSO di Azure.

### <a name="create-an-azure-ad-test-user-and-group"></a>Creare un utente e un gruppo di test di Azure AD

Nel portale di Azure si creerà un utente di test di nome B.Simon e quindi un gruppo di test che controlla l'accesso di B.Simon in Lenses.

Per informazioni su come Lenses usa il mapping delle appartenenze ai gruppi per l'autorizzazione, vedere la [documentazione di Lenses relativa all'accesso SSO](https://docs.lenses.io/install_setup/configuration/security.html#id3).

**Per creare l'utente di test:**

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory** , **Utenti** e quindi **Tutti gli utenti** .
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nella casella **Nome** immettere **B.Simon** .  
   1. Nella casella **Nome utente** immettere username@companydomain.extension. Ad esempio: B.Simon@contoso.com.
   1. Selezionare la casella di controllo **Mostra password** . Prendere nota della password visualizzata nella casella **Password** .
   1. Selezionare **Crea** .

**Per creare il gruppo:**

1. Passare ad **Azure Active Directory** e quindi selezionare **Gruppi** .
1. Selezionare **Nuovo gruppo** in alto nella schermata.
1. In **Proprietà gruppo** seguire questa procedura:
   1. Selezionare **Sicurezza** nella casella **Tipo di gruppo** .
   1. Nella casella **Nome gruppo** immettere **LensesUsers** .
   1. Selezionare **Crea** .
1. Selezionare il gruppo **LensesUsers** e copiare il valore di **ID oggetto** , ad esempio f8b5c1ec-45de-4abd-af5c-e874091fb5f7. Questo ID verrà usato in Lenses per eseguire il mapping tra gli utenti di tale gruppo e le [autorizzazioni corrette](https://docs.lenses.io/install_setup/configuration/security.html#id3).  

**Per assegnare il gruppo all'utente di test:**

1. Passare ad **Azure Active Directory** e quindi selezionare **Utenti** .
1. Selezionare l'utente di test **B.Simon** .
1. Selezionare **Gruppi** .
1. Selezionare **Aggiungi appartenenze** in alto nella schermata.
1. Cercare e selezionare **LensesUsers** .
1. Fare clic su **Seleziona** .

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Lenses.io.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni** .
1. Nell'elenco delle applicazioni selezionare **Lenses.io** .
1. Nella sezione **Gestione** della pagina di panoramica dell'app selezionare **Utenti e gruppi** .

   ![Screenshot che mostra il collegamento "Utenti e gruppi".](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** .

   ![Screenshot che mostra il collegamento Aggiungi utente.](common/add-assign-user.png)

1. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Utenti e gruppi** .
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** nell'elenco Utenti. Fare quindi clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** scegliere il ruolo appropriato per l'utente dall'elenco. Fare quindi clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** selezionare il pulsante **Assegna** .

## <a name="configure-lensesio-sso"></a>Configurare l'accesso Single Sign-On di Lenses.io

Per configurare l'accesso Single Sign-On nel portale di **Lenses.io** , installare nell'istanza di Lenses il file **XML dei metadati di federazione** scaricato e [configurare Lenses per consentire l'accesso SSO](https://docs.lenses.io/install_setup/configuration/security.html#configure-lenses).

### <a name="create-lensesio-test-group-permissions"></a>Creare autorizzazioni del gruppo di test di Lenses.io

1. Per creare un gruppo in Lenses, usare il valore di **ID oggetto** del gruppo **LensesUsers** . Si tratta dell'ID copiato nella [sezione di creazione](#create-an-azure-ad-test-user-and-group) dell'utente.
1. Assegnare le autorizzazioni desiderate per B. Simon.

Per altre informazioni, vedere la pagina relativa al [mapping dei gruppi Lenses per Azure](https://docs.lenses.io/install_setup/configuration/security.html#azure-groups).

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso SSO di Azure AD usando il pannello di accesso.

Quando si seleziona il riquadro di Lenses.io nel pannello di accesso, si dovrebbe accedere automaticamente al portale di Lenses.io. Per altre informazioni, vedere [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Configurare l'accesso SSO nell'istanza di Lenses.io](https://docs.lenses.io/install_setup/configuration/security.html#single-sign-on-sso-saml-2-0)

- [Elenco di esercitazioni sull'integrazione delle app SaaS con Azure AD](./tutorial-list.md)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure AD](../manage-apps/what-is-single-sign-on.md)

- [Informazioni sull'accesso condizionale in Azure AD](../conditional-access/overview.md)

- [Provare Lenses.io con Azure AD](https://aad.portal.azure.com/)

- [Informazioni sul controllo sessioni in Microsoft Cloud App Security](/cloud-app-security/proxy-intro-aad)

- [Come proteggere Lenses.io con visibilità e controlli avanzati](/cloud-app-security/proxy-intro-aad)