---
title: 'Esercitazione: integrazione di Azure Active Directory con Way We Do | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Way We Do.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 84fc4f36-ecd1-42c6-8a70-cb0f3dc15655
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: faa23f61e5a213c492a7fb51bfc5b108e5c77946
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/21/2019
ms.locfileid: "67310395"
---
# <a name="tutorial-integrate-way-we-do-with-azure-active-directory"></a>Esercitazione: Integrare Way We Do con Azure Active Directory

Questa esercitazione descrive come integrare Way We Do con Azure Active Directory (Azure AD). Integrando Way We Do con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Way We Do.
* Abilitare gli utenti per l'accesso automatico a Way We Do con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere una versione di valutazione gratuita per un mese [qui](https://azure.microsoft.com/pricing/free-trial/).
* Sottoscrizione di Way We Do abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Way We Do supporta l'accesso SSO avviato da **SP**
* Way We Do supporta il provisioning utenti **JIT**

## <a name="adding-way-we-do-from-the-gallery"></a>Aggiunta di Way We Do dalla raccolta

Per configurare l'integrazione di Way We Do in Azure AD, è necessario aggiungere Way We Do dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Way We Do** nella casella di ricerca.
1. Selezionare **Way We Do** nel riquadro dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

Configurare e testare l'accesso SSO di Azure AD con Way We Do usando un utente di test di nome **B. Simon**. Per il corretto funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Way We Do.

Per configurare e testare l'accesso SSO di Azure AD con Way We Do, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Way We Do](#configure-way-we-do-sso)** : per configurare le impostazioni dell'accesso Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di Way We Do](#create-way-we-do-test-user)** : per avere una controparte di Britta Simon in Way We Do collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Way We Do** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella pagina **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<SUBDOMAIN>.waywedo.com/Authentication/ExternalSignIn`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL usando il modello seguente: `https://<SUBDOMAIN>.waywedo.com`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi. Per ottenere questi valori contattare il [team di supporto clienti di Way We Do](mailto:support@waywedo.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** trovare **Certificato (base)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer in uso.

   ![Collegamento di download del certificato](common/certificateraw.png)

1. Nella sezione **Configura Way We Do** copiare gli URL appropriati in base alle esigenze.

   ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="configure-way-we-do-sso"></a>Configurare l'accesso Single Sign-On per Way We Do

1. Per automatizzare la configurazione all'interno di Way We Do, è necessario installare l'**estensione del browser per l'accesso sicuro alle app personali** facendo clic su **Installa l'estensione**.

    ![Estensione My Apps](common/install-myappssecure-extension.png)

1. Dopo aver aggiunto l'estensione al browser, fare clic su **Configura Way We Do** per passare direttamente all'applicazione Way We Do. Nell'applicazione specificare le credenziali di amministratore per accedere a Way We Do. L'estensione del browser configurerà automaticamente l'applicazione e automatizzerà i passaggi da 3 a 6.

    ![Eseguire la configurazione](common/setup-sso.png)

1. Se si vuole configurare manualmente Way We Do, aprire una nuova finestra del Web browser, accedere al sito aziendale di Way We Do come amministratore e seguire questa procedura:

1. Fare clic sull'**icona della persona** nell'angolo in alto a destra di qualsiasi pagina in Way We Do e quindi fare clic su **Account** nel menu a discesa.

    ![Account Way We Do](./media/waywedo-tutorial/tutorial_waywedo_account.png)

1. Fare clic sull'**icona del menu** per aprire il menu di navigazione e fare clic su **Single Sign On**.

    ![Accesso Single Sign-On Way We Do](./media/waywedo-tutorial/tutorial_waywedo_single.png)

1. Nella pagina **Single sign-on setup** (Configurazione Single Sign-On) seguire questa procedura:

    ![Salvataggio Way We Do](./media/waywedo-tutorial/tutorial_waywedo_save.png)

    a. Impostare **Turn on single sign-on** (Attiva Single Sign-On) su **Yes** (Sì) per abilitare l'accesso Single Sign-On.

    b. Nella casella di testo **Single sign-on name** (Nome Single Sign-On) immettere il proprio nome.

    c. Nella casella di testo **Entity ID** (ID entità) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    d. Nella casella di testo **SAML SSO URL** (URL SSO SAML) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    e. Caricare il certificato facendo clic sul pulsante **Select** (Seleziona) accanto a **Certificate** (Certificato).

    f. **Impostazioni facoltative** -
    
    * Enable Passwords (Abilita le password): quando questa opzione è disabilitata, la normale password per Way We Do consente solo l'accesso Single Sign-on degli utenti.

    * Enable Auto-provisioning (Abilita il provisioning automatico): quando questa opzione è abilitata, l'indirizzo di posta elettronica usato per l'accesso viene confrontato automaticamente con l'elenco degli utenti in Way We Do. Se l'indirizzo di posta elettronica non corrisponde a un utente attivo in Way We Do, aggiunge automaticamente un nuovo account utente per la persona che esegue l'accesso, chiedendo eventuali informazioni mancanti.

      > [!NOTE]
      > Gli utenti aggiunti tramite Single Sign-On vengono aggiunti come utenti generici e non hanno un ruolo assegnato nel sistema. Un amministratore può accedere e modificare il ruolo di sicurezza degli utenti in qualità di editor o amministratore e può anche assegnare uno o più ruoli dell'organigramma.

    g. Per salvare le impostazioni, fare clic su **Save** (Salva).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

In questa sezione verrà creato un utente di test di nome B. Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abiliterà B. Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Way We Do.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Way We Do**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B. Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-way-we-do-test-user"></a>Creare l'utente di test di Way We Do

In questa sezione viene creato un utente di nome Britta Simon in Way We Do. Way We Do supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in Way We Do, ne viene creato uno nuovo dopo l'autenticazione.

> [!Note]
> Per creare un utente manualmente, contattare il [team del supporto clienti di Way We Do](mailto:support@waywedo.com).

### <a name="test-sso"></a>Testare l'accesso SSO

Quando si seleziona il riquadro di Way We Do nel Pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Way We Do per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)