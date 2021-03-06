---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con LinkedIn Sales Navigator | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e LinkedIn Sales Navigator.
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
ms.openlocfilehash: 3f4d4d1e0fc44b9fb031ce5e3e45219fa8169562
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92458507"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-sales-navigator"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con LinkedIn Sales Navigator

Questa esercitazione descrive come integrare LinkedIn Sales Navigator con Azure Active Directory (Azure AD). Integrando LinkedIn Sales Navigator con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a LinkedIn Sales Navigator.
* Abilitare gli utenti per l'accesso automatico a LinkedIn Sales Navigator con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di LinkedIn Sales Navigator abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* LinkedIn Sales Navigator supporta l'accesso SSO avviato da **SP e IDP**
* LinkedIn Sales Navigator supporta il provisioning utenti **JIT**
* LinkedIn Sales Navigator supporta il [provisioning utenti **automatico**](linkedinsalesnavigator-provisioning-tutorial.md)

## <a name="adding-linkedin-sales-navigator-from-the-gallery"></a>Aggiunta di LinkedIn Sales Navigator dalla raccolta

Per configurare l'integrazione di LinkedIn Sales Navigator in Azure AD è necessario aggiungere LinkedIn Sales Navigator dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory** .
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni** .
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione** .
1. Nella sezione **Aggiungi dalla raccolta** digitare **LinkedIn Sales Navigator** nella casella di ricerca.
1. Selezionare **LinkedIn Sales Navigator** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-linkedin-sales-navigator"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per LinkedIn Sales Navigator

Configurare e testare l'accesso SSO di Azure AD con LinkedIn Sales Navigator usando un utente di test di nome **B.Simon** . Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in LinkedIn Sales Navigator.

Per configurare e testare l'accesso SSO di Azure AD con LinkedIn Sales Navigator, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    * **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    * **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di LinkedIn Sales Navigator](#configure-linkedin-sales-navigator-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    * **[Creare l'utente di test di LinkedIn Sales Navigator](#create-linkedin-sales-navigator-test-user)** : per avere una controparte di B.Simon in LinkedIn Sales Navigator collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **LinkedIn Sales Navigator** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On** .
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML** .
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP** :

    a. Nella casella di testo **Identifier** (Identificatore) immettere il valore di **Entity ID** (ID entità). Tale valore verrà copiato dal portale di Linkedin, come descritto più avanti in questa esercitazione.

    b. Nella casella di testo **Reply URL** (URL di risposta) immettere il valore di **Assertion Consumer Access (ACS) Url** (URL ACS). Tale valore verrà copiato dal portale di Linkedin, come descritto più avanti in questa esercitazione.

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP** :

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://www.linkedin.com/checkpoint/enterprise/login/<account id>?application=salesNavigator`

1. L'applicazione LinkedIn Sales Navigator richiede le asserzioni SAML in un formato specifico. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/default-attributes.png)

1. Oltre quelli elencati in precedenza, l'applicazione LinkedIn Sales Navigator prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.

    | Nome | Attributo di origine|
    | --- | --- |
    | email| user.mail |
    | department| user.department |
    | firstname| user.givenname |
    | lastname| user.surname |
    | Identificatore univoco dell'utente | user.mail |

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il file **XML dei metadati della federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura LinkedIn Sales Navigator** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory** , **Utenti** e quindi **Tutti gli utenti** .
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password** .
   1. Fare clic su **Crea** .

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a LinkedIn Sales Navigator.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni** .
1. Nell'elenco delle applicazioni selezionare **LinkedIn Sales Navigator** .
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi** .

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione** .

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna** .

## <a name="configure-linkedin-sales-navigator-sso"></a>Configurare l'accesso Single Sign-On di LinkedIn Sales Navigator

1. In un'altra finestra del Web browser accedere al sito Web di **LinkedIn Sales Navigator** come amministratore.

1. In **Account Center** (Centro account) fare clic su **Global Settings** (Impostazioni globali) in **Settings** (Impostazioni). Selezionare **Sales Navigator** nell'elenco a discesa.

    ![Screenshot che mostra la sezione Application Settings in cui è possibile selezionare l'opzione Sales Navigator.](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_01.png)

1. Fare clic su **OR Click Here to load and copy individual fields from the form** (OPPURE fare clic qui per caricare e copiare singoli campi del modulo) e seguire questa procedura:

    ![Screenshot che mostra la sezione Single Sign-On in cui è possibile immettere i valori descritti.](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_031.png)

    a. Copiare il valore di **Entity Id** (ID entità) e incollarlo nella casella di testo **Identificatore** in **Configurazione SAML di base** del portale di Azure.

    b. Copiare il valore di **Assertion Consumer Access (ACS) Url** (URL ACS) e incollarlo nella casella di testo **URL di risposta** in **Configurazione SAML di base** del portale di Azure.

1. Accedere alla sezione **LinkedIn Admin Settings** (Impostazioni di amministrazione LinkedIn). Per caricare il file XML scaricato dal portale di Azure, fare clic sull'opzione **Upload XML file** (Carica il file XML).

    ![Screenshot che mostra la sezione Configure the LinkedIn service provider SSO settings in cui è possibile caricare un file XML.](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_metadata_03.png)

1. Fare clic su **On** per abilitare SSO. Lo stato SSO passa da **Not Connected** (Non connesso) a **Connected** (Connesso)

    ![Screenshot che mostra la sezione Single Sign-On in cui è possibile abilitare l'opzione Authenticate users with SSO.](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-linkedin-sales-navigator-test-user"></a>Creare l'utente di test di LinkedIn Sales Navigator

L'applicazione Linked Sales Navigator supporta il provisioning dell'utente just-in-time e dopo l'autenticazione gli utenti verranno automaticamente creati nell'applicazione. Attivare l' **assegnazione automatica delle licenze** per assegnare una licenza all'utente.

   ![Creazione di un utente di test di Azure AD](./media/linkedinsalesnavigator-tutorial/LinkedinUserprovswitch.png)

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di LinkedIn Sales Navigator nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione LinkedIn Sales Navigator per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](./tutorial-list.md)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)

- [Provare LinkedIn Sales Navigator con Azure AD](https://aad.portal.azure.com/)