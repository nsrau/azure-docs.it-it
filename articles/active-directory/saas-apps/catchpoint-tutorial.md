---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Catchpoint | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Catchpoint.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ab3eead7-8eb2-4c12-bb3a-0e46ec899d37
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c56d34a331821ffbc3d0d4f2cf5e9b033f4011ff
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "78968448"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-catchpoint"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Catchpoint

Questa esercitazione descrive come integrare Catchpoint con Azure Active Directory (Azure AD). Integrando Catchpoint con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Catchpoint.
* Abilitare gli utenti per l'accesso automatico a Catchpoint con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Catchpoint abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Catchpoint supporta l'accesso SSO avviato da **SP e IDP**
* Catchpoint supporta il provisioning utenti **JIT**
* Dopo aver configurato Catchpoint, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-catchpoint-from-the-gallery"></a>Aggiunta di Catchpoint dalla raccolta

Per configurare l'integrazione di Catchpoint in Azure AD è necessario aggiungere Catchpoint dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Catchpoint** nella casella di ricerca.
1. Selezionare **Catchpoint** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-catchpoint"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Catchpoint

Configurare e testare l'accesso SSO di Azure AD con Catchpoint usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Catchpoint.

Per configurare e testare l'accesso SSO di Azure AD con Catchpoint, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    * **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    * **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Catchpoint](#configure-catchpoint-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    * **[Creare l'utente di test di Catchpoint](#create-catchpoint-test-user)** : per avere una controparte di B.Simon in Catchpoint collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Catchpoint** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Se si vuole configurare l'applicazione in modalità avviata da **IDP**, immettere i valori per i campi seguenti:

    a. Nella casella di testo **Identificatore** digitare l'URL: `https://portal.catchpoint.com/SAML2`

    b. Nella casella di testo **URL di risposta** digitare il valore `https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL di accesso** digitare l'URL: `https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`

1. L'applicazione Catchpoint prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/default-attributes.png)

1. Oltre quelli elencati in precedenza, l'applicazione Catchpoint prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.

    | Nome | Attributo di origine|
    | ------------ | --------- |
    | spazio dei nomi | user.assignedrole |

    > [!NOTE]
    > È necessario eseguire il mapping dell'attestazione di namespace con il nome dell'account. Questo nome dell'account deve essere configurato con i ruoli in Azure AD, che verranno restituiti nella risposta SAML. Per informazioni su come configurare i ruoli, vedere questo [articolo](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management)

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura Catchpoint** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Catchpoint.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Catchpoint**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-catchpoint-sso"></a>Configurare l'accesso Single Sign-On di Catchpoint

1. In un'altra finestra del Web browser accedere all'applicazione Catchpoint come amministratore.

1. Fare clic sull'icona **Settings** (Impostazioni) e selezionare **SSO Identity Provider** (Provider di identità SSO).

    ![Configurazione di Catchpoint](./media/catchpoint-tutorial/configuration1.png)

1. Nella pagina **Single Sign-On** seguire questa procedura:

    ![Configurazione di Catchpoint](./media/catchpoint-tutorial/configuration2.png)

    1. Nella casella di testo **Namespace** (Spazio dei nomi) immettere un valore valido per lo spazio dei nomi.

    1. Nella casella di testo **Identity Provider Issuer** (Autorità di certificazione provider di identità) immettere il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    1. Nella casella di testo **Single Sign-On URL** (URL di accesso SSO) immettere il valore di **URL di accesso** copiato dal portale di Azure.

    1. Aprire il file **Certificato (Base64)** scaricato nel Blocco note, copiare il contenuto del file del certificato e incollarlo nella casella di testo **Certificate** (Certificato).

    1. È anche possibile caricare il file **XML dei metadati di federazione** facendo clic sull'opzione **Upload Metadata** (Carica metadati).

    1. Fare clic su **Salva**.

### <a name="create-catchpoint-test-user"></a>Creare l'utente di test di Catchpoint

In questa sezione viene creato un utente di nome Britta Simon in Catchpoint. Catchpoint supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in Catchpoint, ne viene creato uno nuovo dopo l'autenticazione.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Catchpoint nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Catchpoint per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

> [!NOTE]
> Quando si accede all'applicazione Catchpoint tramite la pagina di accesso, dopo aver specificato le **credenziali di Catchpoint**, immettere il valore valido di **Namespace** (Spazio dei nomi) nella casella di testo **Company Credentials(SSO)** (Credenziali aziendali - SSO) e fare clic su **Login** (Accedi).

![Configurazione di Catchpoint](./media/catchpoint-tutorial/loginimage.png)

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare Catchpoint con Azure AD](https://aad.portal.azure.com/)

- [Informazioni sul controllo sessioni in Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)