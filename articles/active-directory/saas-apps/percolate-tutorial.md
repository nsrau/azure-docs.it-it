---
title: 'Esercitazione: Integrazione di Azure Active Directory con Percolate | Microsoft Docs'
description: In questa esercitazione si apprenderà come configurare l'accesso Single Sign-On tra Azure Active Directory e Percolate.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 355f9659-b378-44c9-aa88-236e9b529a53
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: 83027e9fbc1826de727f123afe4507c2858c49ff
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/13/2019
ms.locfileid: "65560558"
---
# <a name="tutorial-azure-active-directory-integration-with-percolate"></a>Esercitazione: Integrazione di Azure Active Directory con Percolate

Questa esercitazione descrive come integrare Percolate con Azure Active Directory (Azure AD).

Questa integrazione offre i seguenti vantaggi:

* È possibile usare Azure AD per controllare chi può accedere a Percolate.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Percolate con gli account Azure AD personali.
* È possibile gestire gli account da una posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Percolate, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Percolate abilitata per l'accesso Single Sign-On.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione verranno eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Percolate supporta l'accesso SSO avviato da SP e IdP.

## <a name="add-percolate-from-the-gallery"></a>Aggiungere Percolate dalla raccolta

Per configurare l'integrazione di Percolate in Azure AD, è necessario aggiungere Percolate dalla raccolta all'elenco di app SaaS gestite.

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Azure Active Directory** nel riquadro sinistro:

    ![Selezionare Azure Active Directory](common/select-azuread.png)

2. Passare a **Applicazioni aziendali** > **Tutte le applicazioni**:

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere un'applicazione, selezionare **Nuova applicazione** nella parte superiore della finestra:

    ![Selezionare Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Percolate**. Selezionare **Percolate** nei risultati della ricerca e quindi selezionare **Aggiungi**.

     ![Search Results](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione verrà configurato e testato l'accesso Single Sign-On di Azure AD con Percolate usando un utente di test di nome Britta Simon.
Per abilitare l'accesso Single Sign-On, è necessario stabilire una relazione tra un utente di Azure AD e l'utente corrispondente in Percolate.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Percolate, è necessario completare questi passaggi:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** per abilitare questa funzionalità per gli utenti.
2. **[Configurare l'accesso Single Sign-On di Percolate](#configure-percolate-single-sign-on)** sul lato dell'applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** per testare l'accesso Single Sign-On di Azure AD.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** per abilitare l'accesso Single Sign-On di Azure AD per tale utente.
5. **[Creare un utente di test di Percolate](#create-a-percolate-test-user)** collegato alla rappresentazione di Azure AD dell'utente.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione verrà abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Percolate, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Percolate** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**:

    ![Selezionare Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare l'accesso Single Sign-On:

    ![Selezionare un metodo di accesso Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** selezionare l'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**:

    ![Icona Modifica](common/edit-urls.png)

4. Nella finestra di dialogo **Configurazione SAML di base** non è necessario eseguire nessuna operazione per configurare l'applicazione nella modalità avviata da IdP. L'applicazione è già integrata con Azure.

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Percolate](common/preintegrated.png)

5. Se si desidera configurare l'applicazione in modalità avviata da SP, selezionare **Impostare URL aggiuntivi** e nel riquadro **URL di accesso** immettere **https://percolate.com/app/login** :

   ![Informazioni su URL e dominio per l'accesso Single Sign-On di Percolate](common/metadata-upload-additional-signon.png)
6. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** selezionare l'icona **Copia** per copiare l'**URL dei metadati di federazione dell'app**. Salvare questo URL.

    ![Copiare l'URL dei metadati di federazione dell'app](common/copy-metadataurl.png)

7. Nella sezione **Configura Percolate** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    1. **URL di accesso**.

    1. **Identificatore Azure AD**.

    1. **URL di disconnessione**.

### <a name="configure-percolate-single-sign-on"></a>Configurare l'accesso Single Sign-On di Percolate

1. In una nuova finestra del Web browser accedere a Percolate come amministratore.

2. Sul lato sinistro della home page selezionare **Settings** (Impostazioni):
    
    ![Selezionare Impostazioni](./media/percolate-tutorial/configure01.png)

3. Nel riquadro sinistro selezionare **SSO** sotto **Organization** (Organizzazione):

    ![Selezionare SSO sotto Organizzazione](./media/percolate-tutorial/configure02.png)

    1. Nella casella **Login URL** (URL di accesso) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    1. Nella casella **Entity ID** (ID entità) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    1. Aprire nel Blocco note il certificato con codifica base 64 scaricato dal portale di Azure. Copiarne il contenuto e incollarlo nella casella **x509 certificates** (Certificati x509).

    1. Nella casella di testo **Email attribute** (Attributo posta elettronica) immettere **emailaddress**.

    1. La casella **Identity provider metadata URL** (URL metadati provider di identità) è un campo facoltativo. Se è stato copiato un **URL dei metadati di federazione dell'app** dal portale di Azure, è possibile incollarlo in questa casella.

    1. Rispondere **No** a **Should AuthNRequests be signed?** (Le N richieste di autorizzazione devono essere firmate?).

    1. Selezionare **No** per **Enable SSO auto-Provisioning** (Abilita provisioning automatico SSO).

    1. Selezionare **Salva**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

In questa sezione verrà creato un utente di test di nome Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**:

    ![Selezionare Tutti gli utenti](common/users.png)

2. Selezionare **Nuovo utente** nella parte superiore della schermata:

    ![Selezionare Nuovo utente](common/new-user.png)

3. Nella finestra di dialogo **Utente** seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    1. Nella casella **Nome** immettere **BrittaSimon**.
  
    1. Nella casella **Nome utente** immettere **BrittaSimon@\<dominioaziendale>.\<estensione>** , ad esempio BrittaSimon@contoso.com.

    1. Selezionare **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    1. Selezionare **Create**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure AD, concedendole così accesso a Percolate.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Percolate**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Percolate**.

    ![Elenco delle applicazioni](common/all-applications.png)

3. Nel riquadro sinistro selezionare **Utenti e gruppi**:

    ![Selezionare Utenti e gruppi](common/users-groups-blade.png)

4. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Selezionare Utenti e gruppi](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco di utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco. Fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Assegna**.

### <a name="create-a-percolate-test-user"></a>Creare un utente di test di Percolate

Per consentire agli utenti di Azure AD di accedere a Percolate, aggiungerli a Percolate. È necessario aggiungerli manualmente.

Per creare un account utente, seguire questi passaggi:

1. Accedere a Percolate come amministratore.

2. Nel riquadro sinistro selezionare **Users** (Utenti) sotto **Organization** (Organizzazione). Selezionare **New users** (Nuovi utenti):

    ![Selezionare Nuovi utenti](./media/percolate-tutorial/configure03.png)

3. Nella pagina **Create users** (Crea utenti) seguire la procedura seguente.

    ![Pagina di creazione utenti](./media/percolate-tutorial/configure04.png)

    1. Nella casella **Email** immettere l'indirizzo di posta elettronica dell'utente. Ad esempio: brittasimon@contoso.com.

    1. Nella casella **Full name** (Nome e cognome) immettere il nome dell'utente, ad esempio: **Brittasimon**.

    1. Selezionare **Create users** (Crea utenti).

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

Il passaggio finale consiste nel testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si seleziona il riquadro di Percolate nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza Percolate per cui si è configurato l'accesso SSO. Per altre informazioni, vedere [Accedere e usare le app nel portale App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Esercitazioni per l'integrazione di applicazioni SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)