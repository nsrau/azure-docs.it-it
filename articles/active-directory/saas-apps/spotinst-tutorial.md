---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Spotinst | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Spotinst.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/03/2020
ms.author: jeedes
ms.openlocfilehash: d9c9ac87e655cf9178d58f04b502f7e781d1fef7
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96181408"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-spotinst"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Spotinst

Questa esercitazione descrive come integrare Spotinst con Azure Active Directory (Azure AD). Integrando Spotinst con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Spotinst.
* Abilitare gli utenti per l'accesso automatico a Spotinst con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Spotinst abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Spotinst supporta l'accesso SSO avviato da **SP e IDP**

## <a name="adding-spotinst-from-the-gallery"></a>Aggiunta di Spotinst dalla raccolta

Per configurare l'integrazione di Spotinst in Azure AD, è necessario aggiungere Spotinst dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Spotinst** nella casella di ricerca.
1. Selezionare **Spotinst** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-spotinst"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Spotinst

Configurare e testare l'accesso SSO di Azure AD con Spotinst usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Spotinst.

Per configurare e testare l'accesso SSO di Azure AD con Spotinst, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    * **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    * **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Spotinst](#configure-spotinst-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    * **[Creare l'utente di test di Spotinst](#create-spotinst-test-user)** : per avere una controparte di B.Simon in Spotinst collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Spotinst** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** completare questa procedura se si vuole configurare l'applicazione nella modalità avviata da IDP:

   1. Assicurarsi che **URL di risposta** sia impostato su: https://console.spotinst.com/auth/saml.
   1. In **Stato dell'inoltro** immettere l'ID organizzazione di Spotinst, che è possibile confermare anche nella scheda **SSO**.
   1. Il campo **URL di accesso** deve essere vuoto.

1. Fare clic su **Salva**.

1. L'applicazione Spotinst prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/default-attributes.png)

1. Oltre a quelli elencati in precedenza, l'applicazione Spotinst prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.

    | Nome | Attributo di origine|
    | -----| --------------- |
    | Email | user.mail |
    | FirstName | user.givenname |
    | LastName | user.surname |

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il file **XML dei metadati della federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura Spotinst** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Spotinst.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco di applicazioni selezionare **Spotinst**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-spotinst-sso"></a>Configurare l'accesso Single Sign-On di Spotinst

1. In un'altra finestra del Web browser accedere a Spotinst come amministratore della sicurezza.

2. Fare clic sull'**icona utente** in alto a destra nella schermata e fare clic su **Settings** (Impostazioni).

    ![Screenshot che mostra l'opzione Settings selezionata dall'icona dell'utente.](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

3. Fare clic sulla scheda **SECURITY** (SICUREZZA) nella parte superiore e quindi selezionare **Identity Providers** (Provider di identità) e seguire questa procedura:

    ![Sicurezza di Spotinst](./media/spotinst-tutorial/tutorial_spotinst_security.png)

    a. Copiare il valore di **Relay State** (Stato dell'inoltro) per l'istanza e incollarlo nella casella di testo **Stato dell'inoltro** della sezione **Configurazione SAML di base** del portale di Azure.

    b. Fare clic su **BROWSE** (SFOGLIA) per caricare il file XML di metadati scaricato dal portale di Azure

    c. Fare clic su **SALVA**.

### <a name="create-spotinst-test-user"></a>Creare un utente di test di Spotinst

Questa sezione descrive come creare un utente chiamato Britta Simon in Spotinst.

1. Se l'applicazione è stata configurata in modalità avviata da **SP**, seguire questa procedura:

   a. In un'altra finestra del Web browser accedere a Spotinst come amministratore della sicurezza.

   b. Fare clic sull'**icona utente** in alto a destra nella schermata e fare clic su **Settings** (Impostazioni).

    ![Screenshot che mostra l'opzione Settings selezionata dall'icona dell'utente.](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

    c. Fare clic su **Users** (Utenti) e selezionare **ADD USER** (AGGIUNGI UTENTE).

    ![Screenshot che mostra l'opzione ADD USER selezionata in Users.](./media/spotinst-tutorial/adduser1.png)

    d. Nella sezione Add User (Aggiungi utente) seguire questa procedura:

    ![Screenshot che mostra la sezione Add User in cui è possibile immettere i valori descritti.](./media/spotinst-tutorial/adduser2.png)

    * Nella casella di testo **Full Name** (Nome completo) immettere nome e cognome dell'utente, ad esempio **BrittaSimon**.

    * Nella casella di testo **Email** (Posta elettronica) immettere l'indirizzo di posta elettronica dell'utente, ad esempio `brittasimon\@contoso.com`.

    * Selezionare i dettagli specifici dell'organizzazione per **Organization Role, Account Role e Accounts** (Ruolo organizzazione, Ruolo account e Account).

2. Se l'applicazione è stata configurata nella modalità avviata da **IDP**, non è necessario alcun intervento dell'utente in questa sezione. Spotinst supporta il provisioning JIT, che è abilitato per impostazione predefinita. Se non esiste già, un nuovo utente viene creato durante un tentativo di accesso a Spotinst.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Spotinst nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Spotinst per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](./tutorial-list.md)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)

- [Provare Spotinst con Azure AD](https://aad.portal.azure.com/)