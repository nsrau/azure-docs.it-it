---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con AskYourTeam | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e AskYourTeam.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/28/2020
ms.author: jeedes
ms.openlocfilehash: 5d17d4769825bdf377c2dbe9892ceba429beabb7
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92457623"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-askyourteam"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con AskYourTeam

Questa esercitazione descrive come integrare AskYourTeam con Azure Active Directory (Azure AD). Integrando AskYourTeam con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere ad AskYourTeam.
* Abilitare gli utenti per l'accesso automatico ad AskYourTeam con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di AskYourTeam abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* AskYourTeam supporta l'accesso SSO avviato da **SP e IDP** .
* Dopo aver configurato AskYourTeam, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-askyourteam-from-the-gallery"></a>Aggiunta di AskYourTeam dalla raccolta

Per configurare l'integrazione di AskYourTeam in Azure AD è necessario aggiungere AskYourTeam dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory** .
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni** .
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione** .
1. Nella sezione **Aggiungi dalla raccolta** digitare **AskYourTeam** nella casella di ricerca.
1. Selezionare **AskYourTeam** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-askyourteam"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per AskYourTeam

Configurare e testare l'accesso SSO di Azure AD con AskYourTeam usando un utente di test di nome **B.Simon** . Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in AskYourTeam.

Per configurare e testare l'accesso SSO di Azure AD con AskYourTeam, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    * **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    * **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di AskYourTeam](#configure-askyourteam-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    * **[Creare l'utente di test di AskYourTeam](#create-askyourteam-test-user)** : per avere una controparte di B.Simon in AskYourTeam collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **AskYourTeam** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On** .
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML** .
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP** :

    Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<COMPANY>.app.askyourteam.com/users/auth/saml/callback`

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP** :

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<COMPANY>.app.askyourteam.com/login`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornare tali valori con i valori reali di URL di risposta e URL di accesso. La procedura viene descritta più avanti nell'esercitazione.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura AskYourTeam** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a AskYourTeam.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni** .
1. Nell'elenco delle applicazioni selezionare **AskYourTeam** .
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi** .

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione** .

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna** .

## <a name="configure-askyourteam-sso"></a>Configurare l'accesso Single Sign-On di AskYourTeam

1. In un'altra finestra del Web browser accedere al sito Web di AskYourTeam come amministratore.

1. Fare clic su **My Organisation** (La mia organizzazione).

    ![Screenshot che mostra il collegamento My Organisation.](./media/askyourteam-tutorial/user1.png)

1. Fare clic su **Integrations** (Integrazioni).

    ![Screenshot che mostra il collegamento Integrations.](./media/askyourteam-tutorial/configure1.png)

1. Fare clic su **Edit Settings** (Modifica impostazioni).

    ![Screenshot che mostra il messaggio Single Sign-On con un pulsante Edit Settings.](./media/askyourteam-tutorial/configure2.png)

1. Nella pagina **Edit Single Sign-On Integration** (Modifica integrazione per l'accesso Single Sign-On) seguire questa procedura: 

    ![Screenshot che mostra la schermata Edit Single Sign-On Integration in cui è possibile immettere i valori per questo passaggio.](./media/askyourteam-tutorial/configure3.png)

    a. Nella casella di testo **SAML Single Sign-On Service URL** (URL del servizio Single Sign-On SAML) incollare il valore di **Login URL** (URL di accesso) copiato dal portale di Azure.

    b. Nella casella di testo **SAML Entity ID** (ID entità SAML) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    c. Nella casella di testo **Sign-Out URL** (URL di disconnessione) incollare il valore di **URL disconnessione** copiato dal portale di Azure.

    d. Aprire nel Blocco note il file del **certificato (Base64)** scaricato dal portale di Azure e incollarne il contenuto nella casella **SAML Signing Certificate - Base64** (Certificato di firma SAML - Base64).

    > [!NOTE]
    > In alternativa, è anche possibile caricare il file **XML dei metadati di federazione** facendo clic sull'opzione **Choose File** (Scegli file).

    e. Copiare il valore di **Reply URL (Assertion Consumer Service URL)** (URL di risposta (URL del servizio consumer di asserzione)) e incollarlo nella casella di testo **URL di risposta** nella sezione **Configurazione SAML di base** del portale di Azure.

    f. Copiare il valore di **Sign on URL** (URL di accesso) e incollarlo nella casella di testo **URL di accesso** nella sezione **Configurazione SAML di base** del portale di Azure.

    g. Fare clic su **Salva** .

### <a name="create-askyourteam-test-user"></a>Creare l'utente di test di AskYourTeam

1. In un'altra finestra del Web browser accedere al sito Web di AskYourTeam come amministratore.

1. Fare clic su **My Organisation** (La mia organizzazione).

    ![Screenshot che mostra il collegamento My Organisation che consente di avviare questa attività.](./media/askyourteam-tutorial/user1.png)

1. Fare clic su **Users** (Utenti) e selezionare **New User** (Nuovo utente).

    ![Screenshot che mostra il collegamento Users con il pulsante New User.](./media/askyourteam-tutorial/user2.png)

1. Nella sezione **New user** (Nuovo utente) seguire questa procedura:

    ![Screenshot che mostra un sezione New User in cui immettere le informazioni utente.](./media/askyourteam-tutorial/user3.png)

    1. Nella casella di testo **First name** (Nome) immettere il nome dell'utente.

    1. Nella casella di testo **Last name** (Cognome) immettere il cognome dell'utente.

    1. Nella casella di testo **Email** (Posta elettronica) immettere l'indirizzo di posta elettronica dell'utente, ad esempio B.Simon@contoso.com.

    1. Selezionare il **ruolo** per l'utente in base ai requisiti dell'organizzazione.

    1. Fare clic su **Salva** .

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di AskYourTeam nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di AskYourTeam per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](./tutorial-list.md)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)

- [Provare AskYourTeam con Azure AD](https://aad.portal.azure.com/)

- [Informazioni sul controllo sessioni in Microsoft Cloud App Security](/cloud-app-security/proxy-intro-aad)