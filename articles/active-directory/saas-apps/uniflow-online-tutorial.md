---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con uniFLOW Online | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e uniFLOW Online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28313d27-638c-4d50-92ad-d093f2ae9ecf
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9eb369047574ef76dd31996fd16399380ea027c8
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74823153"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-uniflow-online"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con uniFLOW Online

Questa esercitazione descrive come integrare uniFLOW Online con Azure Active Directory (Azure AD). Integrando uniFLOW Online con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a uniFLOW Online.
* Abilitare gli utenti per l'accesso automatico a uniFLOW Online con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di uniFLOW Online abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* uniFLOW Online supporta l'accesso SSO avviato da **SP**

## <a name="adding-uniflow-online-from-the-gallery"></a>Aggiunta di uniFLOW Online dalla raccolta

Per configurare l'integrazione di uniFLOW Online in Azure AD, è necessario aggiungere uniFLOW Online dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **uniFLOW Online** nella casella di ricerca.
1. Selezionare **uniFLOW Online** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-uniflow-online"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per uniFLOW Online

Configurare e testare l'accesso SSO di Azure AD con uniFLOW Online usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in uniFLOW Online.

Per configurare e testare l'accesso SSO di Azure AD con uniFLOW Online, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    * **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    * **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On per uniFLOW Online](#configure-uniflow-online-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    * **[Creare l'utente di test di uniFLOW Online](#create-uniflow-online-test-user)** : per avere una controparte di B.Simon in uniFLOW Online collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **uniFLOW Online** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente:

    | | |
    |-|-|
    | `https://<tenant_domain_name>.eu.uniFLOWonline.com`|
    | `https://<tenant_domain_name>.us.uniFLOWonline.com`|
    | `https://<tenant_domain_name>.sg.uniFLOWonline.com`|
    | `https://<tenant_domain_name>.jp.uniFLOWonline.com`|
    | `https://<tenant_domain_name>.au.uniFLOWonline.com`|

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente:

    | | |
    |-|-|
    | `https://<tenant_domain_name>.eu.uniFLOWonline.com`|
    | `https://<tenant_domain_name>.us.uniFLOWonline.com`|
    | `https://<tenant_domain_name>.sg.uniFLOWonline.com`|
    | `https://<tenant_domain_name>.jp.uniFLOWonline.com`|
    | `https://<tenant_domain_name>.au.uniFLOWonline.com`|

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di uniFLOW Online](mailto:support@nt-ware.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. L'applicazione uniFLOW Online prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/default-attributes.png)

1. Oltre quelli elencati in precedenza, l'applicazione uniFLOW Online prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.

    | NOME |  Attributo di origine|
    | -----------| --------------- |
    | displayname | user.displayname |
    | nickname | user.onpremisessamaccountname |

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e salvarlo nel computer.

    ![Collegamento di download del certificato](common/copy-metadataurl.png)

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a uniFLOW Online.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **uniFLOW Online**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-uniflow-online-sso"></a>Configurare l'accesso Single Sign-On per uniFLOW Online

1. In un'altra finestra del Web browser accedere al sito Web di uniFLOW Online come amministratore.

1. Nel pannello di spostamento a sinistra selezionare la scheda **User** (Utente).

    ![Configurazione di uniFLOW Online](./media/uniflow-online-tutorial/configure1.png)

1. Fare clic su **Provider di identità**.

    ![Configurazione di uniFLOW Online](./media/uniflow-online-tutorial/configure2.png)

1. Fare clic su **Add identity provider** (Aggiungi provider di identità).

    ![Configurazione di uniFLOW Online](./media/uniflow-online-tutorial/configure3.png)

1. Nella sezione **ADD IDENTITY PROVIDER** (AGGIUNGI PROVIDER DI IDENTITÀ) seguire questa procedura:


    ![Configurazione di uniFLOW Online](./media/uniflow-online-tutorial/configure4.png)

    a. Immettere il nome visualizzato, ad esempio *AzureAD SSO*.

    b. Per **Provider type** (Tipo di provider) selezionare l'opzione **WS-Fed** nell'elenco a discesa.

    c. Per **WS-Fed type** (Tipo WS-Fed) selezionare l'opzione **Azure Active Directory** nell'elenco a discesa.

    d. Fare clic su **Save**.

1. Nella scheda **General** (Generale) seguire questa procedura:

    ![Configurazione di uniFLOW Online](./media/uniflow-online-tutorial/configure5.png)

    a. Immettere il nome visualizzato, ad esempio *AzureAD SSO*.

    b. Selezionare l'opzione **From URL** (Da URL) per **ADGS Federation Metadata** (Metadati della federazione ADGS).

    c. Nella casella di testo **Federation Metadata URL** (URL dei metadati della federazione) incollare il valore di **URL dei metadati di federazione dell'app** copiato dal portale di Azure.

    d. Impostare **Identity provider** (Provider di identità) su **Enabled** (Abilitato).

    e. Impostare **Automatic user registration** (Registrazione utenti automatica) su **Activated** (Attivata).

    f. Fare clic su **Save**.

### <a name="create-uniflow-online-test-user"></a>Creare l'utente di test di uniFLOW Online

1. In un'altra finestra del Web browser accedere al sito Web di uniFLOW Online come amministratore.

1. Nel pannello di spostamento a sinistra selezionare la scheda **User** (Utente).

    ![Configurazione di uniFLOW Online](./media/uniflow-online-tutorial/configure1.png)

1. Fare clic su **Add user** (Aggiungi utente).

    ![Configurazione di uniFLOW Online](./media/uniflow-online-tutorial/user1.png)

1. Fare clic su **Create user manually** (Crea utente manualmente).

    ![Configurazione di uniFLOW Online](./media/uniflow-online-tutorial/user2.png)

1. Nella scheda **CREATE USER MANUALLY** (CREA UTENTE MANUALMENTE) specificare i valori richiesti in base ai requisiti dell'organizzazione.

    ![Configurazione di uniFLOW Online](./media/uniflow-online-tutorial/user3.png)

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di uniFLOW Online nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di uniFLOW Online per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare uniFLOW Online con Azure AD](https://aad.portal.azure.com/)