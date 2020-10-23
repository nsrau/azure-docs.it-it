---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con IntelligenceBank | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e IntelligenceBank.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/15/2020
ms.author: jeedes
ms.openlocfilehash: 429820f41025eda262742ac9aec1de3b646b141f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91850550"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-intelligencebank"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con IntelligenceBank

Questa esercitazione descrive come integrare IntelligenceBank con Azure Active Directory (Azure AD). Integrando IntelligenceBank con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a IntelligenceBank.
* Abilitare gli utenti per l'accesso automatico a IntelligenceBank con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di IntelligenceBank abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* IntelligenceBank supporta l'accesso SSO avviato da **SP**

* Dopo aver configurato IntelligenceBank, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-intelligencebank-from-the-gallery"></a>Aggiunta di IntelligenceBank dalla raccolta

Per configurare l'integrazione di IntelligenceBank in Azure AD, è necessario aggiungere IntelligenceBank dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **IntelligenceBank** nella casella di ricerca.
1. Selezionare **IntelligenceBank** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-intelligencebank"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per IntelligenceBank

Configurare e testare l'accesso SSO di Azure AD con IntelligenceBank usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in IntelligenceBank.

Per configurare e testare l'accesso SSO di Azure AD con IntelligenceBank, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di IntelligenceBank](#configure-intelligencebank-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di IntelligenceBank](#create-intelligencebank-test-user)** : per avere una controparte di B.Simon in IntelligenceBank collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **IntelligenceBank** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<SUBDOMAIN>.intelligencebank.com`

    b. Nella casella di testo **Identificatore (ID entità)** immettere uno dei valori seguenti:

    - `IB`
    - `IntelligenceBank`
    - `https://<SUBDOMAIN>.intelligencebank.com`

    c. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<SUBDOMAIN>.intelligencebank.com/auth`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'URL di accesso, l'identificatore e l'URL di risposta effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di IntelligenceBank](mailto:helpdesk@intelligencebank.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura IntelligenceBank** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a IntelligenceBank.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **IntelligenceBank**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-intelligencebank-sso"></a>Configura l'accesso Single Sign-On di IntelligenceBank

1. In un'altra finestra del Web browser accedere al sito aziendale di IntelligenceBank come amministratore.

1. Fare clic su **Authenticator** (Autenticatore) e fare clic su **Add New** (Aggiungi nuovo)

    ![Screenshot che mostra la scheda Administrator e l'icona Add New.](./media/intelligencebank-tutorial/authenticator.PNG)

1. Seguire questa procedura:

    ![Screenshot che mostra i campi in cui immettere le informazioni in questo passaggio.](./media/intelligencebank-tutorial/urls.PNG)

    a. Nella casella di testo **Name** (Nome) immettere il nome, ad esempio `azureadsso`.

    b. Nella casella di testo **Description** (Descrizione) immettere una descrizione valida.

    c. Selezionare **SAML** nell'elenco a discesa **Type** (Tipo).

    d. Nella casella di testo **Remote Url** (URL remoto) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    e. Nella casella di testo **Host** incollare il valore di **ID entità** copiato dal portale di Azure.

    f. Aprire nel Blocco note il file del **certificato (Base64)** scaricato dal portale di Azure e incollarne il contenuto nella casella di testo **CertData**

    g. Nella casella di testo **SingleLogoutService** incollare il valore di **URL di disconnessione** copiato dal portale di Azure.

    h. Fare clic sul pulsante **Save** (Salva).

### <a name="create-intelligencebank-test-user"></a>Creare l'utente di test di IntelligenceBank

1. In un'altra finestra del Web browser accedere al sito aziendale di IntelligenceBank come amministratore.

1. Passare a **Admin** -> **Users** (Amministrazione > Utenti) e selezionare l'icona **Add New User** (Aggiungi nuovo utente) per aggiungere l'**utente**.

    ![Screenshot che mostra l'icona Users selezionata nella scheda Users.](./media/intelligencebank-tutorial/creating-user.PNG)

1. Compilare i campi necessari in base ai requisiti dell'organizzazione e fare clic su **Save** (Salva).

    ![Screenshot che mostra la pagina Add New User in cui immettere le informazioni utente.](./media/intelligencebank-tutorial/creating-user-1.PNG)

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di IntelligenceBank nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di IntelligenceBank per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare IntelligenceBank con Azure AD](https://aad.portal.azure.com/)

- [Informazioni sul controllo sessioni in Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Come proteggere IntelligenceBank con visibilità e controlli avanzati](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
