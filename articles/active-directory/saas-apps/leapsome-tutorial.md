---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Leapsome | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Leapsome.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: jeedes
ms.openlocfilehash: c58c9febcbd8c1007938b16545337f07f1a0da63
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88519123"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-leapsome"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Leapsome

Questa esercitazione descrive come integrare Leapsome con Azure Active Directory (Azure AD). Integrando Leapsome con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Leapsome.
* Abilitare gli utenti per l'accesso automatico a Leapsome con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Leapsome abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Leapsome supporta l'accesso SSO avviato da **SP e IDP**

## <a name="adding-leapsome-from-the-gallery"></a>Aggiunta di Leapsome dalla raccolta

Per configurare l'integrazione di Leapsome in Azure AD, è necessario aggiungere l'app dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Leapsome** nella casella di ricerca.
1. Selezionare **Leapsome** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-leapsome"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Leapsome

Configurare e testare l'accesso SSO di Azure AD con Leapsome usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Leapsome.

Per configurare e testare l'accesso SSO di Azure AD con Leapsome, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    * **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    * **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Leapsome](#configure-leapsome-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    * **[Creare l'utente di test di Leapsome](#create-leapsome-test-user)** : per avere una controparte di B.Simon in Leapsome collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Leapsome** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Nella casella di testo **Identificatore** digitare un URL: `https://www.leapsome.com`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/assert`

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/login`

    > [!NOTE]
    > I precedenti valori di URL di risposta e URL di accesso non sono reali. È necessario sostituirli con i valori effettivi, come descritto più avanti nell'esercitazione.

1. L'applicazione Leapsome prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/default-attributes.png)

1. Oltre quelli elencati in precedenza, l'applicazione Leapsome prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.

    | Nome | Attributo di origine | Spazio dei nomi |
    | ---------------| --------------- | --------- |  
    | firstname | user.givenname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | lastname | user.surname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | title | user. jobtitle | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | picture | URL dell'immagine del dipendente | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | | |

    > [!Note]
    > Il valore dell'attributo picture non è reale. Aggiornare questo valore con l'URL dell'immagine effettivo. Per ottenere questo valore, contattare il [team di supporto clienti di Leapsome](mailto:support@leapsome.com).

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura Leapsome** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Leapsome.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco di applicazioni selezionare **Leapsome**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-leapsome-sso"></a>Configurare l'accesso Single Sign-On di Leapsome

1. In un'altra finestra del Web browser accedere a Leapsome come amministratore.

1. In alto a destra, fare clic sul logo Impostazioni e quindi fare clic su **Impostazioni di amministrazione**.

    ![Set Leapsome](./media/leapsome-tutorial/tutorial_leapsome_admin.png)

1. Nella barra dei menu a sinistra fare clic su **Single Sign On (SSO)** e nella pagina **Accesso Single Sign-On basato su SAML** seguire questa procedura:

    ![SAML Leapsome](./media/leapsome-tutorial/tutorial_leapsome_samlsettings.png)

    a. Selezionare **Enable SAML-based single sign-on** (Abilita accesso Single Sign-On basato su SAML).

    b. Copiare il valore di **Login URL (point your users here to start login)** (URL di accesso - indirizzare gli utenti qui per accedere) e incollarlo nella casella di testo **URL accesso** della sezione **Configurazione SAML di base** del portale di Azure.

    c. Copiare il valore di **Reply URL (receives response from your identity provider)** (URL di risposta -riceve la risposta dal provider di identità) e incollarlo nella casella di testo **URL di risposta** della sezione **Configurazione SAML di base** del portale di Azure.

    d. Nella casella di testo **URL di accesso (fornito dal provider di identità)** incollare il valore di **URL di accesso** copiato dal portale di Azure.

    e. Copiare il certificato che è stato scaricato dal portale di Azure senza i commenti `--BEGIN CERTIFICATE and END CERTIFICATE--` e incollarlo nella casella di testo **Certificato (fornito dal provider di identità)** .

    f. Fare clic su **UPDATE SSO SETTINGS** (AGGIORNA IMPOSTAZIONI SSO).

### <a name="create-leapsome-test-user"></a>Creare un utente di test di Leapsome

In questa sezione viene creato un utente di nome Britta Simon in Leapsome. Rivolgersi al [team di supporto di Leapsome](mailto:support@leapsome.com) per aggiungere gli utenti o il dominio che devono essere aggiunti a un elenco di elementi consentiti per la piattaforma Leapsome. Se il dominio viene aggiunto dal team, verrà automaticamente eseguito il provisioning degli utenti nella piattaforma Leapsome. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Leapsome nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Leapsome per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare Leapsome con Azure AD](https://aad.portal.azure.com/)