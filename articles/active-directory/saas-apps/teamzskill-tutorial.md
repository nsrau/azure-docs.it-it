---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con TeamzSkill | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e TeamzSkill.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/17/2020
ms.author: jeedes
ms.openlocfilehash: 4e7d8c4917f7860ba07ef9eb749337afd83f723a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91255554"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-teamzskill"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con TeamzSkill

Questa esercitazione descrive come integrare TeamzSkill con Azure Active Directory (Azure AD). Integrando TeamzSkill con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a TeamzSkill.
* Abilitare gli utenti per l'accesso automatico a TeamzSkill con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di TeamzSkill abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* TeamzSkill supporta l'accesso SSO avviato da **SP e IDP**
* TeamzSkill supporta il provisioning utenti **JIT**

## <a name="adding-teamzskill-from-the-gallery"></a>Aggiunta di TeamzSkill dalla raccolta

Per configurare l'integrazione di TeamzSkill in Azure AD, è necessario aggiungere TeamzSkill dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **TeamzSkill** nella casella di ricerca.
1. Selezionare **TeamzSkill** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-sso-for-teamzskill"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per TeamzSkill

Configurare e testare l'accesso SSO di Azure AD con TeamzSkill usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in TeamzSkill.

Per configurare e testare l'accesso SSO di Azure AD con TeamzSkill, completare i passaggi seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di TeamzSkill](#configure-teamzskill-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di TeamzSkill](#create-teamzskill-test-user)** : per avere una controparte di B.Simon in TeamzSkill collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **TeamzSkill** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<CUSTOMER_SUBDOMAIN>.teamzskill.com/login/callback`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<CUSTOMER_SUBDOMAIN>.teamzskill.com/login/callback`

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<CUSTOMER_SUBDOMAIN>.teamzskill.com/login/callback`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di TeamzSkill](mailto:support@teamzskill.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. L'applicazione TeamzSkill prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/default-attributes.png)

1. Oltre quelli elencati in precedenza, l'applicazione TeamzSkill prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.
    
    | Nome |  Attributo di origine|
    | ------------ | --------- |
    | Firstname | user.givenname |
    | Lastname | user.surname |
    | jobtitle | user. jobtitle |
    | department | user.department |
    | employeeid | user.employeeid |
    | postalcode | user.postalcode |
    | country | user.country |
    | ruolo | user.assignedroles |

    > [!NOTE]
    > TeamzSkill prevede ruoli per gli utenti assegnati all'applicazione. Configurare questi ruoli in Azure AD in modo che agli utenti possano essere assegnati i ruoli appropriati. Per informazioni su come configurare i ruoli in Azure AD, vedere [qui](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management).

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il file **XML dei metadati della federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura TeamzSkill** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a TeamzSkill.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **TeamzSkill**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-teamzskill-sso"></a>Configurare l'accesso Single Sign-On di TeamzSkill

1. In un'altra finestra del Web browser accedere a TeamzSkill come amministratore.

1. Fare clic sull'icona del profilo e quindi selezionare **Company settings** (Impostazioni aziendali).

    ![Impostazioni aziendali in Teamzskill](./media/teamzskill-tutorial/settings.png)

1. Nella pagina **Settings** seguire questa procedura.

    ![Impostazioni in Teamzskill](./media/teamzskill-tutorial/metadata.png)

    a. Passare a **Company > Single Sign-On** (Società > Accesso Single Sign-On) e quindi selezionare la scheda **Metadata Upload** (Caricamento metadati).

    b. Incollare il valore di **XML metadati federazione**, copiato dal portale di Azure, nel campo **XML Metadata** (Metadati XML).
     
    c. Fare quindi clic su **Salva**.

### <a name="create-teamzskill-test-user"></a>Creare l'utente di test di TeamzSkill

In questa sezione viene creato un utente di nome B.Simon in TeamzSkill. TeamzSkill supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in TeamzSkill, ne viene creato uno nuovo quando si prova ad accedere a TeamzSkill.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

#### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di TeamzSkill, in cui è possibile avviare il flusso di accesso.  

* Passare direttamente all'URL di accesso di TeamzSkill e avviare il flusso di accesso da questa posizione.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Dopo aver fatto clic su **Test this application** (Testa questa applicazione) nel portale di Azure, si dovrebbe accedere automaticamente all'istanza di TeamzSkill per cui si è configurato l'accesso SSO 

È anche possibile usare il pannello di accesso Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro di TeamzSkill nel pannello di accesso, se è stato configurato in modalità SP, si dovrebbe essere reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso; se invece è configurato in modalità IDP, si dovrebbe accedere automaticamente all'istanza di TeamzSkill per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato TeamzSkill, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

