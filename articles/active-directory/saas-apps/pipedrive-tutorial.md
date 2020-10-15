---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Pipedrive | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Pipedrive.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/06/2020
ms.author: jeedes
ms.openlocfilehash: b3b2032d8cefe881e59fe339786877c4f03c9305
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88553785"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pipedrive"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Pipedrive

Questa esercitazione descrive come integrare Pipedrive con Azure Active Directory (Azure AD). Integrando Pipedrive con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Pipedrive.
* Abilitare gli utenti per l'accesso automatico a Pipedrive con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Pipedrive abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Pipedrive supporta l'accesso SSO avviato da **SP e IDP**
* Dopo aver configurato Pipedrive SSO, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


## <a name="adding-pipedrive-from-the-gallery"></a>Aggiunta di Pipedrive dalla raccolta

Per configurare l'integrazione di Pipedrive in Azure AD, è necessario aggiungere Pipedrive dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Pipedrive** nella casella di ricerca.
1. Selezionare **Pipedrive** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-pipedrive"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Pipedrive

Configurare e testare l'accesso SSO di Azure AD con Pipedrive usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Pipedrive.

Per configurare e testare l'accesso SSO di Azure AD con Pipedrive, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    * **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    * **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Pipedrive](#configure-pipedrive-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    * **[Creare l'utente di test di Pipedrive](#create-pipedrive-test-user)** : per avere una controparte di B.Simon in Pipedrive collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Pipedrive** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<COMPANY-NAME>.pipedrive.com/sso/auth/samlp/metadata.xml`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<COMPANY-NAME>.pipedrive.com/sso/auth/samlp`

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<COMPANY-NAME>.pipedrive.com/`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Pipedrive](mailto:support@pipedrive.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. L'applicazione Pipedrive prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/default-attributes.png)

1. Oltre quelli elencati in precedenza, l'applicazione Pipedrive prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.

    | Nome | Attributo di origine|
    | ------------ | --------- |
    | email | user.mail |

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il **certificato (Base64)** e quindi selezionare **Scarica** per scaricare il certificato e salvarlo nel computer. Copiare anche l'**URL dei metadati di federazione dell'app** e salvarlo nel computer.

    ![Collegamento di download del certificato](./media/pipedrive-tutorial/certificate-data.png)

1. Nella sezione **Configura Pipedrive** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Pipedrive.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Pipedrive**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-pipedrive-sso"></a>Configurare l'accesso Single Sign-On di Pipedrive

1. In un'altra finestra del browser accedere al sito Web di Pipedrive come amministratore.

1. Fare clic su **User Profile** (Profilo utente) e selezionare **Settings** (Impostazioni).

    ![Configurazione di Pipedrive](./media/pipedrive-tutorial/configure1.png)

1. Scorrere verso il basso fino al centro sicurezza e selezionare **Single sign-on**.

    ![Configurazione di Pipedrive](./media/pipedrive-tutorial/configure2.png)

1. Nella sezione **SAML configuration for pipedrive** (Configurazione SAML per Pipedrive) seguire questa procedura:

    ![Configurazione di Pipedrive](./media/pipedrive-tutorial/configure3.png)

    a. Nella casella di testo **Issuer** (Autorità di certificazione) incollare il valore dell'**URL dei metadati di federazione dell'app** copiato dal portale di Azure.

    b. Nella casella di testo **Single Sign On (SSO) url** (URL di accesso SSO) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    c. Nella casella di testo **Single Log Out (SLO) url** (URL di disconnessione) incollare il valore di **URL disconnessione** copiato dal portale di Azure.

    d. Nella casella di testo **x.509 certificate** (**Certificato x.509**) aprire nel Blocco note il file del **certificato (Base64)** scaricato dal portale di Azure, copiarne il contenuto e quindi incollarlo nella casella di testo, infine salvare le modifiche.

### <a name="create-pipedrive-test-user"></a>Creare l'utente di test di Pipedrive

1. In un'altra finestra del browser accedere al sito Web di Pipedrive come amministratore.

1. Scorrere verso il basso fino a company (società) e selezionare **manage users** (gestisci utenti).

    ![Configurazione di Pipedrive](./media/pipedrive-tutorial/user1.png)

1. Fare clic su **Add users** (Aggiungi utenti).
    
    ![Configurazione di Pipedrive](./media/pipedrive-tutorial/user2.png)

1. Nella sezione **Manage users** (Gestisci utenti) seguire questa procedura:

    ![Configurazione di Pipedrive](./media/pipedrive-tutorial/user3.png)

    a. Nella casella di testo **Email** (Posta elettronica) immettere l'indirizzo di posta elettronica dell'utente, ad esempio `B.Simon@contoso.com`.

    b. Nella casella di testo **First name** (Nome) immettere il nome dell'utente.

    c. Nella casella di testo **Last name** (Cognome) immettere il cognome dell'utente.

    d. Fare clic su **Confirm and invite users** (Conferma e invita utenti).

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Pipedrive nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Pipedrive per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare Pipedrive con Azure AD](https://aad.portal.azure.com/)

- [Informazioni sul controllo sessioni in Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)