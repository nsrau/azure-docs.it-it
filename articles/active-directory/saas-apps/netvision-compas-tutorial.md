---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Netvision Compas | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Netvision Compas.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a56f44c5-dc08-4c7c-ad20-b6e7127deb2c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3015ea26d81505c4f058846dbcb3b7858f79267
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520185"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netvision-compas"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Netvision Compas

Questa esercitazione descrive come integrare Netvision Compas con Azure Active Directory (Azure AD). Integrando Netvision Compas con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Netvision Compas.
* Abilitare gli utenti per l'accesso automatico a Netvision Compas con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Netvision Compas abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Netvision Compas supporta l'accesso SSO avviato da **SP e IDP**
* Dopo aver configurato Netvision Compas, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)


## <a name="adding-netvision-compas-from-the-gallery"></a>Aggiunta di Netvision Compas dalla raccolta

Per configurare l'integrazione di Netvision Compas in Azure AD, è necessario aggiungere Netvision Compas dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Netvision Compas** nella casella di ricerca.
1. Selezionare **Netvision Compas** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-netvision-compas"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Netvision Compas

Configurare e testare l'accesso SSO di Azure AD con Netvision Compas usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Netvision Compas.

Per configurare e testare l'accesso SSO di Azure AD con Netvision Compas, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Netvision Compas](#configure-netvision-compas-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Configurare l'utente di test di Netvision Compas](#configure-netvision-compas-test-user)** : per avere una controparte di B.Simon in Netvision Compas collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Netvision Compas** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<TENANT>.compas.cloud/Identity/Saml20`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<TENANT>.compas.cloud/Identity/Auth/AssertionConsumerService`

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<TENANT>.compas.cloud/Identity/Auth/AssertionConsumerService`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Netvision Compas](mailto:contact@net.vision). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **XML metadati federazione** e selezionare **Scarica** per scaricare il file dei metadati e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/metadataxml.png)



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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Netvision Compas.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Netvision Compas**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-netvision-compas-sso"></a>Configurare l'accesso Single Sign-On di Netvision Compas

In questa sezione si abilita l'accesso SSO SAML in **Netvision Compas**.
1. Accedere a **Netvision Compas** con un account di amministratore e selezionare il pannello di amministrazione.

    ![Pannello di amministrazione](media/netvision-compas-tutorial/admin.png)

1. Individuare l'area **System** (Sistema) e selezionare **Identity Providers** (Provider di identità).

    ![Opzione Identity Providers](media/netvision-compas-tutorial/admin-idps.png)

1. Selezionare l'azione **Add** (Aggiungi) per registrare Azure AD come nuovo provider di identità.

    ![Aggiunta del provider di identità](media/netvision-compas-tutorial/idps-add.png)

1. Selezionare **SAML** come valore di **Provider type** (Tipo di provider).
1. Immettere valori significativi per i campi **Display name** (Nome visualizzato) e **Description** (Descrizione).
1. Assegnare gli utenti di **Netvision Compas** al provider di identità selezionandoli nell'elenco **Available users** (Utenti disponibili) e quindi facendo clic sul pulsante **Add selected** (Aggiungi selezionati). È anche possibile assegnare gli utenti al provider di identità durante la procedura di provisioning.
1. Per l'opzione SAML **Metadata** (Metadati) fare clic sul pulsante **Choose File** (Scegli file) e selezionare il file dei metadati salvato in precedenza nel computer.
1. Fare clic su **Save** (Salva).

    ![Modifica del provider di identità](media/netvision-compas-tutorial/idp-edit.png)


### <a name="configure-netvision-compas-test-user"></a>Configurare l'utente di test di Netvision Compas

In questa sezione si configura un utente esistente in **Netvision Compas** per usare Azure AD per l'accesso SSO.
1. Seguire la procedura di provisioning utenti di **Netvision Compas** definita dall'azienda o modificare un account utente esistente.
1. Durante la definizione del profilo dell'utente, assicurarsi che l'indirizzo **Email (Personal)** (Posta elettronica personale) dell'utente corrisponda al nome utente in Azure AD: username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.

    ![Modificare l'utente](media/netvision-compas-tutorial/user-config.png)

Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD.

### <a name="using-the-access-panel-idp-initiated"></a>Uso del pannello di accesso (avviato dal provider di identità).

Quando si fa clic sul riquadro di Netvision Compas nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Netvision Compas per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

### <a name="directly-accessing-netvision-compas-sp-initiated"></a>Accesso diretto a Netvision Compas (avviato da provider di servizi).

1. Accedere all'URL di **Netvision Compas**. Ad esempio: `https://tenant.compas.cloud`.
1. Immettere il nome utente di **Netvision Compas** e selezionare **Next** (Avanti).

    ![Utente di accesso](media/netvision-compas-tutorial/login-user.png)

1. **(Facoltativo)** Se all'utente sono assegnati più provider di identità in **Netvision Compas**, viene visualizzato un elenco di provider di identità disponibili. Selezionare il provider di identità di Azure AD configurato in precedenza in **Netvision Compas**.

    ![Scelta dell'accesso](media/netvision-compas-tutorial/login-choose.png)

1. Si viene reindirizzati a Azure AD per eseguire l'autenticazione. Dopo aver eseguito l'autenticazione, si dovrebbe accedere automaticamente all'istanza di **Netvision Compas** per cui si è configurato l'accesso SSO.

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare Netvision Compas con Azure AD](https://aad.portal.azure.com/)

- [Informazioni sul controllo sessioni in Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
