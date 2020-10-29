---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Samsara | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Samsara.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: jeedes
ms.openlocfilehash: 9512287f656c6c64399cb8749b7451a5a780bba8
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92677642"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-samsara"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Samsara

Questa esercitazione descrive come integrare Samsara con Azure Active Directory (Azure AD). Integrando Samsara con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Samsara.
* Abilitare gli utenti per l'accesso automatico a Samsara con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Samsara abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Samsara supporta l'accesso SSO avviato da **SP** e **IDP**
* Samsara supporta il provisioning utenti **JIT**

## <a name="adding-samsara-from-the-gallery"></a>Aggiunta di Samsara dalla raccolta

Per configurare l'integrazione di Samsara in Azure AD, è necessario aggiungere Samsara dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory** .

    ![Pulsante Azure Active Directory](common/select-azuread.png)
    
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni** .

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione** .

    ![Pulsante Nuova applicazione](common/add-new-app.png)

1. Nella sezione **Aggiungi dalla raccolta** digitare **Samsara** nella casella di ricerca.

     ![OneTrust Privacy Management Software nell'elenco dei risultati](common/search-new-app.png)

1. Selezionare **Samsara** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-sso-for-samsara"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Samsara

Configurare e testare l'accesso SSO di Azure AD con Samsara usando un utente di test di nome **B.Simon** . Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Samsara.

Per configurare e testare l'accesso SSO di Azure AD con Samsara, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Samsara](#configure-samsara-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Samsara](#create-samsara-test-user)** : per avere una controparte di B.Simon in Samsara collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Samsara** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On** .

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML** .

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **URL di accesso** digitare un URL in formato `https://cloud.samsara.com/signin/<ORGID>` per gli utenti cloud statunitensi o in formato `https://cloud.eu.samsara.com/signin/<ORGID>` per gli utenti cloud europei

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `urn:auth0:samsara-dev:samlp-orgid-<ORGID>`

    c. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://samsara-dev.auth0.com/login/callback?connection=samlp-orgid-<ORGID>`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con i valori effettivi di URL di accesso, URL di risposta e identificatore. Contattare il [team di supporto clienti di Samsara](mailto:support@samsara.com) per ottenere questi valori oppure in Samsara passare a **Settings** > **Single-Sign-On** > **New SAML Connection** (Impostazioni > Single-Sign-On > Nuova connessione SAML) per ottenere il valore di \<ORGID\>. È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura Samsara** copiare il valore dell' **URL di accesso**

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)
    
### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory** , **Utenti** e quindi **Tutti gli utenti** .
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password** .
   1. Fare clic su **Crea** .

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Samsara.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni** .
1. Nell'elenco delle applicazioni selezionare **Samsara** .
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi** .
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione** .
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo** . Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna** .

## <a name="configure-samsara-sso"></a>Configurare l'accesso Single Sign-On di Samsara

Per configurare l'accesso Single Sign-On sul lato **Samsara** , è necessario inviare il **certificato (Base64)** scaricato e il valore di **URL di accesso** , copiato dal portale di Azure, al [team di supporto di Samsara](mailto:support@samsara.com). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-samsara-test-user"></a>Creare l'utente di test di Samsara

In questa sezione viene creato un utente di nome B.Simon in Samsara. Samsara supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in Samsara, dopo l'autenticazione ne viene creato uno nuovo con il ruolo predefinito Admin (No Dash Cam Access) for Organization (Amministratore per l'organizzazione senza accesso a dash cam). In base alle esigenze, è quindi possibile definire in Samsara un ruolo di livello superiore o inferiore per l'utente.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

1. Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di Samsara, in cui è possibile avviare il flusso di accesso. 

2. Passare direttamente all'URL di accesso di Samsara e avviare il flusso di accesso da questa posizione.

3. È possibile usare il Pannello di accesso Microsoft. Quando si fa clic sul riquadro di Samsara nel pannello di accesso, si verrà reindirizzati all'URL di accesso di Samsara. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Samsara, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).