---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con OpenAthens | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e OpenAthens.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dd4adfc7-e238-41d5-8b25-1811f08078b6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43fc2272a81672ea613bdcbe17c5381e99cafbff
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053206"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-openathens"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con OpenAthens

In questa esercitazione si apprenderà come integrare OpenAthens con Azure Active Directory (Azure AD). Integrando OpenAthens con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a OpenAthens.
* Abilitare gli utenti per l'accesso automatico a OpenAthens con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di OpenAthens abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* OpenAthens supporta l'accesso SSO avviato da **IDP**
* OpenAthens supporta il provisioning utenti **JIT**

## <a name="adding-openathens-from-the-gallery"></a>Aggiunta di OpenAthens dalla raccolta

Per configurare l'integrazione di OpenAthens in Azure AD, è necessario aggiungere OpenAthens dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **OpenAthens** nella casella di ricerca.
1. Selezionare **OpenAthens** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-openathens"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per OpenAthens

Configurare e testare l'accesso SSO di Azure AD con OpenAthens usando un utente di test di nome **B.Simon**. Per il corretto funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in OpenAthens.

Per configurare e testare l'accesso SSO di Azure AD con OpenAthens, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    * **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    * **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On per OpenAthens](#configure-openathens-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    * **[Creare un utente di test di OpenAthens](#create-openathens-test-user)** : per avere una controparte di B.Simon in OpenAthens collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **OpenAthens** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** caricare il **file di metadati del provider di servizi**, la cui procedura è illustrata più avanti in questa esercitazione.

    a. Fare clic su **Carica il file di metadati**.

    ![Caricamento dei metadati di OpenAthens](common/upload-metadata.png)

    b. Fare clic su **logo cartella** per selezionare il file di metadati e fare quindi clic su **Upload**.

    ![Selezione dei metadati da caricare di OpenAthens](common/browse-upload-metadata.png)

    c. Dopo il caricamento del file di metadati, il valore **Identificatore** viene inserito automaticamente nella casella di testo della sezione **Configurazione SAML di base**:

    ![Informazioni sull'accesso Single Sign-On per URL e dominio di OpenAthens](common/idp-identifier.png)

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il file **XML dei metadati della federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura OpenAthens** copiare gli URL appropriati in base alle esigenze.

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

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a OpenAthens.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **OpenAthens**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-openathens-sso"></a>Configurare l'accesso Single Sign-On per OpenAthens

1. In un'altra finestra del Web browser accedere al sito aziendale di OpenAthens come amministratore.

1. Selezionare **Connections** (Connessioni) nella scheda **Management** (Gestione).

    ![Configura accesso Single Sign-On](./media/openathens-tutorial/tutorial_openathens_application1.png)

1. Selezionare **SAML 1.1 o 2.0** e quindi selezionare il pulsante **Configura**.

    ![Configura accesso Single Sign-On](./media/openathens-tutorial/tutorial_openathens_application2.png)

1. Per aggiungere la configurazione, selezionare il pulsante **Sfoglia** per caricare il file di metadati con estensione XML scaricato dal portale di Azure e quindi selezionare **Aggiungi**.

    ![Configura accesso Single Sign-On](./media/openathens-tutorial/tutorial_openathens_application3.png)

1. Nella scheda **Dettagli** seguire questa procedura.

    ![Configura accesso Single Sign-On](./media/openathens-tutorial/tutorial_openathens_application4.png)

    a. In **Display name mapping** (Mapping nome visualizzato) selezionare **Use attribute** (Usa attributo).

    b. Nella casella di testo **Display name attribute** (Attributo nome visualizzato) immettere il valore `http://schema.microsoft.com/identity/claims/displayname`.

    c. In **Unique user mapping** (Mapping utente univoco) selezionare **Use attribute** (Usa attributo).

    d. Nella casella di testo **Unique user attribute** (Attributo utente univoco) immettere il valore `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    e. In **Status** (Stato) selezionare tutte le tre caselle di controllo.

    f. In **Create local accounts** (Crea account locali) selezionare **automatically** (automaticamente).

    g. Selezionare **Save changes** (Salva modifiche).

    h. Dalla scheda **Relying Party** copiare il valore di **URL dei metadati** e aprire l'URL nel browser per scaricare il file **XML dei metadati SP**. Caricare questo file di metadati SP nella sezione **Configurazione SAML di base** in Azure AD.

    ![Configura accesso Single Sign-On](./media/openathens-tutorial/tutorial_openathens_application5.png)

### <a name="create-openathens-test-user"></a>Creare l'utente di test di OpenAthens

In questa sezione viene creato un utente di nome Britta Simon in OpenAthens. OpenAthens supporta il **provisioning utenti JIT**, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in OpenAthens, ne viene creato uno nuovo dopo l'autenticazione.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di OpenAthens nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione OpenAthens per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare OpenAthens con Azure AD](https://aad.portal.azure.com/)