---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Salesforce | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: d2d7d420-dc91-41b8-a6b3-59579e043b35
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea87c1e0b6379afac6a9433adb0e301626e27811
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68986175"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-salesforce"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Salesforce

Questa esercitazione descrive come integrare Salesforce con Azure Active Directory (Azure AD). Integrando Salesforce con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Salesforce.
* Abilitare gli utenti per l'accesso automatico a Salesforce con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Salesforce abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Salesforce supporta l'accesso SSO avviato da **SP**

* Salesforce supporta il provisioning **Just-In-Time** (JIT) degli utenti

* Salesforce supporta il [**provisioning utenti automatico**](salesforce-provisioning-tutorial.md)

* È ora possibile configurare l'applicazione per dispositivi mobili Salesforce con Azure AD per abilitare l'accesso SSO. In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

## <a name="adding-salesforce-from-the-gallery"></a>Aggiunta di Salesforce dalla raccolta

Per configurare l'integrazione di Salesforce in Azure AD, è necessario aggiungere Salesforce dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Salesforce** nella casella di ricerca.
1. Selezionare **Salesforce** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-salesforce"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Salesforce

Configurare e testare l'accesso SSO di Azure AD con Salesforce usando un utente di test di nome **B.Simon**. Per il corretto funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Salesforce.

