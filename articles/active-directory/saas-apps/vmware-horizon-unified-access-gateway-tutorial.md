---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con VMware Horizon - Unified Access Gateway | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e VMware Horizon - Unified Access Gateway.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/02/2020
ms.author: jeedes
ms.openlocfilehash: 9e9d98e8b68b4e01d7e8f19e7c7d89a88d9bf69f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89500572"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-vmware-horizon---unified-access-gateway"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con VMware Horizon - Unified Access Gateway

Questa esercitazione descrive come integrare VMware Horizon - Unified Access Gateway con Azure Active Directory (Azure AD). Integrando VMware Horizon - Unified Access Gateway con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a VMware Horizon - Unified Access Gateway.
* Abilitare gli utenti per l'accesso automatico a VMware Horizon - Unified Access Gateway con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di VMware Horizon - Unified Access Gateway abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* VMware Horizon - Unified Access Gateway supporta l'accesso SSO avviato da **SP e IDP**

## <a name="adding-vmware-horizon---unified-access-gateway-from-the-gallery"></a>Aggiunta di VMware Horizon - Unified Access Gateway dalla raccolta

Per configurare l'integrazione di VMware Horizon - Unified Access Gateway in Azure AD, è necessario aggiungere VMware Horizon - Unified Access Gateway dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **VMware Horizon - Unified Access Gateway** nella casella di ricerca.
1. Selezionare **VMware Horizon - Unified Access Gateway** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-sso-for-vmware-horizon---unified-access-gateway"></a>Configurare e testare l'accesso SSO di Azure AD per VMware Horizon - Unified Access Gateway

Configurare e testare l'accesso SSO di Azure AD con VMware Horizon - Unified Access Gateway usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in VMware Horizon - Unified Access Gateway.

Per configurare e testare l'accesso SSO di Azure AD con VMware Horizon - Unified Access Gateway, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di VMware Horizon - Unified Access Gateway](#configure-vmware-horizon-unified-access-gateway-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare un utente di test di VMware Horizon - Unified Access Gateway](#create-vmware-horizon-unified-access-gateway-test-user)** : per avere una controparte di B.Simon in VMware Horizon-Unified Access Gateway collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **VMware Horizon - Unified Access Gateway** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<HORIZON_UAG_FQDN>/portal`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<HORIZON_UAG_FQDN>/portal/samlsso`

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<HORIZON_UAG_FQDN>`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di VMware Horizon - Unified Access Gateway Client](mailto:support@vmware.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il file **XML dei metadati della federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura VMware Horizon - Unified Access Gateway** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a VMware Horizon - Unified Access Gateway.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **VMware Horizon - Unified Access Gateway**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se non è stato configurato alcun ruolo per questa app, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-vmware-horizon-unified-access-gateway-sso"></a>Configurare l'accesso Single Sign-On di VMware Horizon - Unified Access Gateway

Per configurare l'accesso Single Sign-On sul lato **VMware Horizon - Unified Access Gateway**, è necessario inviare il file **XML dei metadati della federazione** scaricato e gli URL appropriati, copiati dal portale di Azure, al [team di supporto di VMware Horizon - Unified Access Gateway](mailto:support@vmware.com). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-vmware-horizon-unified-access-gateway-test-user"></a>Creare l'utente di test di VMware Horizon - Unified Access Gateway

In questa sezione viene creato un utente di test di nome B.Simon in VMware Horizon - Unified Access Gateway. Collaborare con il  [team di supporto di VMware Horizon - Unified Access Gateway](mailto:support@vmware.com) per aggiungere gli utenti alla piattaforma VMware Horizon - Unified Access Gateway. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

#### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di VMware Horizon - Unified Access Gateway, in cui è possibile avviare il flusso di accesso.  

* Passare direttamente all'URL di accesso di VMware Horizon - Unified Access Gateway e avviare il flusso di accesso da questa posizione.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Dopo aver fatto clic su **Test this application** (Testa questa applicazione) nel portale di Azure, si dovrebbe accedere automaticamente all'istanza di VMware Horizon - Unified Access Gateway per cui si è configurato l'accesso SSO 

È anche possibile usare il pannello di accesso Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro di VMware Horizon - Unified Access Gateway nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di VMware Horizon - Unified Access Gateway per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato VMware Horizon - Unified Access Gateway, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
