---
title: 'Esercitazione: Integrazione di Azure Active Directory con Help Scout | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Help Scout.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0aad9910-0bc1-4394-9f73-267cf39973ab
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b71ccbc6cfdb9d3d37fc46b0e932fa98eee2fb43
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73159102"
---
# <a name="tutorial-azure-active-directory-integration-with-help-scout"></a>Esercitazione: Integrazione di Azure Active Directory con Help Scout

Questa esercitazione descrive come integrare Help Scout con Azure Active Directory (Azure AD).
L'integrazione di Help Scout con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Help Scout.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Help Scout con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Help Scout, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Help Scout abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Help Scout supporta l'accesso SSO avviato da **SP e IDP**
* Help Scout supporta il provisioning utenti **JIT**

## <a name="adding-help-scout-from-the-gallery"></a>Aggiunta di Help Scout dalla raccolta

Per configurare l'integrazione di Help Scout in Azure AD, è necessario aggiungere Help Scout dalla raccolta al proprio elenco delle app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Help Scout** nella casella di ricerca.
1. Selezionare **Help Scout** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Help Scout usando un utente di test di nome **B.Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Help Scout.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Help Scout, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    * **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    * **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On per Help Scout](#configure-help-scout-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    * **[Creare un utente di test di Help Scout](#create-help-scout-test-user)** : per avere una controparte di B.Simon in Help Scout collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Help Scout, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Help Scout** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

1. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni su URL e dominio per Single Sign-On di Help Scout](common/idp-intiated.png)

    a. Il valore di **Identificatore** corrisponde al valore di **Audience URI (Service Provider Entity ID)** (URI destinatario - ID entità provider di servizi) di Help Scout e inizia con `urn:`

    b. Il valore di **URL di risposta** corrisponde al valore di **Post-back URL (Assertion Consumer Service URL)** (URL postback (URL servizio consumer di asserzione)) di Help Scout e inizia con `https://` 

    > [!NOTE]
    > I valori in questi URL sono forniti solo a scopo dimostrativo. È necessario aggiornare questi valori dall'URL di risposta e dall'identificatore effettivi. Si ottengono questi valori dalla scheda **Single Sign-On** nella sezione Autenticazione, che è illustrata più avanti nell'esercitazione.

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per Single Sign-On di Help Scout](common/metadata-upload-additional-signon.png)

    Nella casella di testo **URL di accesso** digitare l'URL come: `https://secure.helpscout.net/members/login/`

1. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura Help Scout** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

Questa sezione descrive come creare un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **B.Simon**.
  
    b. Nel campo **Nome utente** digitare **B.Simon\@dominioaziendale.estensione**  
    Ad esempio: B.Simon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Help Scout.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Help Scout**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Help Scout**.

    ![Collegamento di Help Scout nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-help-scout-sso"></a>Configurare l'accesso Single Sign-On per Help Scout

1. Per automatizzare la configurazione all'interno di Help Scout, è necessario installare l'**estensione del browser per l'accesso sicuro My Apps** facendo clic su **Installa l'estensione**.

    ![Estensione per App personali](common/install-myappssecure-extension.png)

1. Dopo aver aggiunto l'estensione al browser, fare clic su **Configura Help Scout** per passare direttamente all'applicazione Help Scout. Nell'applicazione specificare le credenziali di amministratore per accedere a Help Scout. L'estensione del browser configurerà automaticamente l'applicazione per l'utente e automatizzerà i passaggi da 3 a 7.

    ![Eseguire la configurazione](common/setup-sso.png)

1. Se si vuole configurare manualmente Help Scout, aprire una nuova finestra del Web browser, accedere al sito aziendale di Help Scout come amministratore e seguire questa procedura:

1. Fare clic su **Manage** (Gestisci) nel menu in alto e quindi selezionare **Company** (Società) nel menu a discesa.

    ![Configure Single Sign-On](./media/helpscout-tutorial/settings1.png)

1. Selezionare **Authentication** (Autenticazione) nel riquadro di spostamento a sinistra.

    ![Configure Single Sign-On](./media/helpscout-tutorial/settings2.png)

1. Nella sezione relativa alle impostazioni SAML, seguire questa procedura:

    ![Configure Single Sign-On](./media/helpscout-tutorial/settings3.png)

    a. Copiare il valore di **Post-back URL (Assertion Consumer Service URL)** (URL post-back (URL servizio consumer di asserzione)) e incollarlo nella casella di testo **URL di risposta** nella sezione **Configurazione SAML di base** del portale di Azure.

    b. Copiare il valore **Audience URI (Service Provider Entity ID)** (URI destinatario - ID entità provider di servizi) e incollarlo nella casella di testo **Identificatore** nella sezione **Configurazione SAML di base** del portale di Azure.

1. Attiva **Abilita SAML** ed esegui la procedura seguente:

    ![Configure Single Sign-On](./media/helpscout-tutorial/settings4.png)

    a. Nella casella di testo **Single Sign-On URL** (URL di Single Sign-On) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    b. Fare clic sul pulsante **Browser** per caricare il **Certificato (Base64)** scaricato dal portale di Azure.

    c. Immettere i domini di posta elettronica domini, ad esempio `contoso.com` nella casella di testo **Email Domains** (Domini di posta elettronica). È possibile separare più domini con una virgola. In qualsiasi momento un utente di Scout Guida o un amministratore che entra in tale dominio specifico nella [pagina di accesso della Guida Scout](https://secure.helpscout.net/members/login/) verrà indirizzato al provider di identità per l'autenticazione con le proprie credenziali.

    d. Infine, è possibile attivare o disattivare **Force SAML Sign-on** (Forza accesso SAML) se si desidera che gli utenti accedano a Guida Scout solo tramite questo metodo. Se si desidera lasciare l'opzione per poter accedere con le proprie credenziali di Scout Guida, è possibile lasciarla disattivata. Anche se questa opzione è abilitata, il proprietario dell'account sarà sempre in grado di accedere a Scout Guida con la password dell'account.

    e. Fare clic su **Save**.

### <a name="create-help-scout-test-user"></a>Creare l'utente di test di Help Scout

In questa sezione viene creato un utente di nome B.Simon in Help Scout. Help Scout supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in Help Scout, ne viene creato uno nuovo dopo l'autenticazione.

### <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Help Scout nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Help Scout per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare Help Scout con Azure AD](https://aad.portal.azure.com/)