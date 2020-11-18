---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Software AG Cloud | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Software AG Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/23/2020
ms.author: jeedes
ms.openlocfilehash: 852fabfd1fe496064e6d13c21ba052ee6f96b79e
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93242981"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-software-ag-cloud"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Software AG Cloud

Questa esercitazione descrive come integrare Software AG Cloud con Azure Active Directory (Azure AD). Integrando Software AG Cloud con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Software AG Cloud.
* Abilitare gli utenti per l'accesso automatico a Software AG Cloud con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Software AG Cloud abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Software AG Cloud supporta l'accesso SSO avviato da **SP**
* Software AG Cloud supporta il provisioning utenti **JIT**

## <a name="adding-software-ag-cloud-from-the-gallery"></a>Aggiunta di Software AG Cloud dalla raccolta

Per configurare l'integrazione di Software AG Cloud in Azure AD, è necessario aggiungere Software AG Cloud dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Software AG Cloud** nella casella di ricerca.
1. Selezionare **Software AG Cloud** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-sso-for-software-ag-cloud"></a>Configurare e testare l'accesso SSO di Azure AD per Software AG Cloud

Configurare e testare l'accesso SSO di Azure AD con Software AG Cloud usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Software AG Cloud.

Per configurare e testare l'accesso SSO di Azure AD con Software AG Cloud, completare la procedura seguente:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Software AG Cloud](#configure-software-ag-cloud-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Software AG Cloud](#create-software-ag-cloud-test-user)** : per avere una controparte di B.Simon in Software AG Cloud collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Software AG Cloud** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<SUBDOMAIN>.softwareag.cloud/auth/realms/TENANT-NAME/broker/IDENTITY-PROVIDER-NAME/endpoint`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `https://<SUBDOMAIN>.softwareag.cloud/auth/realms/TENANT-NAME`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Software AG Cloud](mailto:support@softwareag.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il file **XML dei metadati della federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura Software AG Cloud** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Software AG Cloud.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Software AG Cloud**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-software-ag-cloud-sso"></a>Configurare l'accesso Single Sign-On di Software AG Cloud

1. In un'altra finestra del Web browser accedere al sito Web di Software AG Cloud come amministratore.

1.  Fare clic su **Administration** (Amministrazione)

    ![Configurazione di Software AG Cloud - 1](./media/software-ag-cloud-tutorial/admin.png)

1. Passare a **Single-sign on > Add identity provider** (Accesso Single Sign-On > Aggiungi provider di identità)

    ![Configurazione di Software AG Cloud - 2](./media/software-ag-cloud-tutorial/add-identity-provider.png)

1. Nella pagina successiva seguire questa procedura.

    ![Configurazione di Software AG Cloud - 3](./media/software-ag-cloud-tutorial/saml-1.png)

    a. Nella casella di testo **Identity provider display name** (Nome visualizzato del provider di identità) specificare un nome valido, ad esempio `azure ad`.

    b. Nella casella di testo **Identity provider unique identifier for use in Software AG Cloud redirect URI** (Identificatore univoco del provider di identità da usare nell'URL di reindirizzamento di Software AG Cloud) incollare il valore di **ID entità** copiato dal portale di Azure.

    c. Importare il file **XML dei metadati di federazione** in **Identity provider configuration** (Configurazione del provider di identità) e fare clic su **Next** (Avanti).

    d. Passare alla pagina **Conﬁguration** (Configurazione) e completare i campi come richiesto.

### <a name="create-software-ag-cloud-test-user"></a>Creare l'utente di test di Software AG Cloud

In questa sezione viene creato un utente di nome Britta Simon in Software AG Cloud. Software AG Cloud supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in Software AG Cloud, ne viene creato uno nuovo dopo l'autenticazione.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

1. Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di Software AG Cloud, in cui è possibile avviare il flusso di accesso. 

2. Passare direttamente all'URL di accesso di Software AG Cloud e avviare il flusso di accesso da questa posizione.

3. È possibile usare il Pannello di accesso Microsoft. Quando si fa clic sul riquadro di Software AG Cloud nel pannello di accesso, si verrà reindirizzati all'URL di accesso di Software AG Cloud. Per altre informazioni in proposito, vedere l'[introduzione al pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Software AG Cloud, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


