---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con EZOfficeInventory | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e EZOfficeInventory.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e8594f7c-dc2f-446f-9c25-676fe49ff3af
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc73d7ec81ea0105a5868ec698289bd27526a43b
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893502"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ezofficeinventory"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con EZOfficeInventory

Questa esercitazione descrive come integrare EZOfficeInventory con Azure Active Directory (Azure AD). Integrando EZOfficeInventory con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a EZOfficeInventory.
* Abilitare gli utenti per l'accesso automatico a EZOfficeInventory con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di EZOfficeInventory abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* EZOfficeInventory supporta l'accesso SSO avviato da **SP**

* EZOfficeInventory il provisioning utenti **JIT**

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.

## <a name="adding-ezofficeinventory-from-the-gallery"></a>Aggiunta di EZOfficeInventory dalla raccolta

Per configurare l'integrazione di EZOfficeInventory in Azure AD, è necessario aggiungere EZOfficeInventory dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **EZOfficeInventory** nella casella di ricerca.
1. Selezionare **EZOfficeInventory** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ezofficeinventory"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per EZOfficeInventory

Configurare e testare l'accesso SSO di Azure AD con EZOfficeInventory usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in EZOfficeInventory.

Per configurare e testare l'accesso SSO di Azure AD con EZOfficeInventory, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di EZOfficeInventory](#configure-ezofficeinventory-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di EZOfficeInventory](#create-ezofficeinventory-test-user)** : per avere una controparte di B.Simon in EZOfficeInventory collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **EZOfficeInventory** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<SUBDOMAIN>.ezofficeinventory.com/users/sign_in`

    > [!NOTE]
    > Poiché non è reale, è necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere tale valore, contattare il [team di supporto clienti di EZOfficeInventory](mailto:support@ezofficeinventory.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. L'applicazione EZOfficeInventory prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/default-attributes.png)

1. Oltre quelli elencati in precedenza, l'applicazione EZOfficeInventory prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.

    | NOME | Attributo di origine|
    | ---------------| --------------- |
    | First_name | user.givenname |
    | Last_name | user.surname |
    | Email | user.mail |

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura EZOfficeInventory** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a EZOfficeInventory.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **EZOfficeInventory**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-ezofficeinventory-sso"></a>Configurare l'accesso Single Sign-On di EZOfficeInventory

1. Per automatizzare la configurazione all'interno di EZOfficeInventory, è necessario installare l'**estensione del browser per l'accesso sicuro ad App personali** facendo clic su **Installa l'estensione**.

    ![Estensione per App personali](common/install-myappssecure-extension.png)

1. Dopo aver aggiunto l'estensione al browser, fare clic su **Configura EZOfficeInventory** per passare direttamente all'applicazione EZOfficeInventory. Nell'applicazione fornire le credenziali di amministratore per accedere a EZOfficeInventory. L'estensione del browser configurerà automaticamente l'applicazione e automatizzerà i passaggi da 3 a 5.

    ![Eseguire la configurazione](common/setup-sso.png)

1. Se si vuole configurare manualmente EZOfficeInventory, aprire una nuova finestra del Web browser, accedere al sito aziendale di EZOfficeInventory come amministratore e seguire questa procedura:

1. Nell'angolo in alto a destra della pagina fare clic su **Profile** (Profilo) e quindi passare a **Settings** (Impostazioni)  > **Add Ons** (Componenti aggiuntivi).

    ![Configurazione di EZOfficeInventory](./media/ezofficeinventory-tutorial/configure01.png)

1. Scorrere verso il basso fino alla sezione **SAML Integration** (Integrazione SAML) e seguire questa procedura:

    ![Configurazione di EZOfficeInventory](./media/ezofficeinventory-tutorial/configure02.png)

    a. Selezionare l'opzione **Enabled** (Abilitata).

    b. Nella casella di testo **Identity Provider URL** (URL del provider di identità) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    c. Aprire il certificato con codifica Base64, copiarne il contenuto e incollarlo nella casella di testo **Identity Provider Certificate** (Certificato provider di identità).

    d. Nella casella di testo **Login Button Text** (Testo pulsante di accesso) immettere il testo del pulsante di accesso.

    e. Nella casella di testo **First Name** (Nome) immettere **first_name**.

    f. Nella casella di testo **Last Name** (Nome) immettere **last_name**.

    g. Nella casella di testo **Email**  immettere **email**.

    h. Selezionare il ruolo previsto dai requisiti con l'opzione **EZOfficeInventory Role By default** (Ruolo di EZOfficeInventory per impostazione predefinita).

    i. Fare clic su **Update**.

### <a name="create-ezofficeinventory-test-user"></a>Creare l'utente di test di EZOfficeInventory

In questa sezione viene creato un utente di nome Britta Simon in EZOfficeInventory. EZOfficeInventory supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in EZOfficeInventory, ne viene creato uno nuovo dopo l'autenticazione.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di EZOfficeInventory nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di EZOfficeInventory per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare EZOfficeInventory con Azure AD](https://aad.portal.azure.com/)
