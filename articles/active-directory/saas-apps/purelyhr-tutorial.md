---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con PurelyHR | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e PurelyHR.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 86a9c454-596d-4902-829a-fe126708f739
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56049e1b1253cd749a8e16061957c6b5b8786e3c
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72594523"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-purelyhr"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con PurelyHR

Questa esercitazione descrive come integrare PurelyHR con Azure Active Directory (Azure AD). Integrando PurelyHR con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a PurelyHR.
* Abilitare gli utenti per l'accesso automatico a PurelyHR con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di PurelyHR abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* PurelyHR supporta l'accesso SSO avviato da **SP e IDP**
* PurelyHR supporta il provisioning utenti **JIT**

## <a name="adding-purelyhr-from-the-gallery"></a>Aggiunta di PurelyHR dalla raccolta

Per configurare l'integrazione di PurelyHR in Azure AD, è necessario aggiungere PurelyHR dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **PurelyHR** nella casella di ricerca.
1. Selezionare **PurelyHR** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-purelyhr"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per PurelyHR

Configurare e testare l'accesso SSO di Azure AD con PurelyHR usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in PurelyHR.

Per configurare e testare l'accesso SSO di Azure AD con PurelyHR, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    * **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    * **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di PurelyHR](#configure-purelyhr-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    * **[Creare l'utente di test di PurelyHR](#create-purelyhr-test-user)** : per avere una controparte di B.Simon in PurelyHR collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **PurelyHR** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<companyID>.purelyhr.com/sso-consume`

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<companyID>.purelyhr.com/sso-initiate`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di PurelyHR](https://support.purelyhr.com/). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura PurelyHR** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a PurelyHR.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **PurelyHR**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-purelyhr-sso"></a>Configurare l'accesso Single Sign-On di PurelyHR

1. Per automatizzare la configurazione all'interno di PurelyHR, è necessario installare l'**estensione del browser per l'accesso sicuro ad App personali** facendo clic su **Installa l'estensione**.

    ![Estensione per App personali](common/install-myappssecure-extension.png)

1. Dopo aver aggiunto l'estensione al browser, fare clic su **Configura PurelyHR** per passare direttamente all'applicazione PurelyHR. Nell'applicazione specificare le credenziali di amministratore per accedere a PurelyHR. L'estensione del browser configurerà automaticamente l'applicazione e automatizzerà i passaggi da 3 a 5.

    ![Eseguire la configurazione](common/setup-sso.png)

1. Se si vuole configurare manualmente PurelyHR, aprire una nuova finestra del Web browser, accedere al sito aziendale di PurelyHR come amministratore e completare i passaggi seguenti:

1. Aprire il **dashboard** dalle opzioni nella barra degli strumenti e fare clic su **SSO Settings** (Impostazioni SSO).

1. Incollare i valori nelle caselle come descritto di seguito.

    ![Configure Single Sign-On](./media/purelyhr-tutorial/purelyhr-dashboard-sso-settings.png)  

    a. Aprire il certificato **Certificate(Bas64)** scaricato dal portale di Azure nel blocco note e copiare il valore del certificato. Incollare il valore copiato nella casella **x509 Certificate** (Certificato x509).

    b. Nella casella **Idp Issuer URL** (URL emittente IdP) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    c. Nella casella **Idp Endpoint URL** (URL endpoint IdP) incollare il valore di **URL di accesso** copiato dal portale di Azure. 

    d. Selezionare la casella di controllo **Auto-Create Users** (Creazione automatica utenti) per abilitare il provisioning utenti automatico in PurelyHR.

    e. Fare clic su **Salva** per salvare le impostazioni.

### <a name="create-purelyhr-test-user"></a>Creare l'utente di test di PurelyHR

Questo passaggio in genere non è necessario poiché l'applicazione supporta il provisioning utenti JIT. Se il provisioning automatico degli utenti non è abilitato, la creazione manuale dell'utente può essere eseguita come descritto di seguito.

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

Quando si fa clic sul riquadro di PurelyHR nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di PurelyHR per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare PurelyHR con Azure AD](https://aad.portal.azure.com/)
