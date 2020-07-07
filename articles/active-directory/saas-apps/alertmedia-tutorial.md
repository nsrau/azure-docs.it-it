---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con AlertMedia | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e AlertMedia.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5c77f6fc-807c-47e8-8e49-eece5ebd7f09
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/17/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc8a416ac930de24e694107bec4f7ac287e15437
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2020
ms.locfileid: "85608892"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-alertmedia"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con AlertMedia

Questa esercitazione descrive come integrare AlertMedia con Azure Active Directory (Azure AD). Integrando AlertMedia con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere ad AlertMedia.
* Abilitare gli utenti per l'accesso automatico ad AlertMedia con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di AlertMedia abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* AlertMedia supporta l'accesso SSO avviato da **IDP**
* AlertMedia supporta il provisioning utenti **JIT**
* Dopo aver configurato AlertMedia, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-alertmedia-from-the-gallery"></a>Aggiunta di AlertMedia dalla raccolta

Per configurare l'integrazione di AlertMedia in Azure AD, è necessario aggiungere AlertMedia dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **AlertMedia** nella casella di ricerca.
1. Selezionare **AlertMedia** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-alertmedia"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per AlertMedia

Configurare e testare l'accesso SSO di Azure AD con AlertMedia usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in AlertMedia.

Per configurare e testare l'accesso SSO di Azure AD con AlertMedia, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di AlertMedia](#configure-alertmedia-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di AlertMedia](#create-alertmedia-test-user)** : per avere una controparte di B.Simon in AlertMedia collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **AlertMedia** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella pagina **Configura l'accesso Single Sign-On con SAML** immettere i valori per i campi seguenti:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<SUBDOMAIN>.alertmedia.com`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<SUBDOMAIN>.alertmedia.com/api/sso/saml/`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di risposta effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di AlertMedia](mailto:support@alertmedia.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. L'applicazione AlertMedia prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/default-attributes.png)

1. Oltre quelli elencati in precedenza, l'applicazione AlertMedia prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.

| Nome | Attributo di origine|
| ---- | --------------- |
| email | user.userprincipalname |
| firstname | user.givenname |
| lastname | user.surname |

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e salvarlo nel computer.

    ![Collegamento di download del certificato](common/copy-metadataurl.png)

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad AlertMedia.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **AlertMedia**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-alertmedia-sso"></a>Configurare l'accesso Single Sign-On di AlertMedia

1. In una nuova finestra del Web browser accedere al sito aziendale di AlertMedia come amministratore.
1. Passare a **Company** (Società) e selezionare **Single Sign-On**.

    ![Pulsante Account](./media/alertmedia-tutorial/Configure1.png)
1. In **Authentication Method** (Metodo di autenticazione) selezionare **Remote SAML Metadata** (Metadati SAML remoti).
1. Attivare **Sign Request** (Richiesta di firma).
1. Attivare **Allow Passive Requests** (Consenti richieste passive).
1. Nella casella di testo **MetaData URL** (URL dei metadati) incollare il valore dell'**URL dei metadati di federazione dell'app** copiato dal portale di Azure.
1. Impostare **Requested Authentication Context Comparison** (Confronto contesti di autenticazione richiesto) su **exact** (esatto).
1. Nella casella di testo **IDP Login URL** (URL di accesso IDP) incollare il valore di **URL di accesso** copiato dal portale di Azure.
1. Fare clic su **Save** (Salva).

### <a name="create-alertmedia-test-user"></a>Creare l'utente di test di AlertMedia

In questa sezione viene creato un utente di nome Britta Simon in AlertMedia. AlertMedia supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in AlertMedia, ne viene creato uno nuovo dopo l'autenticazione.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di AlertMedia nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di AlertMedia per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare AlertMedia con Azure AD](https://aad.portal.azure.com/)

- [Informazioni sul controllo sessione in Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Come proteggere AlertMedia con visibilità e controlli avanzati](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

