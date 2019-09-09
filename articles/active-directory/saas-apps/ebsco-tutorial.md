---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con EBSCO | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory ed EBSCO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 144f7f65-69e9-4016-a151-fe1104fd6ba8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ebaf3a1d877025cafe8829bc937ef032a3c95d03
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70163448"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ebsco"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con EBSCO

Questa esercitazione descrive come integrare EBSCO con Azure Active Directory (Azure AD). Integrando EBSCO con Azure AD è possibile:

* Controllare in Azure AD chi può accedere a EBSCO.
* Abilitare gli utenti per l'accesso automatico a EBSCO con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di EBSCO abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* EBSCO supporta l'accesso SSO avviato da **SP e IDP**
* EBSCO supporta il provisioning utenti **JIT**

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.

## <a name="adding-ebsco-from-the-gallery"></a>Aggiunta di EBSCO dalla raccolta

Per configurare l'integrazione di EBSCO in Azure AD, è necessario aggiungere EBSCO dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **EBSCO** nella casella di ricerca.
1. Selezionare **EBSCO** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ebsco"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per EBSCO

Configurare e testare l'accesso SSO di Azure AD con EBSCO usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in EBSCO.

Per configurare e testare l'accesso SSO di Azure AD con EBSCO, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di EBSCO](#configure-ebsco-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di EBSCO](#create-ebsco-test-user)** : per avere una controparte di B.Simon in EBSCO collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **EBSCO** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    Nella casella di testo **Identificatore** digitare un URL: `pingsso.ebscohost.com`

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `http://search.ebscohost.com/login.aspx?authtype=sso&custid=<unique EBSCO customer ID>&profile=<profile ID>`

    > [!NOTE]
    > Poiché il valore di URL accesso non è reale, è necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere tali valori, contattare il [team di supporto clienti EBSCO](mailto:support@ebsco.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

    o   **Elementi univoci:**  

    o   **Custid** = Immettere l'ID cliente EBSCO univoco 

    o   **Profile** = I clienti possono personalizzare il collegamento in modo da indirizzare gli utenti a uno profilo specifico, in base al prodotto acquistato da EBSCO. È possibile immettere un ID di profilo specifico. I principali ID sono eds (EBSCO Discovery Service) ed ehost (database EBSCOhost). Istruzioni specifiche sull'argomento sono disponibili in [questa pagina](https://help.ebsco.com/interfaces/EBSCOhost/EBSCOhost_FAQs/How_do_I_set_up_direct_links_to_EBSCOhost_profiles_and_or_databases#profile).

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il file **XML dei metadati della federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura EBSCO** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a EBSCO.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **EBSCO**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-ebsco-sso"></a>Configurare l'accesso Single Sign-On di EBSCO

Per configurare l'accesso Single Sign-On sul lato **EBSCO**, è necessario inviare il file di **XML dei metadati** scaricato e gli URL appropriati, copiati dal portale di Azure, al [team di supporto di EBSCO](mailto:support@ebsco.com). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-ebsco-test-user"></a>Creare l'utente di test di EBSCO

Nel caso di EBSCO, il provisioning utenti viene eseguito automaticamente.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

Azure AD passa i dati necessari all'applicazione EBSCO. Il provisioning utenti di EBSCO può essere eseguito automaticamente o richiedere la compilazione di un modulo unico, a seconda che il cliente abbia o meno molti account EBSCOhost preesistenti con impostazioni personali salvate. Questo aspetto può essere esaminato con il [team di supporto EBSCO](mailto:support@ebsco.com) durante l'implementazione. In entrambi i casi, il cliente non deve creare alcun account EBSCOhost prima del test.

   >[!Note]
   >È possibile automatizzare la personalizzazione o il provisioning utenti EBSCOhost. Per informazioni sul provisioning utenti JIT (Just-In-Time), contattare il [team di supporto EBSCO](mailto:support@ebsco.com).

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

1. Quando si fa clic sul riquadro EBSCO nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione EBSCO.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md).

1. Dopo aver eseguito l'accesso all'applicazione, fare clic sul pulsante **Sign In** (Accedi) nell'angolo in alto a destra.

    ![Accesso a EBSCO nell'elenco delle applicazioni](./media/ebsco-tutorial/tutorial_ebsco_signin.png)

1. Si riceverà un messaggio unico in cui viene richiesto di associare i dati di accesso istituzionali/SAML con l'opzione **Link your existing MyEBSCOhost account to your institution account now** (Collega l'account MyEBSCOhost esistente all'account dell'istituto ora) oppure **Create a new MyEBSCOhost account and link it to your institution account** (Crea un nuovo account MyEBSCOhost e collegalo all'account dell'istituto). L'account viene usato per la personalizzazione nell'applicazione EBSCOhost. Selezionare l'opzione per **creare un nuovo account** e si noterà che il modulo per la personalizzazione è precompilato con i valori della risposta SAML, come illustrato nella schermata seguente. Fare clic su "**Continue**" (Continua) per salvare la selezione.
    
     ![Utente EBSCO nell'elenco delle applicazioni](./media/ebsco-tutorial/tutorial_ebsco_user.png)

1. Dopo aver completato la configurazione precedente, cancellare nuovamente i cookie, la cache e i dati di accesso. Le impostazioni di personalizzazione vengono memorizzate e non sarà necessario eseguire nuovamente l'accesso manuale.

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare EBSCO con Azure AD](https://aad.portal.azure.com/)

