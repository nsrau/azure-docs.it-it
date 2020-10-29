---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Trelica | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Trelica.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: jeedes
ms.openlocfilehash: a674f5f653ad420ab8f28ff73c6b86f9c18b154e
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92517753"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trelica"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Trelica

Questa esercitazione descrive come integrare Trelica con Azure Active Directory (Azure AD).

Grazie a questa integrazione, è possibile:

* Controllare in Azure AD chi può accedere a Trelica.
* Abilitare gli utenti per l'accesso automatico a Trelica con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS (Software as a Service) con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Una sottoscrizione di Trelica abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Trelica supporta l'accesso SSO avviato da IDP.
* Trelica supporta il provisioning utenti JIT.
* Dopo aver configurato Trelica, è possibile applicare il controllo sessione, che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-trelica-from-the-gallery"></a>Aggiunta di Trelica dalla raccolta

Per configurare l'integrazione di Trelica in Azure AD, è necessario aggiungere Trelica dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento all'estrema sinistra selezionare il servizio **Azure Active Directory** .
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni** .
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione** .
1. Nella sezione **Aggiungi dalla raccolta** immettere **Trelica** nella casella di ricerca.
1. Selezionare **Trelica** nei risultati della ricerca e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-trelica"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Trelica

Configurare e testare l'accesso SSO di Azure AD con Trelica usando un utente di test di nome **B.Simon** . Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Trelica.

Per configurare e testare l'accesso SSO di Azure AD con Trelica, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** per consentire a B.Simon di usare l'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Trelica](#configure-trelica-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Trelica](#create-a-trelica-test-user)** : per avere una controparte di B.Simon in Trelica. Tale controparte è collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Trelica** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** . Selezionare **Single Sign-On** .
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML** .
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona di matita relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Pagina Configura l'accesso Single Sign-On con SAML con l'icona della matita relativa a Configurazione SAML di base evidenziata](common/edit-urls.png)

1. Nella pagina **Configura l'accesso Single Sign-On con SAML** immettere i valori seguenti:

    1. Nella casella **Identificatore** immettere l'URL **https://app.trelica.com** .

    1. Nella casella **URL di risposta** immettere un URL nel formato `https://app.trelica.com/Id/Saml2/<CUSTOM_IDENTIFIER>/Acs`.

    > [!NOTE]
    > Il valore di URL di risposta non è reale. Aggiornarlo con l'URL di risposta effettivo, noto anche come ACS.
    > Per trovarlo, accedere a Trelica e passare alla [pagina di configurazione dei provider di identità SAML](https://app.trelica.com/Admin/Profile/SAML) facendo clic su Admin > Account > SAML (Amministrazione > Account > SAML). Fare clic sul pulsante di copia accanto a **Assertion Consumer Service (ACS) URL** (URL servizio consumer di asserzione) per inserirlo negli Appunti, pronto per essere incollato nella casella di testo **URL di risposta** in Azure AD.
    > Per eventuali domande, vedere la [documentazione della guida di Trelica](https://docs.trelica.com/admin/saml/azure-ad) o contattare il [team di supporto clienti di Trelica](mailto:support@trelica.com).

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante Copia per copiare l' **URL dei metadati di federazione dell'app** e salvarlo nel computer in uso.

    ![Sezione Certificato di firma SAML con il pulsante di copia evidenziato accanto a URL dei metadati di federazione dell'app](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro all'estrema sinistra del portale di Azure selezionare **Azure Active Directory** > **Utenti** > **Tutti gli utenti** .
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere **B. Simon** .
   1. Nel campo **Nome utente** immettere **B.Simon@** _dominio_aziendale_ **.** _estensione_ . Ad esempio: B.Simon@contoso.com.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password** .
   1. Selezionare **Crea** .

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Trelica.

1. Nel portale di Azure selezionare **Applicazioni aziendali** > **Tutte le applicazioni** .
1. Nell'elenco delle applicazioni selezionare **Trelica** .
1. Nella pagina di panoramica dell'app passare alla sezione **Gestione** e selezionare **Utenti e gruppi** .

   ![Sezione Gestione con opzione Utenti e gruppi evidenziata](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** . Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Utenti e gruppi** .

   ![Finestra Utenti e gruppi con l'opzione Aggiungi utente evidenziata](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** nell'elenco degli utenti. Fare quindi clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco. Fare quindi clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Assegna** .

## <a name="configure-trelica-sso"></a>Configurare l'accesso Single Sign-On di Trelica

Per configurare l'accesso Single Sign-On sul lato **Trelica** , passare alla [pagina di configurazione dei provider di identità SAML](https://app.trelica.com/Admin/Profile/SAML) facendo clic su Admin > Account > SAML (Amministrazione > Account > SAML). Fare clic sul pulsante **New** (Nuovo). Immettere **Azure AD** come nome e scegliere **Metadata from url** (Metadati da URL) come tipo di metadati. Incollare l' **URL dei metadati di federazione dell'app** copiato da Azure AD nel campo **Metadata url** (URL metadati) in Trelica.

Per eventuali domande, vedere la [documentazione della guida di Trelica](https://docs.trelica.com/admin/saml/azure-ad) o contattare il [team di supporto clienti di Trelica](mailto:support@trelica.com).

### <a name="create-a-trelica-test-user"></a>Creare l'utente di test di Trelica

Trelica supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. In questa sezione non è necessario alcun intervento da parte dell'utente. Se non esiste già un utente in Trelica, ne viene creato uno nuovo dopo l'autenticazione.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD tramite il portale App personali.

Quando si seleziona il riquadro di Trelica nel portale App personali, si accede automaticamente all'istanza di Trelica per cui si è configurato l'accesso SSO. Per altre informazioni sul portale App personali, vedere l'[introduzione al portale App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Esercitazioni per l'integrazione di app SaaS con Azure Active Directory](./tutorial-list.md)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)

- [Provare Trelica con Azure AD](https://aad.portal.azure.com/)

- [Informazioni sul controllo sessioni in Microsoft Cloud App Security](/cloud-app-security/proxy-intro-aad)

- [Come proteggere Trelica con visibilità e controlli avanzati](/cloud-app-security/proxy-intro-aad)