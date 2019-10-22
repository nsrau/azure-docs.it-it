---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Kanbanize | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Kanbanize.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b436d2f6-bfa5-43fd-a8f9-d2144dc25669
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/09/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c5a3a096c5a44f681d23587837ae31fd1af33b2
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72373219"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-kanbanize"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Kanbanize

Questa esercitazione descrive come integrare Kanbanize con Azure Active Directory (Azure AD). Integrando Kanbanize con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Kanbanize.
* Abilitare gli utenti per l'accesso automatico a Kanbanize con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Kanbanize abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Kanbanize supporta l'accesso SSO avviato da **provider di servizi e IDP**
* Kanbanize supporta il provisioning utenti **JIT**

## <a name="adding-kanbanize-from-the-gallery"></a>Aggiunta di Kanbanize dalla raccolta

Per configurare l'integrazione di Kanbanize in Azure AD, è necessario aggiungere Kanbanize dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Kanbanize** nella casella di ricerca.
1. Selezionare **Kanbanize** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-kanbanize"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Kanbanize

Configurare e testare l'accesso SSO di Azure AD con Kanbanize usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Kanbanize.

Per configurare e testare l'accesso SSO di Azure AD con Kanbanize, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Kanbanize](#configure-kanbanize-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Kanbanize](#create-kanbanize-test-user)** : per avere una controparte di B.Simon in Kanbanize collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Kanbanize** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP**:

     a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<subdomain>.kanbanize.com/`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<subdomain>.kanbanize.com/saml/acs`

    c. Fare clic su **Impostare URL aggiuntivi**.

    d. Nella casella di testo **Stato dell'inoltro** digitare un URL: `/ctrl_login/saml_login`

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<subdomain>.kanbanize.com`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori contattare il [team di supporto clienti di Kanbanize](mailto:support@ms.kanbanize.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. L'applicazione Kanbanize prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente illustra l'elenco degli attributi predefiniti in cui nameidentifier è associato a **user.userprincipalname**. L'applicazione Kanbanize prevede che nameidentifier sia associato a **user.mail**, di conseguenza è necessario modificare il mapping dell'attributo. A tale scopo, fare clic sull'icona Modifica e modificare il mapping.

    ![image](common/edit-attribute.png)

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura Kanbanize** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Kanbanize.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Kanbanize**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-kanbanize-sso"></a>Configurare l'accesso SSO di Kanbanize

1. Per automatizzare la configurazione all'interno di Kanbanize, è necessario installare l'**estensione del browser per l'accesso sicuro ad App personali** facendo clic su **Installa l'estensione**.

    ![Estensione per App personali](common/install-myappssecure-extension.png)

2. Dopo aver aggiunto l'estensione al browser, fare clic su **Configura Kanbanize** per passare direttamente all'applicazione Kanbanize. Nell'applicazione specificare le credenziali di amministratore per accedere a Kanbanize. L'estensione del browser configurerà automaticamente l'applicazione per l'utente e automatizzerà i passaggi da 3 a 7.

    ![Eseguire la configurazione](common/setup-sso.png)

3. Se si vuole configurare manualmente Kanbanize, aprire una nuova finestra del Web browser, accedere al sito aziendale di Kanbanize come amministratore e seguire questa procedura:

4. Andare sulla parte superiore destra della pagina e fare clic sul logo delle **impostazioni**.

    ![Impostazioni di Kanbanize](./media/kanbanize-tutorial/tutorial-kanbanize-set.png)

5. Nella pagina del pannello Administration (Amministrazione) dal lato sinistro del menu fare clic su **Integrations** (Integrazioni) e quindi abilitare **Single Sign-On**.

    ![Integrazioni di Kanbanize](./media/kanbanize-tutorial/tutorial-kanbanize-admin.png)

6. Nella sezione delle integrazioni fare clic su **CONFIGURE** (CONFIGURA) per aprire la pagina **Single Sign-On Integration** (Integrazione Single Sign-On).

    ![Configurazione di Kanbanize](./media/kanbanize-tutorial/tutorial-kanbanize-config.png)

7. Nella pagina **Single Sign-On Integration** (Integrazione Single Sign-On) in **Configurations** (Configurazioni) seguire questa procedura:

    ![Integrazioni di Kanbanize](./media/kanbanize-tutorial/tutorial-kanbanize-save.png)

    a. Nella casella di testo **IdP Entity ID** (ID entità provider di identità) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    b. Nella casella di testo **IdP Login Endpoint** (Endpoint di accesso IdP) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    c. Nella casella di testo **IdP Logout Endpoint** (Endpoint di disconnessione IdP) incollare il valore di **URL di disconnessione** copiato dal portale di Azure.

    d. Nella casella di testo **Attribute name for Email** (Nome attributo per la posta elettronica) immettere questo valore `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    e. Nella casella di testo **Attribute name for First Name** (Nome attributo per nome) immettere questo valore `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    f. Nella casella di testo **Attribute name for Last Name** (Nome attributo per cognome) immettere questo valore `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    > [!Note]
    > È possibile ottenere questi valori combinando i valori dello spazio dei nomi e del nome dell'attributo corrispondente nella sezione Attributi utente nel portale di Azure.

    g. Nel Blocco note aprire il certificato con codifica Base 64 scaricato dal portale di Azure copiarne il contenuto senza i contrassegni di inizio e fine e quindi incollarlo nella casella **Idp X.509 Certificate** (Certificato X.509 IdP).

    h. Selezionare **Enable login with both SSO and Kanbanize** (Abilita l'accesso sia con SSO che con Kanbanize).

    i. Fare clic su **Salva impostazioni**.

### <a name="create-kanbanize-test-user"></a>Creare l'utente di test di Kanbanize

In questa sezione viene creato un utente di nome B.Simon in Kanbanize. Kanbanize supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in Kanbanize, ne verrà creato uno nuovo dopo l'autenticazione. Per creare un utente manualmente, contattare il [team del supporto clienti di Kanbanize](mailto:support@ms.kanbanize.com).

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Kanbanize nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Kanbanize per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare Kanbanize con Azure AD](https://aad.portal.azure.com/)

