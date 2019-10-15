---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Velpic SAML | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Velpic SAML.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f699e2244cde53200a9885602bd20c899c0ce4cd
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241551"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-velpic-saml"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Velpic SAML

Questa esercitazione descrive come integrare Velpic SAML con Azure Active Directory (Azure AD). Integrando Velpic SAML con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Velpic SAML.
* Abilitare gli utenti per l'accesso automatico a Velpic SAML con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Velpic SAML abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Velpic SAML supporta l'accesso SSO avviato da **SP**

## <a name="adding-velpic-saml-from-the-gallery"></a>Aggiungere Velpic SAML dalla raccolta

Per configurare l'integrazione di Velpic SAML in Azure AD, è necessario aggiungere Velpic SAML dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Velpic SAML** nella casella di ricerca.
1. Selezionare **Velpic SAML** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.    

## <a name="configure-and-test-azure-ad-single-sign-on-for-velpic-saml"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Velpic SAML

Configurare e testare l'accesso SSO di Azure AD con Velpic SAML usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Velpic SAML.

Per configurare e testare l'accesso SSO di Azure AD con Velpic SAML, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Velpic SAML](#configure-velpic-saml-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Velpic SAML](#create-velpic-saml-test-user)** : per avere una controparte di B.Simon in Velpic SAML collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Velpic SAML** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<sub-domain>.velpicsaml.net`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `https://auth.velpic.com/saml/v2/<entity-id>/login`

    > [!NOTE]
    > Si noti che l'URL di Single Sign-On verrà indicato dal team di Velpic SAML e il valore dell'identificatore sarà disponibile quando si configura il plug-in SSO sul lato Velpic SAML. È necessario copiare tale valore dalla pagina dell'applicazione Velpic SAML e incollarlo qui.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il file **XML dei metadati della federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura Velpic SAML** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Velpic SAML.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco di applicazioni selezionare **Velpic SAML**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-velpic-saml-sso"></a>Configurare l'accesso Single Sign-On di Velpic SAML

1. Per automatizzare la configurazione all'interno di Velpic SAML, è necessario installare l'**estensione del browser per l'accesso sicuro ad App personali** facendo clic su **Installa l'estensione**.

    ![Estensione per App personali](common/install-myappssecure-extension.png)

2. Dopo aver aggiunto l'estensione al browser, fare clic su **Configura Velpic SAML** per passare direttamente all'applicazione Velpic SAML. Specificare quindi le credenziali di amministratore per accedere a Velpic SAML. L'estensione del browser configurerà automaticamente l'applicazione e automatizzerà i passaggi da 3 a 8.

    ![Eseguire la configurazione](common/setup-sso.png)

3. Se si vuole configurare manualmente Velpic SAML, aprire una nuova finestra del Web browser, accedere al sito aziendale di Velpic SAML come amministratore e seguire questa procedura:

4. Fare clic sulla scheda **Manage** (Gestisci), passare alla sezione **Integration** (Integrazione) e qui scegliere **Plugins** per creare un nuovo plug-in per l'accesso.

    ![Plug-in](./media/velpicsaml-tutorial/velpic_1.png)

5. Fare clic sul pulsante **Add plugin** (Aggiungi plug-in).
    
    ![Plug-in](./media/velpicsaml-tutorial/velpic_2.png)

6. Fare clic sul riquadro **SAML** nella pagina Add plugin (Aggiungi plug-in).
    
    ![Plug-in](./media/velpicsaml-tutorial/velpic_3.png)

7. Immettere il nome del nuovo plug-in SAML e scegliere il pulsante **Add** (Aggiungi).

    ![Plug-in](./media/velpicsaml-tutorial/velpic_4.png)

8. Immettere i dettagli seguenti:

    ![Plug-in](./media/velpicsaml-tutorial/velpic_5.png)

    a. Nella casella di testo **Name** (Nome) digitare il nome del plug-in SAML.

    b. Nella casella di testo **Issuer URL** (URL autorità di certificazione) incollare il valore di **Identificatore Azure AD** copiato dalla finestra **Configura accesso** del portale di Azure.

    c. Nel riquadro della **configurazione dei metadati del provider** caricare il file XML dei metadati scaricato dal portale di Azure.

    d. È anche possibile scegliere di abilitare SAML al momento del provisioning selezionando la casella di controllo **Auto create new users** (Crea automaticamente nuovi utenti). Se un utente non esiste in Velpic e questo flag non è abilitato, l'accesso da Azure avrà esito negativo. Se il flag è abilitato, verrà automaticamente eseguito il provisioning dell'utente in Velpic al momento dell'accesso. 

    e. Copiare l'**URL di Single Sign-On** dalla casella di testo e incollarlo nel portale di Azure.
    
    f. Fare clic su **Save**.

### <a name="create-velpic-saml-test-user"></a>Creare l'utente di test di Velpic SAML

Questo passaggio in genere non è necessario poiché l'applicazione supporta il provisioning degli utenti just-in-time. Se il provisioning automatico degli utenti non è abilitato, la creazione manuale dell'utente può essere eseguita come descritto di seguito.

Accedere al sito aziendale di Velpic SAML come amministratore e seguire questa procedura:
    
1. Fare clic sulla scheda Manage (Gestisci) e passare alla sezione Users (Utenti), quindi fare clic sul pulsante New (Nuovo) per aggiungere gli utenti.

    ![Aggiungi utente](./media/velpicsaml-tutorial/velpic_7.png)

2. Nella finestra di dialogo **Create New User** (Crea nuovo utente) effettuare le operazioni seguenti.

    ![user](./media/velpicsaml-tutorial/velpic_8.png)
    
    a. Nella casella di testo **First Name** (Nome) digitare il nome B.

    b. Nella casella di testo **Last Name** (Cognome) digitare il cognome Simon.

    c. Nella casella di testo **User Name** (Nome utente) digitare il nome utente B.Simon.

    d. Nella casella di testo **Email** (Indirizzo di posta elettronica) digitare l'indirizzo di posta elettronica dell'account B.Simon@contoso.com.

    e. Le informazioni rimanenti sono facoltative, se necessario si possono compilare.
    
    f. Fare clic su **SAVE**.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

1. Quando si fa clic sul riquadro Velpic SAML nel Pannello di accesso, viene visualizzata la pagina di accesso dell'applicazione Velpic SAML, in cui dovrebbe apparire il pulsante **Log In With Azure AD** (Accedi con Azure AD).

    ![Plug-in](./media/velpicsaml-tutorial/velpic_6.png)

1. Fare clic sul pulsante **Log In With Azure AD** (Accedi con Azure AD) per accedere a Velpic usando il proprio account Azure AD.

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare Velpic SAML con Azure AD](https://aad.portal.azure.com/)

