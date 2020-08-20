---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con SiteIntel | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SiteIntel.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/02/2020
ms.author: jeedes
ms.openlocfilehash: f681dd2931300ec00fd6388b4636015c87f38170
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88525076"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-siteintel"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con SiteIntel

Questa esercitazione descrive come integrare SiteIntel con Azure Active Directory (Azure AD). Integrando SiteIntel con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a SiteIntel.
* Abilitare gli utenti per l'accesso automatico a SiteIntel con gli account Azure AD personali.
* Gestire gli account in una posizione centrale, il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS (Software as a Service) con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di SiteIntel abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* SiteIntel supporta l'accesso SSO avviato da SP e da IdP.
* Dopo aver configurato SiteIntel, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-siteintel-from-the-gallery"></a>Aggiungere SiteIntel dalla raccolta

Per configurare l'integrazione di SiteIntel in Azure AD, è necessario aggiungere SiteIntel dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro sinistro selezionare **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella casella **Aggiungi dalla raccolta** immettere **SiteIntel**.
1. Nell'elenco risultati selezionare **SiteIntel** e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-siteintel"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per SiteIntel

Configurare e testare l'accesso SSO di Azure AD con SiteIntel usando un utente di test di nome *B.Simon*. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SiteIntel.

Per configurare e testare l'accesso SSO di Azure AD con SiteIntel, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** per consentire agli utenti di usare questa funzionalità.  

    a. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.  

    b. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per consentire all'utente B.Simon di usare l'accesso Single Sign-On di Azure AD.

1. **[Configurare l'accesso Single Sign-On per SiteIntel](#configure-siteintel-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.

    * **[Creare l'utente di test di SiteIntel](#create-a-siteintel-test-user)** : per avere una controparte di B.Simon in SiteIntel collegata alla rappresentazione dell'utente in Azure AD.

1. **[Testare l'accesso SSO](#test-sso)** per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **SiteIntel** del [portale di Azure](https://portal.azure.com/) passare alla sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** accanto a **Configurazione SAML di base** selezionare **Modifica** (icona della penna).

   ![Screenshot del riquadro "Configura l'accesso Single Sign-On con SAML"](common/edit-urls.png)

1. Per configurare l'applicazione nella modalità avviata da IdP, nella sezione **Configurazione SAML di base** seguire questa procedura:

    a. Nella casella **Identificatore** digitare un URL nel formato seguente: `urn:amazon:cognito:sp:<REGION>_<USERPOOLID>`

    b. Nella casella **URL di risposta** digitare un URL nel formato seguente: `https://<CLIENT>.auth.siteintel.com/saml2/idpresponse`

    c. Nella casella **Stato dell'inoltro** digitare un URL nel formato seguente: `https://<CLIENT>.siteintel.com`

1. Per configurare l'applicazione nella modalità avviata da SP, selezionare **Impostare URL aggiuntivi** e quindi eseguire questa operazione:

   * Nella casella **URL accesso** digitare un URL nel formato seguente: `https://<CLIENT>.siteintel.com`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornare i valori con l'identificatore, l'URL di risposta, l'URL di accesso e lo stato dell'inoltro effettivi. Per ottenere questi valori, contattare il [team di supporto di SiteIntel](mailto:support@intalytics.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** selezionare il pulsante **Copia** per copiare l'URL nella casella **URL dei metadati di federazione dell'app**.

    ![Screenshot del pulsante Copia in "URL dei metadati di federazione dell'app"](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione viene creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory** > **Utenti** > **Tutti gli utenti**.
1. Selezionare **Nuovo utente** nella parte superiore del riquadro.
1. In **Proprietà utente** eseguire le operazioni seguenti:

   a. Nella casella **Nome** immettere **B.Simon**.  

   b. Nella casella **Nome utente** immettere il nome utente nel formato seguente: `username@companydomain.extension`, ad esempio `B.Simon@contoso.com`.

   c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

   d. Selezionare **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abilita l'utente B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a SiteIntel.

1. Nel portale di Azure selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.
1. Nell'elenco **Applicazioni** selezionare **SiteIntel**.
1. Nella sezione **Gestione** della pagina di panoramica dell'app selezionare **Utenti e gruppi**.

   ![Screenshot del collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nel riquadro **Aggiungi assegnazione**.

    ![Screenshot del pulsante "Aggiungi utente"](common/add-assign-user.png)

1. Nel riquadro **Utenti e gruppi** selezionare **B.Simon** e quindi il pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nel riquadro **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi scegliere il pulsante **Seleziona**.
1. Nel riquadro **Aggiungi assegnazione** selezionare il pulsante **Assegna**.

## <a name="configure-siteintel-sso"></a>Configurare l'accesso Single Sign-On per SiteIntel

Per configurare l'accesso Single Sign-On sul lato SiteIntel, inviare l'URL copiato dalla casella **URL dei metadati di federazione dell'app** al [team di supporto di SiteIntel](mailto:support@intalytics.com), che configurerà questo valore in modo che la connessione SSO SAML venga stabilita correttamente su entrambi i lati.

### <a name="create-a-siteintel-test-user"></a>Creare l'utente di test di SiteIntel

In questa sezione viene creato un utente di nome *Britta Simon* in SiteIntel. Collaborare con il  [team di supporto di SiteIntel](mailto:support@intalytics.com) per aggiungere gli utenti alla piattaforma SiteIntel. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si seleziona il riquadro **SiteIntel** nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di SiteIntel per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)
- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Provare SiteIntel con Azure AD](https://aad.portal.azure.com/)
- [Informazioni sul controllo sessioni in Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
- [Come proteggere SiteIntel con visibilità e controlli avanzati](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
