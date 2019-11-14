---
title: 'Esercitazione: Integrazione di Azure Active Directory con Sage Intacct | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Sage Intacct.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 92518e02-a62c-4b1b-a8e9-2803eb2b49ac
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b28390dba009226d493f5bfc6a5270b067f5bba0
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "73570532"
---
# <a name="tutorial-integrate-sage-intacct-with-azure-active-directory"></a>Esercitazione: Integrare Sage Intacct con Azure Active Directory

Questa esercitazione descrive come integrare Sage Intacct con Azure Active Directory (Azure AD). Integrando Sage Intacct con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Sage Intacct.
* Abilitare gli utenti per l'accesso automatico a Sage Intacct con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Sage Intacct abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Sage Intacct supporta l'accesso SSO avviato da **IDP**

## <a name="adding-sage-intacct-from-the-gallery"></a>Aggiunta di Sage Intacct dalla raccolta

Per configurare l'integrazione di Sage Intacct in Azure AD, è necessario aggiungere Sage Intacct dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Sage Intacct** nella casella di ricerca.
1. Selezionare **Sage Intacct** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sage-intacct"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Sage Intacct

Configurare e testare l'accesso SSO di Azure AD con Sage Intacct usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Sage Intacct.

Per configurare e testare l'accesso SSO di Azure AD con Sage Intacct, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
2. **[Configurare l'accesso SSO di Sage Intacct](#configure-sage-intacct-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Sage Intacct](#create-sage-intacct-test-user)** : per avere una controparte di B.Simon in Sage Intacct collegata alla relativa rappresentazione in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Sage Intacct** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    Nella casella di testo **URL di risposta** digitare un URL: `https://www.intacct.com/ia/acct/sso_response.phtml`

1. L'applicazione Sage Intacct prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti. Fare clic su **Modifica** per aprire la finestra di dialogo Attributi utente.

    ![image](common/edit-attribute.png)

1. Oltre a quelli elencati in precedenza, l'applicazione Sage Intacct prevede il passaggio di qualche altro attributo nella risposta SAML. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** eseguire la procedura seguente per aggiungere l'attributo del token SAML come illustrato nella tabella seguente:

    | Nome  |  Attributo di origine|
    | ---------------| --------------- |
    | Nome dell'azienda | **ID società di Sage Intacct** |
    | name | Il valore deve corrispondere a quello di **User ID** (ID utente) di Sage Intacct, che viene immesso nella sezione **Creare l'utente di test di Sage Intacct**, descritta più avanti nell'esercitazione. |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier | Il valore deve corrispondere a quello di **Federated SSO User ID** (ID utente SSO federato) di Sage Intacct, che viene immesso nella sezione **Creare l'utente di test di Sage Intacct**, descritta più avanti nell'esercitazione. |

    a. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Lasciare vuota la casella **Spazio dei nomi**.

    d. Per Origine selezionare **Attributo**.

    e. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo indicato per la riga.

    f. Fare clic su **OK**.

    g. Fare clic su **Save**.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura Sage Intacct** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Sage Intacct.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Sage Intacct**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-sage-intacct-sso"></a>Configurare l'accesso SSO di Sage Intacct

1. In un'altra finestra del Web browser accedere al sito aziendale di Sage Intacct come amministratore.

1. Fare clic sulla scheda **Company** (Azienda) e quindi su **Company Info** (Informazioni sull'azienda).

    ![Azienda](./media/intacct-tutorial/ic790037.png "Azienda")

1. Fare clic sulla scheda **Security** (Sicurezza) e quindi su **Edit** (Modifica).

    ![Sicurezza](./media/intacct-tutorial/ic790038.png "Sicurezza")

1. Nella sezione **Single sign on (SSO)** seguire questa procedura:

    ![Single sign on](./media/intacct-tutorial/ic790039.png "single sign on")

    a. Selezionare **Abilita Single Sign-On**.

    b. In **Identity provider type** (Tipo di provider di identità) selezionare **SAML 2.0**.

    c. Nella casella di testo **Issuer URL** (URL autorità di certificazione) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    d. Nella casella di testo **URL di accesso** incollare il valore di **URL di accesso** copiato dal portale di Azure.

    e. Aprire il certificato con codifica **Base 64** nel Blocco note, copiarne il contenuto negli Appunti e incollarlo nella casella **Certificato**.

    f. Fare clic su **Save**.

### <a name="create-sage-intacct-test-user"></a>Creare l'utente di test di Sage Intacct

Per consentire agli utenti di Azure AD di accedere a Sage Intacct, è necessario effettuarne il provisioning in Sage Intacct. In Sage Intacct il provisioning è un'attività manuale.

**Per eseguire il provisioning degli account utente, seguire questa procedura:**

1. Accedere al tenant di **Sage Intacct**.

1. Fare clic sulla scheda **Company** (Azienda) e quindi su **Users** (Utenti).

    ![Utenti](./media/intacct-tutorial/ic790041.png "Utenti")

1. Fare clic sulla scheda **Add** (Aggiungi).

    ![Aggiungi](./media/intacct-tutorial/ic790042.png "Aggiungi")

1. Nella sezione **Informazioni utente** seguire questa procedura:

    ![Informazioni utente](./media/intacct-tutorial/ic790043.png "Informazioni utente")

    a. Nelle caselle di testo **User ID**, **Last name**, **First name**, **Email address**, **Title** e **Phone** immettere l'ID utente, il cognome, il nome, l'indirizzo di posta elettronica, la posizione e il numero di telefono di un account Azure AD di cui si vuole eseguire il provisioning nella sezione **Informazioni utente**.

    > [!NOTE]
    > Assicurarsi che il valore di **User ID** (ID utente) nello screenshot precedente e il valore di **Source Attribute** (Attributo di origine), di cui è stato eseguito il mapping all'attributo **name** nella sezione **Attributi utente** del portale di Azure, siano identici.

    b. Selezionare i **privilegi di amministratore** di un account Azure AD di cui si vuole eseguire il provisioning.

    c. Fare clic su **Save**. 
    
    d. Il titolare dell'account Azure AD riceve un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.

1. Fare clic sulla scheda **Single sign-on** e assicurarsi che il campo **Federated SSO user ID** (ID utente SSO federato) nello screenshot seguente e il valore **Source Attribute** (Attributo di origine), di cui è stato eseguito il mapping a `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` nella sezione **Attributi utente** del portale di Azure, siano identici.

    ![Informazioni utente](./media/intacct-tutorial/ic790044.png "Informazioni utente")

> [!NOTE]
> Per effettuare il provisioning degli account utente di Azure AD, è possibile usare altri strumenti o API per la creazione di account utente Intacct forniti da Sage Intacct.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Sage Intacct nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Sage Intacct per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

