---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Roadmunk"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Roadmunk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/28/2020
ms.author: jeedes
ms.openlocfilehash: 26424106098a8385faff5ab6d3de33d98576ef4e
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2020
ms.locfileid: "94381304"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-roadmunk"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Roadmunk

Questa esercitazione descrive come integrare Roadmunk con Azure Active Directory (Azure AD). Integrando Roadmunk con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Roadmunk.
* Abilitare gli utenti per l'accesso automatico a Roadmunk con gli account Azure AD personali.
* Gestire gli account in una posizione centrale, il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Roadmunk abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

Roadmunk supporta l'accesso SSO avviato dal *provider di servizi* (SP) e dal *provider di identità* (IDP).

## <a name="add-roadmunk-from-the-gallery"></a>Aggiungere Roadmunk dalla raccolta

Per integrare Roadmunk in Azure AD, aggiungere Roadmunk dalla raccolta all'elenco di app SaaS gestite:

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro sinistro selezionare **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Roadmunk** nella casella di ricerca.
1. Selezionare **Roadmunk** nei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-sso-for-roadmunk"></a>Configurare e testare l'accesso SSO di Azure AD per Roadmunk

Configurare e testare l'accesso SSO di Azure AD con Roadmunk by usando un utente di test di nome *B.Simon*. Per il corretto funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Roadmunk.

Per configurare e testare l'accesso SSO di Azure AD con Roadmunk, seguire questa procedura:

1. [Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso): per consentire agli utenti di usare questa funzionalità.
    1. [Creare un utente di test di Azure AD](#create-an-azure-ad-test-user) per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. [Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user) per consentire a B.Simon di usare l'accesso Single Sign-On di Azure AD.
1. [Configurare l'accesso Single Sign-On di Roadmunk](#configure-roadmunk-sso): per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. [Creare un utente di test di Roadmunk](#create-roadmunk-test-user) in modo da collegare la controparte di B.Simon in Roadmunk alla rappresentazione dell'utente in Azure AD.
1. [Testare l'accesso SSO](#test-sso) per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Roadmunk** del portale di Azure individuare la sezione **Gestione** e quindi selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della penna relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Screenshot che mostra l'icona Modifica per Configurazione SAML di base.](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** seguire questa procedura se si ha un file di metadati SP e si vuole eseguire la configurazione nella modalità avviata da IDP:

    a. Selezionare **Carica il file di metadati**.

    ![Screenshot che mostra il collegamento per Carica file di metadati.](common/upload-metadata.png)

    b. Selezionare l'icona della cartella per scegliere il file di metadati scaricato nel passaggio 4 della procedura "Configurare l'accesso Single Sign-On di Roadmunk". Selezionare quindi **Upload** (Carica).

    ![Screenshot che mostra come scegliere il file di metadati.](common/browse-upload-metadata.png)

    Dopo il caricamento del file di metadati, i valori per **Identificatore** e **URL di risposta** vengono inseriti automaticamente nella sezione **Configurazione SAML di base**.

    ![Screenshot che mostra la sezione Configurazione SAML di base. I campi Identificatore e URL di risposta sono evidenziati.](common/idp-intiated.png)

    > [!Note]
    > Se i valori **Identificatore** e **URL di risposta** non vengono popolati automaticamente, immetterli manualmente.

1. Se si vuole configurare l'applicazione in modalità avviata da SP, selezionare **Impostare URL aggiuntivi**. Nel campo **URL di accesso** digitare `https://login.roadmunk.com`

    ![Screenshot che mostra dove impostare un URL di accesso per la modalità avviata da SP.](common/metadata-upload-additional-signon.png)

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il file **XML dei metadati della federazione**. Selezionare quindi **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Screenshot che mostra il collegamento di download per il certificato di firma SAML.](common/metadataxml.png)

1. Nel riquadro **Configura Roadmunk** copiare l'URL o gli URL necessari.

    ![Screenshot che mostra dove copiare gli URL di configurazione.](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test nel portale di Azure. All'utente verrà assegnato il nome *B.Simon*.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory** > **Utenti** > **Tutti gli utenti**.
1. Nella parte superiore della finestra selezionare **Nuovo utente**.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Immettere ad esempio `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Selezionare **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso SSO di Azure concedendole l'accesso a Roadmunk.

1. Nel portale di Azure selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Roadmunk**.
1. Nella pagina di panoramica dell'app individuare la sezione **Gestione** e quindi selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente**. Nella finestra di dialogo **Aggiungi assegnazione** selezionare quindi **Utenti e gruppi**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** nell'elenco **Utenti**. Nella parte inferiore della finestra di dialogo scegliere **Seleziona**.
1. Se si prevede che agli utenti venga assegnato un ruolo, sceglierlo nel menu a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è **Accesso predefinito**.
1. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Assegna**.

## <a name="configure-roadmunk-sso"></a>Configurare l'accesso Single Sign-On di Roadmunk

1. Accedere al sito Web di Roadmunk come amministratore.

1. Nella parte inferiore della pagina selezionare l'icona dell'utente e quindi scegliere **Account Settings** (Impostazioni account).

    ![Screenshot che mostra dove selezionare le impostazioni dell'account utente.](./media/roadmunk-tutorial/account.png)

1. Passare a **Company** > **Authentication Settings** (Società > Impostazioni di autenticazione).

1. Nella pagina **Authentication Settings** (Impostazioni di autenticazione) seguire questa procedura:

    ![Screenshot che mostra la pagina Authentication Settings.](./media/roadmunk-tutorial/saml-sso.png)

    a. Attivare **SAML Single Sign On (SSO)** (Accesso Single Sign-On SAML).

    b. Nella sezione **Step 1** (Passaggio 1) caricare il file XML dei metadati o specificare l'URL per i metadati.

    c. Nella sezione **Step 2** (Passaggio 2) scaricare il file di metadati di Roadmunk e salvarlo nel computer.

    d. Se si vuole accedere tramite SSO, nella sezione **Step 3** (Passaggio 3) selezionare **Enforce SAML Sign-In Only** (Usa solo accesso Single Sign-On SAML).

    e. Selezionare **Salva**.


### <a name="create-roadmunk-test-user"></a>Creare l'utente di test di Roadmunk

1. Accedere al sito Web di Roadmunk come amministratore.

1. Nella parte inferiore della pagina selezionare l'icona dell'utente e quindi scegliere **Account Settings** (Impostazioni account).

    ![Screenshot che mostra come aprire Account Settings per l'utente di test.](./media/roadmunk-tutorial/account.png)

1. Aprire la scheda **Users** (Utenti) e selezionare **Invite User** (Invita utente).

    ![Screenshot che mostra la scheda Users. Il pulsante Invite User è evidenziato. Nella finestra aperta i campi Email e Role ruolo sono evidenziati.](./media/roadmunk-tutorial/create-user.png)

1. Nel modulo visualizzato inserire le informazioni richieste e quindi selezionare **Invite** (Invita).


## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso SSO di Azure AD usando il pannello di accesso.

Quando nel portale App personali si seleziona il riquadro **Roadmunk**, si dovrebbe accedere automaticamente all'account Roadmunk per cui si è configurato l'accesso SSO. Per altre informazioni, vedere [Accedere e avviare app dal portale App personali](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Roadmunk, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. 

Informazioni su come [applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


