---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Segment | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Segment.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/18/2020
ms.author: jeedes
ms.openlocfilehash: fe8acfd1bfd14f339a0109cab215b8a9ab65256f
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92893880"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-segment"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Segment

Questa esercitazione descrive come integrare Segment con Azure Active Directory (Azure AD). Integrando Segment con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Segment.
* Abilitare gli utenti per l'accesso automatico a Segment con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Segment abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Segment supporta l'accesso SSO avviato da **SP e IDP**
* Segment supporta il provisioning utenti **JIT**

* Dopo aver configurato Segment, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-segment-from-the-gallery"></a>Aggiunta di Segment dalla raccolta

Per configurare l'integrazione di Segment in Azure AD, è necessario aggiungere Segment dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Segment** nella casella di ricerca.
1. Selezionare **Segment** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-segment"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Segment

Configurare e testare l'accesso SSO di Azure AD con Segment usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Segment.

Per configurare e testare l'accesso SSO di Azure AD con Segment, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Segment](#configure-segment-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Segment](#create-segment-test-user)** : per avere una controparte di B.Simon in Segment collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Segment** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP** :

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `urn:auth0:segment-prod:samlp-<CUSTOMER_VALUE>`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://segment-prod.auth0.com/login/callback?connection=<CUSTOMER_VALUE>`

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP** :

    Nella casella di testo **URL di accesso** digitare l'URL: `https://app.segment.com`

    > [!NOTE]
    > Questi valori sono segnaposto. È necessario usare l'identificatore, l'URL di risposta e l'URL di accesso effettivi. La procedura da eseguire per ottenere questi valori è descritta più avanti in questa esercitazione.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura Segment** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory** , **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Segment.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Segment**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-segment-sso"></a>Configura l'accesso Single Sign-On di Segment

1. In una nuova finestra del Web browser accedere al sito aziendale di Segment come amministratore.

1. Fare clic sull'icona **Settings** (Impostazioni), scorrere verso il basso fino alla voce **AUTHENTICATION** (AUTENTICAZIONE) e fare clic su **Connections** (Connessioni).

    ![Screenshot che mostra l'icona "Settings" selezionata e l'opzione "Connections" selezionata nel menu "Authentication".](./media/segment-tutorial/segment1.PNG)

1. Fare clic su **Add new Connection** (Aggiungi nuova connessione).

    ![Screenshot che mostra la sezione "Connections" con il pulsante "Add new Connection" selezionato.](./media/segment-tutorial/segment2.PNG)

1. Selezionare **SAML 2.0** come connessione da configurare e fare clic sul pulsante **Select Connection** (Seleziona connessione).

    ![Screenshot che mostra la sezione "Choose a Connection" con il pulsante l'opzione "SAML 2.0" e il pulsante "Select Connection" selezionati.](./media/segment-tutorial/segment3.PNG)

1. Nella pagina successiva seguire questa procedura:

    ![Screenshot che mostra la pagina "Configure Identity Provider" con le caselle di testo "Single Sign-On URL" e "Audience URL" evidenziate e il pulsante "Avanti" selezionato.](./media/segment-tutorial/segment4.PNG)

    a. Copiare il valore di **Single Sign-On URL** (URL di accesso SSO) e incollarlo nella casella **URL di risposta** nella finestra di dialogo **Configurazione SAML di base** del portale di Azure.

    b. Copiare il valore di ****Audience URL**** (URL destinatari) e incollarlo nella casella dell' **URL identificatore** nella finestra di dialogo **Configurazione SAML di base** del portale di Azure.

    c. Fare clic su **Avanti**.

    ![Configurazione di Segment](./media/segment-tutorial/segment5.PNG)

1. Nella casella **SAML 2.0 Endpoint URL** (URL endpoint SAML 2.0) incollare il valore di **URL di accesso** copiato dal portale di Azure.

1. Aprire nel Blocco note il file del **certificato (Base64)** scaricato dal portale di Azure e incollarne il contenuto nella casella di testo **Public Certificate** (Certificato pubblico).

1. Fare clic su **Configure Connection** (Configura connessione).

### <a name="create-segment-test-user"></a>Creare l'utente di test di Segment

In questa sezione viene creato un utente di nome B.Simon in Segment. Segment supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in Segment, ne viene creato uno nuovo dopo l'autenticazione.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Segment nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Segment per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](./tutorial-list.md)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)

- [Provare Segment con Azure AD](https://aad.portal.azure.com/)

- [Informazioni sul controllo sessioni in Microsoft Cloud App Security](/cloud-app-security/proxy-intro-aad)

- [Come proteggere Segment con visibilità e controlli avanzati](/cloud-app-security/proxy-intro-aad)