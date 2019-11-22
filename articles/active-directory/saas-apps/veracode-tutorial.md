---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Veracode | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Veracode.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4fe78050-cb6d-4db9-96ec-58cc0779167f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcec326ddab1e74f43e1bb7ef446998a40799fd0
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73043569"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-veracode"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Veracode

Questa esercitazione descrive come integrare Veracode con Azure Active Directory (Azure AD). Integrando Veracode con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Veracode.
* Abilitare gli utenti per l'accesso automatico a Veracode con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS (Software as a Service) con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Veracode abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. VeraCode supporta l'accesso SSO avviato da provider di identità e il provisioning utenti Just-In-Time (JIT).

## <a name="add-veracode-from-the-gallery"></a>Aggiungere Veracode dalla raccolta

Per configurare l'integrazione di Veracode in Azure AD, aggiungere Veracode dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare "Veracode" nella casella di ricerca.
1. Selezionare **Veracode** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-veracode"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Veracode

Configurare e testare l'accesso SSO di Azure AD con Veracode usando un utente di test di nome **B.Simon**. Per il corretto funzionamento dell'accesso Single Sign-On, è necessario stabilire un collegamento tra un utente di Azure AD e l'utente correlato in Veracode.

Per configurare e testare l'accesso SSO di Azure AD con Veracode, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** per consentire agli utenti di usare questa funzionalità.
    * **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    * **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** per consentire a B.Simon di usare l'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On per Veracode](#configure-veracode-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    * **[Creare un utente di test di Veracode](#create-veracode-test-user)** : per avere una controparte di B.Simon in Veracode collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Veracode** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Screenshot della pagina Configura l'accesso Single Sign-On con SAML, in cui è evidenziata l'icona della matita](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** l'applicazione è preconfigurata e gli URL necessari sono già prepopolati con Azure. Selezionare **Salva**.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** . Selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Screenshot della sezione Certificato di firma SAML con il collegamento Scarica evidenziato](common/certificatebase64.png)

1. Veracode prevede un formato specifico per le asserzioni SAML, in base al quale è necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![Screenshot della sezione Attributi utente e attestazioni](common/default-attributes.png)

1. Veracode prevede inoltre il passaggio di qualche altro attributo nella risposta SAML. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base alle esigenze.

    | NOME | Attributo di origine|
    | ---------------| --------------- |
    | firstname |User.givenname |
    | lastname |User.surname |
    | email |User.mail |

1. Nella sezione **Configura Veracode** copiare gli URL appropriati in base alle esigenze.

    ![Screenshot della sezione Configura Veracode con gli URL di configurazione evidenziati](common/copy-configuration-urls.png)

## <a name="configure-veracode-sso"></a>Configurare l'accesso Single Sign-On di Veracode

1. In un'altra finestra del Web browser accedere al sito aziendale di Veracode come amministratore.

1. Dal menu in alto selezionare **Settings** (Impostazioni)  > **Admin**.
   
    ![Screenshot della schermata di amministrazione di Veracode, con l'icona delle impostazioni e l'opzione per l'accesso come amministratore evidenziate](./media/veracode-tutorial/ic802911.png "Administration")

1. Selezionare la scheda **SAML**.

1. Nella sezione **Impostazioni SAML dell’organizzazione** seguire questa procedura:

    ![Screenshot della sezione relativa alle impostazioni SAML dell'organizzazione](./media/veracode-tutorial/ic802912.png "Administration")

    a.  Nella casella di testo **Issuer** (Autorità di certificazione) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    b. Per **Assertion Signing Certificate** (Certificato di firma dell'asserzione) selezionare **Choose File** (Scegli file) per caricare il certificato scaricato dal portale di Azure.

    c. Per **Self Registration** (Registrazione self-service) selezionare **Enable Self Registration** (Abilita registrazione self-service).

1. Nella sezione **Self Registration Settings** (Impostazioni di registrazione self-service) seguire questa procedura e quindi fare clic su **Save** (Salva):

    ![Screenshot della sezione delle impostazioni di registrazione self-service con diverse opzioni evidenziate](./media/veracode-tutorial/ic802913.png "Administration")

    a. Per **New User Activation** (Attivazione nuovo utente) selezionare **No Activation Required** (Nessuna attivazione richiesta).

    b. Per **User Data Updates** (Aggiornamenti dati utenti) selezionare **Preference Veracode User Data** (Dati utenti Veracode di preferenza).

    c. In **Dettagli sull’attributo SAML**selezionare le opzioni seguenti:
      * **User Roles**
      * **Policy Administrator**
      * **Reviewer**
      * **Security Lead**
      * **Executive**
      * **Submitter**
      * **Creator**
      * **All Scan Types**
      * **Team Memberships**
      * **Default Team**

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory** >**Utenti** > **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:

   1. Per **Nome** immettere `B.Simon`.  
   1. Per **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare **Mostra password** e prendere nota del valore visualizzato.
   1. Selezionare **Create** (Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Veracode.

1. Nel portale di Azure selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Veracode**.
1. Nella pagina di panoramica dell'app individuare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Screenshot della sezione Gestione con Utenti e gruppi evidenziato](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente**. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Utenti e gruppi**.

    ![Screenshot della pagina Utenti e gruppi, in cui è evidenziato il pulsante Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** nell'elenco **Utenti**. Scegliere quindi **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco. Scegliere quindi **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Assegna**.

### <a name="create-veracode-test-user"></a>Creare l'utente di test di Veracode

Per l'accesso degli utenti di Azure AD a Veracode, è necessario ne sia stato eseguito il provisioning in Veracode. Questa attività è automatizzata e non è necessario eseguire alcuna operazione manualmente. Se necessario, gli utenti vengono creati automaticamente durante il primo tentativo di accesso Single Sign-On.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente di Veracode per effettuare il provisioning degli account utente di Azure AD.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si seleziona **Veracode** nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Veracode per cui si è configurato l'accesso Single Sign-On. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare Veracode con Azure AD](https://aad.portal.azure.com/)