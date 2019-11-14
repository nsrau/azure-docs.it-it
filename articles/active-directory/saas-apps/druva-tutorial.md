---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Druva | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Druva.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ab92b600-1fea-4905-b1c7-ef8e4d8c495c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 16b23ef246561d052935642c323c2d830e21cbe7
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "73570231"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-druva"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Druva

Questa esercitazione descrive come integrare Druva con Azure Active Directory (Azure AD). Integrando Druva con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Druva.
* Abilitare gli utenti per l'accesso automatico a Druva con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Druva abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Druva supporta l'accesso SSO avviato da **SP e IDP**

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.

## <a name="adding-druva-from-the-gallery"></a>Aggiunta di Druva dalla raccolta

Per configurare l'integrazione di Druva in Azure AD, è necessario aggiungere Druva dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Druva** nella casella di ricerca.
1. Selezionare **Druva** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-druva"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Druva

Configurare e testare l'accesso SSO di Azure AD con Druva usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Druva.

Per configurare e testare l'accesso SSO di Azure AD con Druva, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    * **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    * **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Druva](#configure-druva-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    * **[Creare l'utente di test di Druva](#create-druva-test-user)** : per avere una controparte di B.Simon in Druva collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Druva** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

1. Nella sezione **Configurazione SAML di base**, se si vuole configurare l'applicazione in modalità avviata da **IDP**, non è necessario eseguire alcun passaggio, perché l'app è già preintegrata in Azure.

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL di accesso** digitare un URL: `https://login.druva.com/api/commonlogin/samlconsume`

1. Fare clic su **Save**.

1. L'applicazione Druva prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/default-attributes.png)

1. Oltre quelli elencati in precedenza, l'applicazione Druva prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.

    | NOME | Attributo di origine|
    | ------------------- | -------------------- |
    | emailAddress | user.email |
    | druva_auth_token | Token SSO generato dalla console di amministrazione DCP, senza virgolette.  Ad esempio:  X-XXXXX-XXXX-S-A-M-P-L-E+TXOXKXEXNX=. Azure racchiude automaticamente il token di autenticazione tra virgolette. |

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura Druva** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Druva.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Druva**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-druva-sso"></a>Configurare l'accesso SSO di Druva

1. In un'altra finestra del Web browser accedere al sito aziendale di Druva come amministratore.

1. Fare clic sul logo Druva nell'angolo in alto a sinistra e quindi fare clic su **Druva Cloud Settings** (Impostazioni di Druva Cloud).

    ![Impostazioni](./media/druva-tutorial/ic795091.png "Impostazioni")

1. Nella scheda **Single Sign-On** fare clic su **Edit** (Modifica).

    ![Impostazioni per l'accesso Single Sign-On](./media/druva-tutorial/ic795092.png "Single Sign-On Settings")

1. Nella pagina **Edit Single Sign-On Settings** (Modifica impostazioni per l'accesso Single Sign-On) seguire questa procedura:

    ![Impostazioni per l'accesso Single Sign-On](./media/druva-tutorial/ic795095.png "Single Sign-On Settings")

    1. Nella casella di testo **ID Provider Login URL** (URL di accesso provider di identità) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    1. Aprire il certificato con codifica Base 64 nel Blocco note, copiarne il contenuto negli Appunti e quindi incollarlo nella casella di testo **ID Provider Certificate**

       > [!NOTE]
       > Per abilitare l'accesso Single Sign-on per gli amministratori, selezionare le caselle di controllo **Administrators log into Druva Cloud through SSO provider** (Gli amministratori accedono a Druva Cloud tramite il provider SSO) e **Allow failsafe access to Druva Cloud administrators (recommended)** (Consenti accesso failsafe agli amministratori di Druva Cloud - scelta consigliata). Druva consiglia di abilitare l'opzione **Failsafe for Administrators** (Failsafe per amministratori) in modo da consentire l'accesso alla console DCP in caso di errori con l'IdP. Consente inoltre agli amministratori di usare sia SSO che la password DCP per accedere alla console DCP.

    1. Fare clic su **Save**. In questo modo è possibile accedere a Druva Cloud Platform tramite SSO.

### <a name="create-druva-test-user"></a>Creare l'utente di test di Druva

In questa sezione viene creato un utente di nome B.Simon in Druva. Druva supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in Druva, ne viene creato uno nuovo dopo l'autenticazione.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Druva nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Druva per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare Druva con Azure AD](https://aad.portal.azure.com/)