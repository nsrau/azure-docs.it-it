---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con ScaleX Enterprise | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e ScaleX Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c2379a8d-a659-45f1-87db-9ba156d83183
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e54994d02dd1abbca1602952fbad058b3ad993d
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72594269"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-scalex-enterprise"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con ScaleX Enterprise

Questa esercitazione descrive come integrare ScaleX Enterprise con Azure Active Directory (Azure AD). Integrando ScaleX Enterprise con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a ScaleX Enterprise.
* Abilitare gli utenti per l'accesso automatico a ScaleX Enterprise con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di ScaleX Enterprise abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* ScaleX Enterprise supporta l'accesso SSO avviato da **SP e IDP**

## <a name="adding-scalex-enterprise-from-the-gallery"></a>Aggiunta di ScaleX Enterprise dalla raccolta

Per configurare l'integrazione di ScaleX Enterprise in Azure AD, è necessario aggiungere ScaleX Enterprise dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **ScaleX Enterprise** nella casella di ricerca.
1. Selezionare **ScaleX Enterprise** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-scalex-enterprise"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per ScaleX Enterprise

Configurare e testare l'accesso SSO di Azure AD con ScaleX Enterprise usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in ScaleX Enterprise.

Per configurare e testare l'accesso SSO di Azure AD con ScaleX Enterprise, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    * **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    * **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di ScaleX Enterprise](#configure-scalex-enterprise-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    * **[Creare l'utente di test di ScaleX Enterprise](#create-scalex-enterprise-test-user)** : per avere una controparte di B.Simon in ScaleX Enterprise collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **ScaleX Enterprise** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://platform.rescale.com/saml2/<company id>/`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://platform.rescale.com/saml2/<company id>/acs/`

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://platform.rescale.com/saml2/<company id>/sso/`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di ScaleX Enterprise](https://info.rescale.com/contact_sales). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. L'applicazione ScaleX Enterprise prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente illustra l'elenco degli attributi predefiniti in cui **emailaddress** è associato a **user.mail**. Dal momento che l'applicazione ScaleX Enterprise prevede che **emailaddress** sia associato a **user.userprincipalname**, è necessario modificare il mapping dell'attributo facendo clic sull'icona **Modifica**.

    ![image](common/edit-attribute.png)

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura ScaleX Enterprise** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a ScaleX Enterprise.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **ScaleX Enterprise**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-scalex-enterprise-sso"></a>Configurare l'accesso SSO di ScaleX Enterprise

1. Per automatizzare la configurazione all'interno di ScaleX Enterprise, è necessario installare l'**estensione del browser per l'accesso sicuro ad App personali** facendo clic su **Installa l'estensione**.

    ![Estensione per App personali](common/install-myappssecure-extension.png)

1. Dopo aver aggiunto l'estensione al browser, fare clic su **Configura ScaleX Enterprise** per passare direttamente all'applicazione ScaleX Enterprise. Nell'applicazione specificare le credenziali di amministratore per accedere a ScaleX Enterprise. L'estensione del browser configurerà automaticamente l'applicazione e automatizzerà i passaggi da 3 a 6.

    ![Eseguire la configurazione](common/setup-sso.png)

1. Se si vuole configurare manualmente ScaleX Enterprise, aprire una nuova finestra del Web browser, accedere al sito aziendale di ScaleX Enterprise come amministratore e seguire questa procedura:

1. Fare clic sul menu in alto a destra e seleziona **Contoso Administration** (Amministrazione Contoso).

    > [!NOTE]
    > Contoso è solo un esempio. Deve essere il nome effettivo della società.

    ![Configura accesso Single Sign-On](./media/scalex-enterprise-tutorial/Test_Admin.png)

1. Selezionare **Integrations** (Integrazioni) nel menu in alto e scegliere **Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/scalex-enterprise-tutorial/admin_sso.png) 

1. Compilare il modulo come segue:

    ![Configura accesso Single Sign-On](./media/scalex-enterprise-tutorial/scalex_admin_save.png)

    a. Selezionare **Create any user who can authenticate with SSO** (Crea un utente qualsiasi che può eseguire l'autenticazione con SSO)

    b. **Service Provider saml** (Provider del servizio SAML): incollare il valore ***urn:oasis:names:tc:SAML:2.0:nameid-format:persistent***

    c. **Name of Identity Provider email field in ACS response** (Nome del campo e-mail del provider d'identità nella risposta ACS): incollare il valore `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. **Identity Provider EntityDescriptor Entity ID** (ID entità EntityDescriptor del provider di identità): Incollare il valore di **Identificatore Azure AD**  copiato dal portale di Azure.

    e. **Identity Provider SingleSignOnService URL** (URL servizio Single Sign-On del provider d'identità): Incollare l'**URL di accesso** copiato dal portale di Azure.

    f. **Identity Provider public X509 certificate** (Certificato X509 pubblico provider d'identità): aprire il certificato X509 scaricato da Azure nel blocco note e incollare i contenuti in questa casella. Verificare che non vi siano interruzioni di riga nella parte centrale dei contenuti del certificato.

    g. Selezionare le caselle di controllo seguenti: **Enabled, Encrypt NameID e Sign AuthnRequests** (Abilitato, Esegui crittografia dell'ID nome e Firma richieste di autenticazione).

    h. Fare clic su **Update SSO Settings** (Aggiorna impostazioni SSO) per salvare le impostazioni.

### <a name="create-scalex-enterprise-test-user"></a>Creare l'utente di test di ScaleX Enterprise

Per consentire agli utenti di Azure AD di accedere a ScaleX Enterprise, è necessario effettuarne il provisioning in ScaleX Enterprise. Nel caso di ScaleX Enterprise, il provisioning è un'attività automatica e non sono necessarie procedure manuali. Per tutti gli utenti che eseguono l'autenticazione con le credenziali SSO verrà automaticamente eseguito il provisioning sul lato ScaleX.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di ScaleX Enterprise nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di ScaleX Enterprise per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare ScaleX Enterprise con Azure AD](https://aad.portal.azure.com/)