---
title: 'Esercitazione: Integrazione di Azure Active Directory con Mind Tools Toolkit | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Mind Tools Toolkit.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 65b2979d-9e2f-4530-bc08-546975269ebc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/12/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c0c1cab94a6b83ca429fd640759bed8af0ae881
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124857"
---
# <a name="tutorial-azure-active-directory-integration-with-mind-tools-toolkit"></a>Esercitazione: Integrazione di Azure Active Directory con Mind Tools Toolkit

Questa esercitazione descrive come integrare Mind Tools Toolkit con Azure Active Directory (Azure AD).

Grazie a questa integrazione, è possibile:

* Controllare in Azure AD chi può accedere a Mind Tools Toolkit.
* Abilitare gli utenti per l'accesso automatico (Single Sign-On) a Mind Tools Toolkit con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS (Software as a Service) con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Mind Tools Toolkit, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Una sottoscrizione di Mind Tools Toolkit abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Mind Tools Toolkit supporta l'accesso SSO avviato da SP.
* Mind Tools Toolkit supporta il provisioning utenti JIT.
* Dopo aver configurato Mind Tools Toolkit, è possibile applicare il controllo sessione, che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-mind-tools-toolkit-from-the-gallery"></a>Aggiungere Mind Tools Toolkit dalla raccolta

Per configurare l'integrazione di Mind Tools Toolkit in Azure AD, è necessario aggiungere Mind Tools Toolkit dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento all'estrema sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** immettere **Mind Tools Toolkit** nella casella di ricerca.
1. Selezionare **Mind Tools Toolkit** nei risultati della ricerca e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Mind Tools Toolkit usando un utente di test di nome **B.Simon**. Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Mind Tools Toolkit.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Mind Tools Toolkit, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** per consentire a B.Simon di usare l'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Mind Tools Toolkit](#configure-mind-tools-toolkit-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Mind Tools Toolkit](#create-a-mind-tools-toolkit-test-user)** : per avere una controparte di B.Simon in Mind Tools Toolkit. Tale controparte è collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

In questa sezione seguire questa procedura per configurare l'accesso Single Sign-On di Azure AD con Mind Tools Toolkit:

1. Nella pagina di integrazione dell'applicazione **Mind Tools Toolkit** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Sezione Gestione con l'opzione Single Sign-On evidenziata](common/select-sso.png)

1. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML/WS-Fed** per abilitare l'accesso Single Sign-On.

    ![Finestra di dialogo Selezionare un metodo di accesso Single Sign-On con l'opzione SAML evidenziata](common/select-saml-option.png)

1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita relativa a **Configurazione SAML di base** per modificare le impostazioni.

    ![Pagina Configura l'accesso Single Sign-On con SAML con l'icona della matita relativa a Configurazione SAML di base evidenziata](common/edit-urls.png)

1. Nella casella **URL di accesso** della sezione **Configurazione SAML di base** immettere un URL nel formato `https://app.goodpractice.net/#/<subscriptionUrl>/s/<locationId>`.

    > [!NOTE]
    > Poiché il valore di **URL di accesso** non è reale, è necessario aggiornarlo con l'URL di accesso effettivo. Per ottenere il valore, contattare il [team di supporto clienti di Mind Tools Toolkit](mailto:support@goodpractice.com).

1. Nella pagina **Configura l'accesso Single Sign-On con SAML** passare alla sezione **Certificato di firma SAML**. A destra di **XML metadati federazione** selezionare **Scarica** per scaricare il testo in formato XML e salvarlo nel computer. Il contenuto in formato XML dipende dalle opzioni selezionate.

    ![Sezione Certificato di firma SAML con opzione Scarica evidenziata accanto a XML metadati federazione](common/metadataxml.png)

1. Nella sezione **Configura Mind Tools Toolkit** copiare gli URL necessari seguenti.

    * **URL di accesso**

    * **Identificatore Azure AD**

    * **URL di disconnessione**

    ![Sezione Configura Mind Tools Toolkit con gli URL di configurazione evidenziati](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure:

1. All'estrema sinistra del portale di Azure selezionare **Azure Active Directory** > **Utenti** > **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere **B.Simon**.  
   1. Nel campo **Nome utente** immettere **B.Simon@** _dominio_aziendale_ **.** _estensione_. Ad esempio: B.Simon@contoso.com.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Selezionare **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Mind Tools Toolkit.

1. Nel portale di Azure selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Mind Tools Toolkit**.
1. Nella pagina di panoramica dell'app passare alla sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Sezione Gestione con opzione Utenti e gruppi evidenziata](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente**. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Utenti e gruppi**.

   ![Finestra Utenti e gruppi con l'opzione Aggiungi utente evidenziata](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** nell'elenco degli utenti. Fare quindi clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco. Fare quindi clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Assegna**.

## <a name="configure-mind-tools-toolkit-sso"></a>Configurare l'accesso Single Sign-On di Mind Tools Toolkit

Per configurare l'accesso Single Sign-On sul lato **Mind Tools Toolkit**, inviare il testo scaricato di **XML metadati federazione** e gli URL copiati in precedenza al [team di supporto di Mind Tools Toolkit](mailto:support@goodpractice.com). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-a-mind-tools-toolkit-test-user"></a>Creare l'utente di test di Mind Tools Toolkit

In questa sezione viene creato un utente di nome B.Simon in Mind Tools Toolkit.

Mind Tools Toolkit supporta il provisioning JIT, che è abilitato per impostazione predefinita. In questa sezione non è necessario alcun intervento da parte dell'utente. Se non esiste già un utente in Mind Tools Toolkit, ne viene creato uno nuovo quando si prova ad accedere a Mind Tools Toolkit.

### <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD tramite il portale App personali.

Quando si seleziona il riquadro di Mind Tools Toolkit nel portale App personali, si accede automaticamente all'istanza di Mind Tools Toolkit per cui si è configurato l'accesso SSO. Per altre informazioni sul portale App personali, vedere l'[introduzione al portale App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Esercitazioni per l'integrazione di app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare Mind Tools Toolkit con Azure AD](https://aad.portal.azure.com/)

- [Informazioni sul controllo sessioni in Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Come proteggere Mind Tools Toolkit con visibilità e controlli avanzati](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
