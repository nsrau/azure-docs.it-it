---
title: 'Esercitazione: Integrazione di Azure Active Directory con Syncplicity | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Syncplicity.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 896a3211-f368-46d7-95b8-e4768c23be08
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.openlocfilehash: 663958ae367162eaeb336c819d1d219dc74a2cbe
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233275"
---
# <a name="tutorial-integrate-syncplicity-with-azure-active-directory"></a>Esercitazione: Integrare Syncplicity con Azure Active Directory

Questa esercitazione descrive come integrare Syncplicity con Azure Active Directory (Azure AD). Integrando Syncplicity con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Syncplicity.
* Abilitare gli utenti per l'accesso automatico a Syncplicity con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere una versione di valutazione gratuita per un mese [qui](https://azure.microsoft.com/pricing/free-trial/).
* Sottoscrizione di Syncplicity abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Syncplicity supporta l'accesso SSO avviato da **SP**.

## <a name="adding-syncplicity-from-the-gallery"></a>Aggiunta di Syncplicity dalla raccolta

Per configurare l'integrazione di Syncplicity in Azure AD, è necessario aggiungere Syncplicity dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Syncplicity** nella casella di ricerca.
1. Selezionare **Syncplicity** nel riquadro dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso"></a>Configurare e testare l'accesso SSO di Azure AD

Configurare e testare l'accesso SSO di Azure AD con Syncplicity usando un utente di test di nome **B. Simon**. Per il corretto funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Syncplicity.

Per configurare e testare l'accesso SSO di Azure AD con Syncplicity, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Syncplicity](#configure-syncplicity-sso)** : per configurare le impostazioni dell'accesso Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Syncplicity](#create-syncplicity-test-user)** : per avere una controparte di B. Simon in Syncplicity collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Syncplicity** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella pagina **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<companyname>.syncplicity.com`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `https://<companyname>.syncplicity.com/sp`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Syncplicity](https://www.syncplicity.com/contact-us). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML**, trovare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer in uso.

   ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura Syncplicity** copiare gli URL appropriati in base alle esigenze.

   ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="configure-syncplicity-sso"></a>Configurare l'accesso Single Sign-On per Syncplicity

1. Accedere al tenant **Syncplicity**.

1. Nel menu nella parte superiore fare clic su **admin** (amministrazione), selezionare **settings** (impostazioni) e quindi fare clic su **Custom domain and single sign-on** (Dominio personalizzato e Single Sign-On).

    ![Syncplicity](./media/syncplicity-tutorial/ic769545.png "Syncplicity")

1. Nella pagina finestra di dialogo **Single Sign-On (SSO)** , eseguire la procedura seguente:

    ![Accesso Single Sign-On \(SSO\)](./media/syncplicity-tutorial/ic769550.png "Single Sign-On \\\(SSO\\\)")

    a. Nella casella di testo **Custom Domain** , digitare il nome del dominio.
  
    b. Selezionare **Enabled** (Abilitato) come **Single Sign-On Status** (Stato Single Sign-On).

    c. Nella casella di testo **Entity Id** (ID entità) incollare il valore di **Identificatore (ID entità)** usato nella sezione **Configurazione SAML di base** del portale di Azure.

    d. Nella casella di testo **Sign-in page URL** (URL pagina di accesso) incollare l'**URL di accesso** copiato dal portale di Azure.

    e. Nella casella di testo **Logout page URL** (URL pagina di disconnessione) incollare l'**URL di disconnessione** copiato dal portale di Azure.

    f. In **Identity Provider Certificate** (Certificato provider di identità) fare clic su **Choose file** (Scegli file) e quindi caricare il certificato scaricato dal portale di Azure.

    g. Fare clic su **SAVE CHANGES** (Salva modifiche).

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

In questa sezione si abiliterà B. Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Syncplicity.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Syncplicity**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-syncplicity-test-user"></a>Creare l'utente di test di Syncplicity

Per consentire agli utenti di Azure AD di accedere, è necessario effettuarne il provisioning nell'applicazione Syncplicity. Questa sezione descrive come creare account utente Azure AD in Syncplicity.

**Per eseguire il provisioning di un account utente a Syncplicity, eseguire la procedura seguente:**

1. Accedere al tenant **Syncplicity** (ad esempio: `https://company.Syncplicity.com`).

1. Fare clic su **admin** (amministrazione) e selezionare **user accounts** (account utente), quindi fare clic su **ADD A USER** (AGGIUNGI UN UTENTE).

    ![Gestire gli utenti](./media/syncplicity-tutorial/ic769764.png "Manage Users")

1. Digitare gli **indirizzi di posta elettronica** di un account Azure AD di cui effettuare il provisioning, selezionare **User** (Utente) come valore di **Role** (Ruolo) e quindi fare clic su **NEXT** (AVANTI).

    ![Informazioni sull'account](./media/syncplicity-tutorial/ic769765.png "Informazioni account")

    > [!NOTE]
    > Il titolare dell'account Azure AD riceve un messaggio di posta elettronica contenente un collegamento per confermare e attivare l'account.

1. Selezionare un gruppo nell'azienda a cui aggiungere il nuovo utente e quindi fare clic su **NEXT** (Avanti).

    ![Appartenenza a gruppi](./media/syncplicity-tutorial/ic769772.png "Appartenenza al gruppo")

    > [!NOTE]
    > Se non sono elencati gruppi, fare clic su **NEXT** (Avanti).

1. Selezionare le cartelle a cui si vuole applicare il controllo di Syncplicity nel computer dell'utente e quindi fare clic su **NEXT** (Avanti).

    ![Cartelle Syncplicity](./media/syncplicity-tutorial/ic769773.png "Cartella Syncplicity")

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornito da Syncplicity per effettuare il provisioning degli account utente Azure AD.

### <a name="test-sso"></a>Testare l'accesso SSO

Quando si seleziona il riquadro di Syncplicity nel Pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Syncplicity per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)