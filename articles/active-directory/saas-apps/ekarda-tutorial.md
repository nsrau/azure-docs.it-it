---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con ekarda | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e ekarda.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/15/2020
ms.author: jeedes
ms.openlocfilehash: cfd7a007c8f26a96d929026e231cb412cdc70279
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89440865"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ekarda"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con ekarda

Questa esercitazione descrive come integrare ekarda con Azure Active Directory (Azure AD). Integrando ekarda con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a ekarda.
* Abilitare gli utenti per l'accesso automatico a ekarda con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS (Software as a Service) con Azure AD, vedere [Informazioni sull'accesso Single Sign-On](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di ekarda abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* ekarda supporta l'accesso SSO avviato da SP e IDP.
* ekarda supporta il provisioning utenti JIT.
* Dopo aver configurato ekarda, è possibile applicare il controllo sessione. Questa precauzione consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione del Controllo app per l'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-ekarda-from-the-gallery"></a>Aggiungere ekarda dalla raccolta

Per configurare l'integrazione di ekarda in Azure AD, aggiungere ekarda dalla raccolta all'elenco di app SaaS gestite:

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.

1. Nel riquadro sinistro selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **ekarda** nella casella di ricerca.
1. Selezionare **ekarda** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ekarda"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per ekarda

Configurare e testare l'accesso SSO di Azure AD con ekarda usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in ekarda.

Per configurare e testare l'accesso SSO di Azure AD con ekarda, seguire questa procedura:

1. [Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso): per consentire agli utenti di usare questa funzionalità.

    1. [Creare un utente di test di Azure AD](#create-an-azure-ad-test-user) per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. [Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user) per consentire a B.Simon di usare l'accesso Single Sign-On di Azure AD.
1. [Configurare l'accesso Single Sign-On di ekarda](#configure-ekarda-sso) per configurare le impostazioni di Single Sign-On sul lato applicazione.
    * [Creare un utente di test di ekarda](#create-an-ekarda-test-user) per avere una controparte di B.Simon in ekarda collegata alla rappresentazione dell'utente in Azure AD.
1. [Testare l'accesso Single Sign-On](#test-sso): per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD, seguire questa procedura nel portale di Azure:

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Nella pagina di integrazione dell'applicazione **ekarda** individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Screenshot della pagina Configura l'accesso Single Sign-On con SAML, in cui è evidenziata l'icona della matita.](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base**, se viene visualizzato un **file di metadati di un provider di servizi**, seguire questa procedura:
    1. Selezionare **Carica il file di metadati**.
    1. Per selezionare il file di metadati, selezionare l'icona della cartella e quindi **Carica**.
    1. Dopo aver caricato il file di metadati, i valori di **Identificatore** e **URL di risposta** vengono visualizzati automaticamente nella casella di testo della sezione relativa a ekarda.

    > [!Note]
    > Se i valori di **Identificatore** e **URL di risposta** non vengono visualizzati automaticamente, specificarli manualmente in base alle esigenze.

1. Se non viene visualizzato alcun **file di metadati del provider di servizi** nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da IDP:

    1. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://my.ekarda.com/users/saml_metadata/<COMPANY_ID>`
    1. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://my.ekarda.com/users/saml_acs/<COMPANY_ID>`

1. Se si vuole configurare l'applicazione in modalità avviata da SP, selezionare **Impostare URL aggiuntivi** e seguire questa procedura:

    * Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://my.ekarda.com/users/saml_sso/<COMPANY_ID>`

    > [!NOTE]
    > I valori nei due passaggi precedenti non sono reali. Aggiornarli con i valori effettivi di identificatore, URL di risposta e URL di accesso. Per ottenere questi valori, contattare il [team di supporto clienti di ekarda](mailto:contact@ekarda.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** selezionare **Scarica** per salvare il **certificato (Base64)** nel computer.

    ![Screenshot della sezione Certificato di firma SAML della pagina Configura accesso Single Sign-on con SAML con il collegamento di download evidenziato per il certificato Base64.](common/certificatebase64.png)

1. Nella sezione **Configura ekarda** copiare gli URL appropriati in base alle esigenze.

    ![Screenshot della sezione Configura ekarda della pagina Configura l'accesso Single Sign-On con SAML con i collegamenti di copia degli URL evidenziati.](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà usato il portale di Azure per creare un utente di test di nome B.Simon.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory** > **Utenti** > **Tutti gli utenti**.

1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Immettere ad esempio `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e prendere nota del valore visualizzato nella casella **Password**.
   1. Selezionare **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a ekarda.

1. Nel portale di Azure selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **ekarda**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Screenshot della sezione Gestione con Utenti e gruppi evidenziato.](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Screenshot della sezione Utenti e gruppi con il pulsante Aggiungi utente evidenziato.](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** nell'elenco degli utenti. Scegliere quindi **Seleziona** nella parte inferiore della schermata.
1. Se si prevede di usare un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco. Scegliere quindi **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Assegna**.

## <a name="configure-ekarda-sso"></a>Configurare l'accesso Single Sign-On di ekarda

1. In un'altra finestra del Web browser accedere al sito aziendale di ekarda come amministratore.
1. Selezionare **Admin** > **My Account** (Amministratore > Account personale).

    ![Screenshot dell'interfaccia utente del sito di ekarda con l'opzione My Account evidenziata nel menu Admin.](./media/ekarda-tutorial/ekarda.png)

1. Nella parte inferiore della pagina individuare la sezione **SAML SETTINGS** (IMPOSTAZIONI SAML). In questa sezione viene configurata l'integrazione SAML.
1. Nella sezione **SAML SETTINGS** (IMPOSTAZIONI SAML) seguire questa procedura:

    ![Screenshot della pagina SAML SETTINGS di ekarda con i campi di configurazione SAML evidenziati.](./media/ekarda-tutorial/ekarda1.png)

    1. Selezionare il collegamento **Service Provider metadata** (Metadati del provider di servizi) e salvarlo come file nel computer.
    1. Selezionare la casella di controllo **Enable SAML** (Abilita SAML).
    1. Nella casella **IDP Entity ID** (ID entità IDP) incollare il valore di **Identificatore Azure AD** copiato in precedenza dal portale di Azure.
    1. Nella casella **IDP Login URL** (URL di accesso IDP) incollare il valore di **URL di accesso** copiato in precedenza dal portale di Azure.
    1. Nella casella **IDP Logout URL** (URL di disconnessione IDP) incollare il valore di **URL di disconnessione** copiato in precedenza dal portale di Azure.
    1. Nel Blocco note aprire il file del **certificato (Base64)** scaricato dal portale di Azure. Incollarne il contenuto nella casella di testo **IDP x509 Certificate** (Certificato x509 IDP).
    1. Selezionare la casella di testo **Enable SLO** (Abilita SLO) nella sezione **OPTIONS** (OPZIONI).
    1. Selezionare **Aggiorna**.

### <a name="create-an-ekarda-test-user"></a>Creare un utente di test di ekarda

In questa sezione viene creato un utente di nome B.Simon in ekarda. ekarda supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. In questa sezione non è previsto alcun intervento da parte dell'utente. Se in ekarda non esiste già un utente denominato B.Simon, ne viene creato uno nuovo dopo l'autenticazione.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD tramite il portale App personali.

Quando si seleziona il riquadro di ekarda nel portale App personali, si dovrebbe accedere automaticamente al sito di ekarda per cui si è configurato l'accesso SSO. Per altre informazioni sul portale App personali, vedere l'[introduzione al portale App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni per l'integrazione di app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
* [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
* [Provare ekarda con Azure AD](https://aad.portal.azure.com/)
* Usare la [soluzione per eCard aziendali di ekarda](https://ekarda.com/ecards-ecards-with-logo-for-business-corporate-enterprise) per effettuare il provisioning di un numero qualsiasi di addetti e consentire loro di inviare eCard con il logo aziendale a clienti e colleghi. Altre informazioni sul [provisioning di ekarda come soluzione per l'accesso SSO](https://support.ekarda.com/#SSO-Implementation).
* [Informazioni sul controllo sessioni in Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
* [Come proteggere ekarda con visibilità e controlli avanzati](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
