---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con PureCloud by Genesys | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e PureCloud by Genesys.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e16a46db-5de2-4681-b7e0-94c670e3e54e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 779328f4c21afb4392663e6f8840749ea505c529
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2019
ms.locfileid: "73242429"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-purecloud-by-genesys"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con PureCloud by Genesys

Questa esercitazione descrive come integrare PureCloud by Genesys con Azure Active Directory (Azure AD). Al termine, è possibile:

* Usare Azure AD per controllare gli utenti che possono accedere a PureCloud by Genesys.
* Abilitare gli utenti per l'accesso automatico a PureCloud by Genesys con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Una sottoscrizione di PureCloud by Genesys abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* PureCloud by Genesys supporta l'accesso SSO avviato da **provider di servizi e provider di identità**.

> [!NOTE]
> Dal momento che l'ID di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un tenant.

## <a name="adding-purecloud-by-genesys-from-the-gallery"></a>Aggiunta di PureCloud by Genesys dalla raccolta

Per configurare l'integrazione di PureCloud by Genesys in Azure AD, è necessario aggiungere PureCloud by Genesys dalla raccolta all'elenco di app SaaS gestite. A questo scopo, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **PureCloud by Genesys** nella casella di ricerca.
1. Selezionare **PureCloud by Genesys** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-purecloud-by-genesys"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per PureCloud by Genesys

Configurare e testare l'accesso SSO di Azure AD con PureCloud by Genesys usando un utente di test di nome **B.Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in PureCloud by Genesys.

Per configurare e testare l'accesso SSO di Azure AD con PureCloud by Genesys, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** per consentire a B.Simon di usare l'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso SSO di PureCloud by Genesys](#configure-purecloud-by-genesys-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare un utente di test di PureCloud by Genesys](#create-purecloud-by-genesys-test-user)** : per avere una controparte di B.Simon in PureCloud by Genesys collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **PureCloud by Genesys** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** selezionare sull'icona della penna relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **provider di identità**:

    a. Nella casella **Identificatore** immettere un URL corrispondente alla propria area:

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.au/saml` |

    b. Nella casella **URL di risposta** immettere un URL corrispondente alla propria area:

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.com.au/saml`|

1. Selezionare **Impostare URL aggiuntivi** e quindi seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **provider di servizi**:

    Nella casella **URL di accesso** immettere un URL corrispondente alla propria area:
    
    | |
    |--|
    | `https://login.mypurecloud.com` |
    | `https://login.mypurecloud.de` |
    | `https://login.mypurecloud.jp` |
    | `https://login.mypurecloud.ie` |
    | `https://login.mypurecloud.com.au` |

1. L'applicazione PureCloud by Genesys prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti:

    ![image](common/default-attributes.png)

1. L'applicazione PureCloud by Genesys prevede inoltre il passaggio di altri attributi nella risposta SAML, come illustrato nella tabella seguente. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base alle esigenze.

    | NOME | Attributo di origine|
    | ---------------| --------------- |
    | Email | user.userprinicipalname |
    | OrganizationName | `Your organization name` |

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura PureCloud by Genesys** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure:

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere il nome utente nel formato seguente: username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Selezionare **Create** (Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si imposterà B.Simon per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a PureCloud by Genesys.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **PureCloud by Genesys**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco Utenti e quindi scegliere il pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi scegliere il pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** selezionare il pulsante **Assegna**.

## <a name="configure-purecloud-by-genesys-sso"></a>Configurare l'accesso Single Sign-On di PureCloud by Genesys

1. In un'altra finestra del Web browser accedere a PureCloud by Genesys come amministratore.

1. Selezionare **Amministratore** nella parte superiore e passare a **Single Sign-On** in **Integrazioni**.

    ![Configure Single Sign-On](./media/purecloud-by-genesys-tutorial/configure01.png)

1. Passare alla scheda **ADFS/Azure AD (Premium)** e quindi seguire questa procedura:

    ![Configure Single Sign-On](./media/purecloud-by-genesys-tutorial/configure02.png)

    a. Scegliere **Sfoglia** per caricare il certificato con codifica Base64 scaricato dal portale di Azure in **ADFS Certificate** (Certificato ADFS).

    b. Nella casella di testo **ADFS Issuer URI** (URI autorità di certificazione ADFS) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    c. Nella casella **URI di destinazione** incollare il valore di **URL di accesso** copiato dal portale di Azure.

    d. Per il valore di **Relying Party Identifier** (Identificatore relying party) passare al portale di Azure e quindi nella pagina di integrazione dell'applicazione **PureCloud by Genesys** selezionare la scheda **Proprietà** e copiare il valore di **ID applicazione**. Incollare questo valore nella casella **Relying Party Identifier** (Identificatore relying party).

    ![Configure Single Sign-On](./media/purecloud-by-genesys-tutorial/configure06.png)

    e. Selezionare **Salva**.

### <a name="create-purecloud-by-genesys-test-user"></a>Creare l'utente di test di PureCloud by Genesys

Per consentire agli utenti di Azure AD di accedere a PureCloud by Genesys, è necessario effettuarne il provisioning in PureCloud by Genesys. Nel caso di PureCloud by Genesys il provisioning è un'attività manuale.

**Per effettuare il provisioning di un account utente, seguire questa procedura:**

1. Accedere a PureCloud by Genesys come amministratore.

1. Selezionare **Amministratore** nella parte superiore e passare a **Persone** in **People & Permissions** (Persone e autorizzazioni).

    ![Configure Single Sign-On](./media/purecloud-by-genesys-tutorial/configure03.png)

1. Nella pagina **Persone** selezionare **Aggiungi persona**.

    ![Configure Single Sign-On](./media/purecloud-by-genesys-tutorial/configure04.png)

1. Nella finestra di dialogo **Add People to the Organization** (Aggiungi persone all'organizzazione) seguire questa procedura:

    ![Configure Single Sign-On](./media/purecloud-by-genesys-tutorial/configure05.png)

    a. Nella casella **Nome completo** immettere il nome di un utente. Ad esempio:  **B.simon**.

    b. Nella casella **Posta elettronica** immettere l'indirizzo di posta elettronica dell'utente, ad esempio **b.simon\@contoso.com**.

    c. Selezionare **Create** (Crea).

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si seleziona il riquadro di **PureCloud by Genesys** nel pannello di accesso, si dovrebbe accedere automaticamente all'account di PureCloud by Genesys per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sull'integrazione delle app SaaS con Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Informazioni sull'accesso condizionale in Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare PureCloud by Genesys con Azure AD](https://aad.portal.azure.com/)