Per configurare e testare l'accesso SSO di Azure AD con Salesforce, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
2. **[Configurare l'accesso Single Sign-On di Salesforce](#configure-salesforce-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Salesforce](#create-salesforce-test-user)** : per avere una controparte di B. Simon in Salesforce collegata alla rappresentazione dell'utente in Azure AD.
3. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Salesforce, seguire questa procedura:

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Salesforce** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    a. Nella casella di testo **URL di accesso** digitare il valore usando il modello seguente:

    Account aziendale: `https://<subdomain>.my.salesforce.com`

    Account sviluppatore: `https://<subdomain>-dev-ed.my.salesforce.com`

    b. Nella casella di testo **Identificatore** digitare il valore adottando il modello seguente:

    Account aziendale: `https://<subdomain>.my.salesforce.com`

    Account sviluppatore: `https://<subdomain>-dev-ed.my.salesforce.com`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Salesforce](https://help.salesforce.com/support).

1. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura Salesforce** copiare gli URL appropriati in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Salesforce.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Salesforce**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-salesforce-sso"></a>Configurare l'accesso Single Sign-On di Salesforce

1. Aprire una nuova scheda del browser e accedere all'account di amministratore di Salesforce.

2. Fare clic su **Setup** (Configura) sotto l'**icona Settings** (Impostazioni) nell'angolo superiore destro della pagina.

    ![Configure Single Sign-On](./media/salesforce-tutorial/configure1.png)

3. Scorrere verso il basso fino alla voce **SETTINGS** (Impostazioni) nel riquadro di spostamento e fare clic su **Identity** (Identità) per espandere la sezione corrispondente. Fare quindi clic su **Single Sign-On Settings**.

    ![Configure Single Sign-On](./media/salesforce-tutorial/sf-admin-sso.png)

4. Nella pagina **Single Sign-On Settings** fare clic su **Edit**.

    ![Configure Single Sign-On](./media/salesforce-tutorial/sf-admin-sso-edit.png)

    > [!NOTE]
    > Se non si è in grado di abilitare le impostazioni dell'accesso Single Sign-On per l'account Salesforce, potrebbe essere necessario contattare il [team di supporto clienti di Salesforce](https://help.salesforce.com/support).

5. Selezionare **SAML Enabled**, quindi fare clic su **Save**.

      ![Configure Single Sign-On](./media/salesforce-tutorial/sf-enable-saml.png)

6. Per configurare le impostazioni dell'accesso Single Sign-On SAML, fare clic su **New from Metadata File** (Nuovo da file di metadati).

    ![Configure Single Sign-On](./media/salesforce-tutorial/sf-admin-sso-new.png)

7. Fare clic su **Scegli file** per caricare il file XML di metadati scaricato dal portale di Azure e fare clic su **Crea**.

    ![Configure Single Sign-On](./media/salesforce-tutorial/xmlchoose.png)

8. Nella pagina **SAML Single Sign-On Settings** (Impostazioni SAML Single Sign-On) i campi vengono popolati automaticamente. Quindi fare clic su Salva.

    ![Configure Single Sign-On](./media/salesforce-tutorial/salesforcexml.png)

9. Nel pannello di navigazione sinistro in Salesforce fare clic su **Company Settings** (Impostazioni aziendali) per espandere la sezione correlata e quindi fare clic su **My Domain** (Dominio personale).

    ![Configure Single Sign-On](./media/salesforce-tutorial/sf-my-domain.png)

10. Scorrere verso il basso fino alla sezione **Authentication Configuration**, quindi fare clic su **Edit**.

    ![Configure Single Sign-On](./media/salesforce-tutorial/sf-edit-auth-config.png)

11. Nella sezione **Authentication Configuration** (Autenticazione configurazione) selezionare **AzureSSO** come **Authentication Service** (Servizio di autenticazione) della configurazione SSO SAML e quindi fare clic su  **Save** (Salva).

    ![Configure Single Sign-On](./media/salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > Se vengono selezionati più servizi di autenticazione, agli utenti viene richiesta la selezione del servizio di autenticazione da usare per l'accesso quando si tenta di avviare l'accesso Single Sign-On all'ambiente di Salesforce. Se non si vuole visualizzare la richiesta, è consigliabile **lasciare deselezionati tutti gli altri servizi di autenticazione**.

### <a name="create-salesforce-test-user"></a>Creare l'utente di test di Salesforce

In questa sezione viene creato un utente di nome B.Simon in Salesforce. Salesforce supporta il provisioning JIT (Just-In-Time) che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in Salesforce, ne viene creato uno nuovo quando si prova ad accedere a Salesforce. Salesforce supporta anche il provisioning utenti automatico; [qui](salesforce-provisioning-tutorial.md) è possibile trovare altre informazioni su come configurare il provisioning utenti automatico.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Salesforce nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Salesforce per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="test-sso-for-salesforce-mobile"></a>Testare l'accesso SSO per Salesforce (per dispositivi mobili)

1. Aprire l'applicazione per dispositivi mobili Salesforce. Nella pagina di accesso fare clic su **Use Custom Domain** (Usa dominio personalizzato).

    ![App per dispositivi mobili Salesforce](media/salesforce-tutorial/mobile-app1.png)

1. Nella casella di testo **Custom Domain** (Dominio personalizzato) immettere il nome del dominio personalizzato registrato e fare clic su **Continue** (Continua).

    ![App per dispositivi mobili Salesforce](media/salesforce-tutorial/mobile-app2.png)

1. Immettere le credenziali di Azure AD per accedere nell'applicazione Salesforce e fare clic su **Next** (Avanti).

    ![App per dispositivi mobili Salesforce](media/salesforce-tutorial/mobile-app3.png)

1. Nella pagina **Allow Access** (Consenti accesso) come illustrato di seguito, fare clic su **Allow** (Consenti) per concedere l'accesso all'applicazione Salesforce.

    ![App per dispositivi mobili Salesforce](media/salesforce-tutorial/mobile-app4.png)

1. Al termine dell'accesso verrà infine visualizzata la home page dell'applicazione.

    ![App per dispositivi mobili Salesforce](media/salesforce-tutorial/mobile-app5.png) ![App per dispositivi mobili Salesforce](media/salesforce-tutorial/mobile-app6.png)

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Configura provisioning utenti](salesforce-provisioning-tutorial.md)

- [Provare Salesforce con Azure AD](https://aad.portal.azure.com)
