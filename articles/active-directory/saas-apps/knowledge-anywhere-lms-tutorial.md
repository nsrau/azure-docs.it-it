---
title: 'Esercitazione: Integrazione di Azure Active Directory con Knowledge Anywhere LMS | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Knowledge Anywhere LMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 5cfa07b1-a792-4f0a-8c6f-1a13142193d9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34553c92f54992698a7e3930ac302ef970edd7c7
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236678"
---
# <a name="tutorial-integrate-knowledge-anywhere-lms-with-azure-active-directory"></a>Esercitazione: Integrare Knowledge Anywhere LMS con Azure Active Directory

Questa esercitazione descrive come integrare Knowledge Anywhere LMS con Azure Active Directory (Azure AD). Integrando Knowledge Anywhere LMS con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Knowledge Anywhere LMS.
* Abilitare gli utenti per l'accesso automatico a Knowledge Anywhere LMS con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Knowledge Anywhere LMS abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Knowledge Anywhere LMS supporta l'accesso SSO avviato da **SP** e il provisioning utenti **Just-In-Time**.

## <a name="adding-knowledge-anywhere-lms-from-the-gallery"></a>Aggiunta di Knowledge Anywhere LMS dalla raccolta

Per configurare l'integrazione di Knowledge Anywhere LMS in Azure AD, è necessario aggiungere Knowledge Anywhere LMS dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Knowledge Anywhere LMS** nella casella di ricerca.
1. Selezionare **Knowledge Anywhere LMS** nel riquadro dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

Configurare e testare l'accesso Single Sign-On di Azure AD con Knowledge Anywhere LMS usando un utente di test di nome **B. Simon**. Per il corretto funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Knowledge Anywhere LMS.

Per configurare e testare l'accesso SSO di Azure AD con Knowledge Anywhere LMS, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** per consentire agli utenti di usare questa funzionalità.
2. **[Configurare Knowledge Anywhere LMS](#configure-knowledge-anywhere-lms)** per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente test di Azure AD](#create-an-azure-ad-test-user)** per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di Knowledge Anywhere LMS](#create-knowledge-anywhere-lms-test-user)** : per avere una controparte di B. Simon in Knowledge Anywhere LMS collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-sso)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Knowledge Anywhere LMS** del [portale di Azure](https://portal.azure.com/) trovare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    1. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<CLIENTNAME>.knowledgeanywhere.com/`

    1. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<CLIENTNAME>.knowledgeanywhere.com/SSO/SAML/Response.aspx?<IDPNAME>`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore e l'URL di risposta effettivi, come illustrato più avanti in questa esercitazione.

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<CLIENTNAME>.knowledgeanywhere.com/`

    > [!NOTE]
    > Poiché il valore di URL accesso non è reale, è necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere questo valore, contattare il [team di supporto clienti di Knowledge Anywhere LMS](https://knowany.zendesk.com/hc/en-us/articles/360000469034-SAML-2-0-Single-Sign-On-SSO-Set-Up-Guide). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML**, trovare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer in uso.

   ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura Knowledge Anywhere LMS** copiare gli URL appropriati in base alle esigenze.

   ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="configure-knowledge-anywhere-lms"></a>Configurare Knowledge Anywhere LMS

1. Per automatizzare la configurazione all'interno di Knowledge Anywhere LMS, è necessario installare l'**estensione del browser per l'accesso sicuro alle app personali** facendo clic su **Installa estensione**.

    ![Estensione MyApps](common/install-myappssecure-extension.png)

2. Dopo aver aggiunto l'estensione al browser, fare clic su **Configura Knowledge Anywhere LMS** per passare direttamente all'applicazione Knowledge Anywhere LMS. Nell'applicazione fornire le credenziali di amministratore per accedere a Knowledge Anywhere LMS. L'estensione del browser configurerà automaticamente l'applicazione per l'utente e automatizzerà i passaggi da 3 a 7.

    ![Eseguire la configurazione](common/setup-sso.png)

3. Per configurare manualmente Knowledge Anywhere LMS, aprire una nuova finestra del Web browser, accedere al sito aziendale di Knowledge Anywhere LMS come amministratore e seguire questa procedura:

4. Selezionare la scheda **Site** (Sito).

    ![Configurazione di Knowledge Anywhere LMS](./media/knowledge-anywhere-lms-tutorial/configure1.png)

5. Selezionare la scheda **SAML Settings** (Impostazioni SAML).

    ![Configurazione di Knowledge Anywhere LMS](./media/knowledge-anywhere-lms-tutorial/configure2.png)

6. Fare clic su **Add New** (Aggiungi nuova).

    ![Configurazione di Knowledge Anywhere LMS](./media/knowledge-anywhere-lms-tutorial/configure3.png)

7. Nella pagina **Add/Update SAML Settings** (Aggiungi/Aggiorna impostazioni SAML) seguire questa procedura:

    ![Configurazione di Knowledge Anywhere LMS](./media/knowledge-anywhere-lms-tutorial/configure4.png)

    a. Immettere il nome IDP previsto dall'organizzazione, ad esempio `Azure`.

    b. Nella casella di testo **IDP Entity ID** (ID entità IDP) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    c. Nella casella di testo **IDP URL** (URL IDP) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    d. Aprire nel Blocco note il file di certificato scaricato dal portale di Azure, copiare il contenuto del certificato e incollarlo nella casella di testo **Certificate** (Certificato).

    e. Nella casella di testo **Logout URL** (URL di disconnessione) incollare il valore di **URL disconnessione** copiato dal portale di Azure.

    f. Selezionare **Main Site** (Sito principale) nell'elenco a discesa **Domain** (Dominio).

    g. Copiare il valore di **SP Entity ID** (ID entità SP) e incollarlo nella casella di testo **Identificatore** nella sezione **Configurazione SAML di base** del portale di Azure.

    h. Copiare il valore di **SP Response(ACS) URL** (URL risposta SP - ACS) e incollarlo nella casella di testo **URL di risposta** nella sezione **Configurazione SAML di base** del portale di Azure.

    i. Fare clic su **Save**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

In questa sezione verrà creato un utente di test di nome B. Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B. Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `BrittaSimon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abiliterà B. Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Knowledge Anywhere LMS.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Knowledge Anywhere LMS**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B. Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-knowledge-anywhere-lms-test-user"></a>Creare l'utente di test di Knowledge Anywhere LMS

In questa sezione viene creato un utente di nome B. Simon in Knowledge Anywhere LMS. Knowledge Anywhere LMS supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in Knowledge Anywhere LMS, ne viene creato uno nuovo dopo l'autenticazione.

### <a name="test-sso"></a>Testare l'accesso SSO

Quando si seleziona il riquadro di Knowledge Anywhere LMS nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Knowledge Anywhere LMS per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)