---
title: 'Esercitazione: Integrazione di Azure Active Directory con ExpenseIn | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory ed ExpenseIn.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 6ac8053b-a216-45d8-bf5e-ecd37d808e57
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c09542013dff3a18965d1070216a938c26a144e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67102850"
---
# <a name="tutorial-integrate-expensein-with-azure-active-directory"></a>Esercitazione: Integrazione di ExpenseIn con Azure Active Directory

Questa esercitazione descrive come integrare ExpenseIn con Azure Active Directory (Azure AD). Integrando ExpenseIn con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a ExpenseIn.
* Abilitare gli utenti per l'accesso automatico a ExpenseIn con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di ExpenseIn abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. ExpenseIn supporta l'accesso SSO avviato da **SP e IDP**.

## <a name="adding-expensein-from-the-gallery"></a>Aggiunta di ExpenseIn dalla raccolta

Per configurare l'integrazione di ExpenseIn in Azure AD, è necessario aggiungere ExpenseIn dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **ExpenseIn** nella casella di ricerca.
1. Selezionare **ExpenseIn** nel riquadro dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

Configurare e testare l'accesso SSO di Azure AD con ExpenseIn usando un utente di test di nome **B.Simon**. Per il corretto funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in ExpenseIn.

Per configurare e testare l'accesso SSO di Azure AD con ExpenseIn, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** per consentire agli utenti di usare questa funzionalità.
2. **[Configurare ExpenseIn](#configure-expensein)** per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** per testare l'accesso Single Sign-On di Azure AD con B.Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** per consentire a B.Simon di usare l'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di ExpenseIn](#create-expensein-test-user)** per avere una controparte di B.Simon in ExpenseIn collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-sso)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **ExpenseIn** del [portale di Azure](https://portal.azure.com/) trovare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    Nella casella di testo **URL di risposta** digitare uno degli URL seguenti:

    | |
    |--|
    | `https://app.expensein.com/samlcallback` |
    | `https://mobileapi.expensein.com/identity/samlcallback` |

5. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL di accesso** digitare un URL: `https://app.expensein.com/saml`

1. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic sul pulsante Copia per copiare **l'URL dei metadati di federazione dell'app**, quindi fare clic su **Scarica** per scaricare il **Certificato (Base64)** e salvarlo nel computer in uso.

   ![Collegamento di download del certificato](./media/expensein-tutorial/copy-metdataurl-certificate.png)

1. Nella sezione **Configura ExpenseIn** copiare gli URL appropriati in base alle esigenze.

   ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="configure-expensein"></a>Configurare ExpenseIn

1. Per automatizzare la configurazione all'interno di ExpenseIn, è necessario installare l'**estensione de browser per l'accesso sicuro alle app personali** facendo clic su **Installa l'estensione**.

    ![Estensione My Apps](common/install-myappssecure-extension.png)

2. Dopo aver aggiunto l'estensione al browser, fare clic su **Configura ExpenseIn**. Si verrà indirizzati all'applicazione ExpenseIn. Specificare quindi le credenziali di amministratore per accedere a ExpenseIn. L'estensione del browser configurerà automaticamente l'applicazione e automatizzerà i passaggi da 3 a 5.

    ![Eseguire la configurazione](common/setup-sso.png)

3. Se si intende configurare ExpenseIn manualmente, aprire una nuova finestra del Web browser, accedere al sito aziendale di ExpenseIn come amministratore e seguire questa procedura:

4. Fare clic su **Admin** (Amministrazione) nella parte superiore della pagina, quindi passare a **Single Sign-On** e fare clic su **Add provider** (Aggiungi provider).

     ![Configurazione di ExpenseIn](./media/expenseIn-tutorial/config01.png)

5. Nella finestra popup **New Identity Provider** (Nuovo provider di identità) seguire questa procedura:

    ![Configurazione di ExpenseIn](./media/expenseIn-tutorial/config02.png)

    a. Nella casella di testo **Provider Name** (Nome provider) digitare il nome, ad esempio Azure.

    b. Selezionare **Yes** (Sì) per **Allow Provider Initiated Sign-On** (Consenti accesso avviato da provider).

    c. Nella casella di testo **Target Url** (URL di destinazione) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    d. Nella casella di testo **Issuer** (Autorità di certificazione) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    e. Aprire il certificato (Base64) nel Blocco note, copiarne il contenuto e incollarlo nella casella di testo **Certificate** (Certificato).

    f. Fare clic su **Create**(Crea).

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a ExpenseIn.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco di applicazioni selezionare **ExpenseIn**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-expensein-test-user"></a>Creare l'utente di test di ExpenseIn

Per consentire agli utenti di Azure AD di accedere a ExpenseIn, è necessario effettuarne il provisioning in ExpenseIn. Nel caso di ExpenseIn il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere a ExpenseIn come amministratore.

2. Fare clic su **Admin** (Amministrazione) nella parte superiore della pagina, quindi passare a **Users** (Utenti) e fare clic su **New User** (Nuovo utente).

     ![Configurazione di ExpenseIn](./media/expenseIn-tutorial/config03.png)

3. Nella pagina **Details** (Dettagli) completare i passaggi seguenti:

    ![Configurazione di ExpenseIn](./media/expenseIn-tutorial/config04.png)

    a. Nella casella di testo **First name** (Nome) immettere il nome dell'utente, ad esempio **B**.

    b. Nella casella di testo **Last Name** (Nome) immettere il cognome dell'utente, ad esempio **Simon**.

    c. Nella casella di testo **E-mail** (Posta elettronica) immettere l'indirizzo di posta elettronica dell'utente, ad esempio `B.Simon@contoso.com`.

    d. Fare clic su **Create**(Crea).

### <a name="test-sso"></a>Testare l'accesso SSO

Quando si fa clic sul riquadro di ExpenseIn nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione ExpenseIn per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
