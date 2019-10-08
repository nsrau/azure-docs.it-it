---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con DocuSign | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e DocuSign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f02492dd7c9563c8c0002a63f0b105bd0be8b14
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71345578"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-docusign"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con DocuSign

Questa esercitazione descrive come integrare DocuSign con Microsoft Azure Active Directory (Azure AD). Integrando DocuSign con Azure AD, è possibile:

* Usare Azure AD per controllare chi può accedere a DocuSign.
* Abilitare l'accesso automatico a DocuSign per gli utenti tramite gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS (Software as a Service) con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di DocuSign abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione verranno eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test per verificare quanto segue:

* DocuSign supporta l'accesso SSO avviato da provider di servizi (SP).

* DocuSign supporta il provisioning utenti *JIT*.

* DocuSign supporta il [provisioning utenti automatico](https://docs.microsoft.com/azure/active-directory/saas-apps/docusign-provisioning-tutorial).

## <a name="adding-docusign-from-the-gallery"></a>Aggiunta di DocuSign dalla raccolta

Per configurare l'integrazione di DocuSign in Azure AD, è necessario aggiungere DocuSign dalla raccolta all'elenco di app SaaS gestite:

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **DocuSign** nella casella di ricerca.
1. Selezionare **DocuSign** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-docusign"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per DocuSign

Configurare e testare l'accesso SSO di Azure AD con DocuSign usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente corrispondente in DocuSign.

Per configurare e testare l'accesso SSO di Azure AD con DocuSign, completare le procedure di base seguenti:

1. [Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso): per consentire agli utenti di usare questa funzionalità.
    1. [Creare un utente di test di Azure AD](#create-an-azure-ad-test-user) per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. [Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user) per consentire a B.Simon di usare l'accesso Single Sign-On di Azure AD.
1. [Configurare l'accesso Single Sign-On di DocuSign](#configure-docusign-sso): per configurare le impostazioni dell'accesso Single Sign-On sul lato applicazione.
1. [Creare l'utente di test di DocuSign](#create-docusign-test-user): per generare in DocuSign una controparte di B.Simon collegata alla rappresentazione dell'utente in Azure AD.
1. [Testare l'accesso SSO](#test-sso) per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **DocuSign** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della penna relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    a. Nella casella di testo **URL di accesso** immettere un URL nel formato seguente: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. Nella casella di testo **Identificatore (ID entità)** immettere un URL nel formato seguente: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`

    > [!NOTE]
    > Questi valori tra parentesi angolari sono segnaposto. Sostituirli i valori effettivi dell'URL e dell'identificatore di accesso. Per informazioni dettagliate, vedere la sezione relativa a "View SAML 2.0 Endpoints" (Visualizza endpoint SAML 2.0) più avanti in questa esercitazione.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** . Selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura DocuSign** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere **B. Simon**.  
   1. Nel campo **Nome utente** immettere `<username>@<companydomain>.<extension>`. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Selezionare **Create** (Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si concederà a B.Simon l'accesso a DocuSign in modo che questo utente possa usare l'accesso Single Sign-On di Azure.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **DocuSign**.
1. Nella pagina di panoramica dell'app individuare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco **Utenti** e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** selezionare il pulsante **Assegna**.

## <a name="configure-docusign-sso"></a>Configurare l'accesso Single Sign-On di DocuSign

1. Per automatizzare la configurazione in DocuSign, installare l'estensione del browser per l'accesso sicuro ad App personali selezionando **Installa estensione**.

    ![Estensione per App personali](common/install-myappssecure-extension.png)

2. Dopo aver aggiunto l'estensione al browser, selezionare **Configura DocuSign**. Si verrà reindirizzato all'applicazione DocuSign. Nell'applicazione fornire le credenziali di amministratore per accedere a DocuSign. L'estensione del browser configura automaticamente l'applicazione e automatizza i passaggi da 3 a 5.

    ![Eseguire la configurazione](common/setup-sso.png)

3. Se si vuole configurare manualmente DocuSign, aprire una nuova finestra del Web browser e accedere al sito aziendale di DocuSign come amministratore.

4. Nell'angolo in alto a destra della pagina selezionare il logo del profilo e quindi **Go to Admin** (Passa ad amministrazione).
  
    ![Go to Admin (Passa ad amministratore) in Profile (Profilo)][51]

5. Nella pagina delle soluzioni di dominio selezionare **Domains** (Domini).

    ![Domain Solutions/Domains (Soluzioni di dominio/Domini)][50]

6. Nella sezione **Domains** (Domini) selezionare **CLAIM DOMAIN** (RICHIEDI DOMINIO).

    ![Opzione Claim Domain (Richiedi dominio)][52]

7. Nella finestra di dialogo **Claim a Domain** (Richiedi un dominio) digitare il dominio aziendale nella casella **Domain Name** (Nome dominio) e quindi selezionare **CLAIM** (RICHIEDI). Verificare il dominio e assicurarsi che lo stato sia attivo.

    ![Finestra di dialogo Claim a Domain/Domain Name (Richiedi un dominio/Nome dominio)][53]

8. Nella pagina delle soluzioni di dominio selezionare **Identity Providers** (Provider di identità).
  
    ![Opzione Identity Providers (Provider di identità)][54]

9. Nella sezione **Identity Providers** (Provider di identità) selezionare **ADD IDENTITY PROVIDER** (AGGIUNGI PROVIDER DI IDENTITÀ).

    ![Opzione Add Identity Provider (Aggiungi provider di identità)][55]

10. Nella pagina **Identity Provider Settings** (Impostazioni provider di identità) seguire questa procedura:

    ![Campi di Identity Provider Settings (Impostazioni provider di identità)][56]

    a. Nella casella **Name** (Nome) digitare un nome univoco per la configurazione. Non usare spazi.

    b. Nella casella **Identity Provider Issuer** (Autorità di certificazione provider di identità) incollare il valore dell'**identificatore di Azure AD** copiato dal portale di Azure.

    c. Nella casella **Identity Provider Login URL** (URL di accesso del provider di identità) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    d. Nella casella **Identity Provider Logout URL** (URL di disconnessione provider di identità) incollare il valore di **URL disconnessione** copiato dal portale di Azure.

    e. Selezionare **Sign AuthN Request** (Firma richiesta di autenticazione).

    f. Per **Send AuthN request by** (Invia richiesta di autenticazione da) selezionare **POST**.

    g. Per **Send logout request by** (Invia richiesta di disconnessione da) selezionare **GET**.

    h. Nella sezione **Custom Attribute Mapping** (Mapping attributi personalizzati) selezionare **ADD NEW MAPPING** (AGGIUNGI NUOVO MAPPING).

       ![Interfaccia utente di Custom Attribute Mapping (Mapping attributi personalizzati)][62]

    i. Scegliere il campo di cui si vuole eseguire il mapping all'attestazione di Azure AD. In questo esempio viene eseguito il mapping dell'attestazione **emailaddress** al valore di `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`. Si tratta del nome di attestazione predefinito di Azure AD per l'attestazione basata su posta elettronica. Selezionare **SAVE** (SALVA).

       ![Campi di Custom Attribute Mapping (Mapping attributi personalizzati)][57]

       > [!NOTE]
       > Usare l' **Identificatore utente** appropriato per associare l'utente da Azure AD al mapping degli utenti di DocuSign. Selezionare il campo corretto e immettere il valore appropriato in base alle impostazioni dell'organizzazione.

    j. Nella sezione **Identity Provider Certificates** (Certificati provider di identità) selezionare **ADD CERTIFICATE** (AGGIUNGI CERTIFICATO), caricare il certificato scaricato dal portale di Azure AD e quindi selezionare **SAVE** (SALVA).

       ![Identity Provider Certificates/Add Certificate (Certificati provider di identità/Aggiungi certificato)][58]

    k. Nella sezione **Identity Providers** (Provider di identità) fare clic su **ACTIONS** (AZIONI) e quindi su **Endpoints** (Endpoint).

       ![Identity Providers/Endpoints (Provider di identità/Endpoint)][59]

    l. Nella sezione **View SAML 2.0 Endpoints** (Visualizza endpoint SAML 2.0) del portale di amministrazione di DocuSign seguire questa procedura:
       1. Copiare il valore di **Service Provider Issuer URL** (URL autorità di certificazione provider di servizi) e quindi incollarlo nella casella **Identificatore** nella sezione **Configurazione SAML di base** del portale di Azure.

       1. Copiare il valore di **Service Provider Login URL** (URL di accesso provider di servizi) e quindi incollarlo nella casella **URL di accesso** nella sezione **Configurazione SAML di base** del portale di Azure.

       1. Selezionare **Chiudi**.

       ![View SAML 2.0 Endpoints (Visualizza endpoint SAML 2.0)][60]

### <a name="create-docusign-test-user"></a>Creare l'utente di test di DocuSign

In questa sezione viene creato un utente di nome B.Simon in DocuSign. DocuSign supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in DocuSign, ne viene creato uno nuovo dopo l'autenticazione.

>[!Note]
>Per creare un utente manualmente, contattare il [team di supporto di DocuSign](https://support.docusign.com/).

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si seleziona il riquadro di DocuSign nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di DocuSign per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Esercitazioni sull'integrazione delle app SaaS con Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Informazioni sull'accesso condizionale Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare DocuSign con Azure AD](https://aad.portal.azure.com/)

<!--Image references-->

[50]: ./media/docusign-tutorial/tutorial_docusign_18.png
[51]: ./media/docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/docusign-tutorial/tutorial_docusign_29.png
[62]: ./media/docusign-tutorial/tutorial_docusign_30.png
