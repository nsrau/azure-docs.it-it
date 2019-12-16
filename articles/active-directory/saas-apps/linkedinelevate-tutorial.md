---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con LinkedIn Elevate | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e LinkedIn Elevate.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2ad9941b-c574-42c3-bd0f-5d6ec68537ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 03efae5f9dec904f141a6776766850aa1f328892
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892125"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-elevate"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con LinkedIn Elevate

Questa esercitazione descrive come integrare LinkedIn Elevate con Azure Active Directory (Azure AD). Integrando LinkedIn Elevate con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a LinkedIn Elevate.
* Abilitare gli utenti per l'accesso automatico a LinkedIn Elevate con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di LinkedIn Elevate abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.



* LinkedIn Elevate supporta l'accesso SSO avviato da **SP e IDP**
* LinkedIn Elevate supporta il provisioning utenti **JIT**
* LinkedIn Elevate supporta il [provisioning utenti **automatico**](linkedinelevate-provisioning-tutorial.md)

## <a name="adding-linkedin-elevate-from-the-gallery"></a>Aggiunta di LinkedIn Elevate dalla raccolta

Per configurare l'integrazione di LinkedIn Elevate in Azure AD è necessario aggiungere LinkedIn Elevate dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **LinkedIn Elevate** nella casella di ricerca.
1. Selezionare **LinkedIn Elevate** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-linkedin-elevate"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per LinkedIn Elevate

Configurare e testare l'accesso SSO di Azure AD con LinkedIn Elevate usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in LinkedIn Elevate.

Per configurare e testare l'accesso SSO di Azure AD con LinkedIn Elevate, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di LinkedIn Elevate](#configure-linkedin-elevate-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di LinkedIn Elevate](#create-linkedin-elevate-test-user)** : per avere una controparte di B.Simon in LinkedIn Elevate collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **LinkedIn Elevate** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Nella casella di testo **Identifier** (Identificatore) immettere il valore di **Entity ID** (ID entità). Tale valore verrà copiato dal portale di Linkedin, come descritto più avanti in questa esercitazione.

    b. Nella casella di testo **Reply URL** (URL di risposta) immettere il valore di **Assertion Consumer Access (ACS) Url** (URL ACS). Tale valore verrà copiato dal portale di Linkedin, come descritto più avanti in questa esercitazione.

5. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=elevate&applicationInstanceId=<InstanceId>`

1. L'applicazione LinkedIn Elevate richiede le asserzioni SAML in un formato specifico. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. La schermata seguente illustra l'elenco degli attributi predefiniti in cui **nameidentifier** è associato a **user.userprincipalname**. Dato che l'applicazione LinkedIn Elevate prevede che nameidentifier sia associato a **user.mail**, è necessario modificare il mapping dell'attributo facendo clic sull'icona Modifica a tale scopo.

    ![image](common/edit-attribute.png)

1. Oltre quelli elencati in precedenza, l'applicazione LinkedIn Elevate prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.

    | NOME | Attributo di origine|
    | -------| -------------|
    | department | user.department |

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il file **XML dei metadati della federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura LinkedIn Elevate** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a LinkedIn Elevate.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **LinkedIn Elevate**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-linkedin-elevate-sso"></a>Configurare l'accesso Single Sign-On di LinkedIn Elevate

1. In un'altra finestra del Web browser accedere al tenant LinkedIn Elevate come amministratore.

1. In **Account Center** (Centro account) fare clic su **Global Settings** (Impostazioni globali) in **Settings** (Impostazioni). Selezionare anche **Elevate - Elevate AAD Test** (Elevate - Test AAD Elevate) dall'elenco a discesa.

    ![Configure Single Sign-On](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_01.png)

1. Fare clic su **OR Click Here to load and copy individual fields from the form** (OPPURE fare clic qui per caricare e copiare singoli campi del modulo) e seguire questa procedura:

    ![Configure Single Sign-On](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_03.png)

    a. Copiare il valore di **Entity ID** (ID entità) e incollarlo nella casella di testo **Identificatore** in **Configurazione SAML di base** del portale di Azure.

    b. Copiare il valore di **Assertion Consumer Access (ACS) Url** (URL ACS) e incollarlo nella casella di testo **URL di risposta** in **Configurazione SAML di base** del portale di Azure.

1. Accedere alla sezione **LinkedIn Admin Settings** (Impostazioni di amministrazione LinkedIn). Per caricare il file XML scaricato dal portale di Azure, fare clic sull'opzione Upload XML file (Carica il file XML).

    ![Configure Single Sign-On](./media/linkedinelevate-tutorial/tutorial_linkedin_metadata_03.png)

1. Fare clic su **On** per abilitare SSO. Lo stato SSO passa da **Not Connected** (Non connesso) a **Connected** (Connesso)

    ![Configure Single Sign-On](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-linkedin-elevate-test-user"></a>Creare l'utente di test di LinkedIn Elevate

L'applicazione LinkedIn Elevate supporta il provisioning utenti JIT e dopo l'autenticazione gli utenti verranno automaticamente creati nell'applicazione. Nella pagina delle impostazioni di amministrazione del portale di LinkedIn Elevate spostare il selettore dell'opzione **Automatically Assign licenses** (Assegna automaticamente le licenze) per attivare il provisioning just-in-time, assegnando una licenza all'utente. LinkedIn Elevate supporta anche il provisioning utenti automatico; [qui](linkedinelevate-provisioning-tutorial.md) è possibile trovare altre informazioni su come configurare il provisioning utenti automatico.

   ![Creazione di un utente di test di Azure AD](./media/linkedinelevate-tutorial/LinkedinUserprovswitch.png)

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di LinkedIn Elevate nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di LinkedIn Elevate per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare LinkedIn Elevate con Azure AD](https://aad.portal.azure.com/)

