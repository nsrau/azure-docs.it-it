---
title: 'Esercitazione: Integrazione di Azure Active Directory con Replicon | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Replicon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 02a62f15-917c-417c-8d80-fe685e3fd601
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b27615b0c76b5c23bbc79788431b0e909b8bf22a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67092757"
---
# <a name="tutorial-integrate-replicon-with-azure-active-directory"></a>Esercitazione: Integrare Replicon con Azure Active Directory

Questa esercitazione descrive come integrare Replicon con Azure Active Directory (Azure AD). Integrando Replicon con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Replicon.
* Abilitare gli utenti per l'accesso automatico a Replicon con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere una versione di valutazione gratuita per un mese [qui](https://azure.microsoft.com/pricing/free-trial/).
* Sottoscrizione di Replicon abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Replicon supporta l'accesso SSO avviato da **SP**.

## <a name="adding-replicon-from-the-gallery"></a>Aggiunta di Replicon dalla raccolta

Per configurare l'integrazione di Replicon in Azure AD, è necessario aggiungere Replicon dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Replicon** nella casella di ricerca.
1. Selezionare **Replicon** nel riquadro dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

Configurare e testare l'accesso SSO di Azure AD con Replicon usando un utente di test di nome **B. Simon**. Per il corretto funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Replicon.

Per configurare e testare l'accesso SSO di Azure AD con Replicon, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Replicon](#configure-replicon-sso)** : per configurare le impostazioni dell'accesso Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Replicon](#create-replicon-test-user)** : per avere una controparte di B. Simon in Replicon collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Replicon** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella pagina **Configurazione SAML di base** immettere i valori per i campi seguenti:

    1. Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://global.replicon.com/!/saml2/<client name>/sp-sso/post`

    1. Nella casella di testo **Identificatore** digitare un URL usando il modello seguente: `https://global.replicon.com/!/saml2/<client name>`

    1. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://global.replicon.com/!/saml2/<client name>/sso/post`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Replicon](https://www.replicon.com/customerzone/contact-support). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Fare clic sull'icona Modifica (la penna) per **Certificato di firma SAML** per modificare le impostazioni.

    ![Algoritmo di firma](common/signing-algorithm.png)

    1. Selezionare **Firma asserzione SAML** per **Opzione di firma**.

    1. Selezionare **SHA-256** per **Algoritmo di firma**.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **XML metadati federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer in uso.

   ![Collegamento di download del certificato](common/metadataxml.png)

### <a name="configure-replicon-sso"></a>Configurare l'accesso Single Sign-On per Replicon

1. In un'altra finestra del Web browser accedere al sito aziendale di Replicon come amministratore.

2. Per configurare SAML 2.0, eseguire la procedura seguente:

    ![Abilita autenticazione SAML](./media/replicon-tutorial/ic777805.png "autenticazione SAML abilitare")

    a. Per visualizzare la finestra di dialogo **EnableSAML Authentication2** accodare i seguenti elementi all'URL, dopo la chiave della società: `/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

    * Di seguito è illustrato lo schema dell'URL completo: `https://na2.replicon.com/\<YourCompanyKey\>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

   b. Fare clic su **+** per espandere la sezione **v20Configuration**.

   c. Fare clic su **+** per espandere la sezione **metaDataConfiguration**.

   d. Selezionare **SHA256** per xmlSignatureAlgorithm

   e. Fare clic su **Scegli file** per selezionare il file XML dei metadati del provider di identità, quindi scegliere **Invia**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

In questa sezione verrà creato un utente di test di nome B. Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `BrittaSimon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abiliterà B. Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Replicon.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco di applicazioni selezionare **Replicon**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B. Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-replicon-test-user"></a>Creare un utente di test di Replicon

In questa sezione viene creato un utente di nome B. Simon in Replicon.

**Per creare un utente manualmente, seguire questa procedura:**

1. In una finestra del Web browser accedere al sito aziendale di Replicon come amministratore.

2. Passare ad **Amministrazione \> Utenti**.

    ![Utenti](./media/replicon-tutorial/ic777806.png "Utenti")

3. Fare clic su **+Aggiungi utente**.

    ![Aggiungere un utente](./media/replicon-tutorial/ic777807.png "Aggiungere un utente")

4. Nella sezione **Profilo utente** , eseguire la procedura seguente:

    ![Profilo utente](./media/replicon-tutorial/ic777808.png "Profilo utente")

    a. Nella casella di testo **Login Name** (Nome di accesso) digitare l'indirizzo di posta elettronica dell'utente di Azure AD di cui si vuole effettuare il provisioning, ad esempio `B.Simon@contoso.com`.

    > [!NOTE]
    > Il nome di accesso deve corrispondere all'indirizzo di posta elettronica dell'utente in Azure AD

    b. In **Tipo di autenticazione** selezionare **SSO**.

    c. Impostare l'ID di autenticazione sullo stesso valore del nome di accesso (indirizzo di posta elettronica dell'utente in Azure AD)

    d. Nella casella di testo **Reparto** , digitare il reparto dell'utente.

    e. In **Tipo di dipendente** selezionare **Amministratore**.

    f. Fare clic su **Salva profilo utente**.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente offerti da Replicon per eseguire il provisioning degli account utente di Azure AD.

### <a name="test-sso"></a>Testare l'accesso SSO

Quando si seleziona il riquadro di Replicon nel Pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Replicon per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)