---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con MongoDB Cloud | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e MongoDB Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/03/2020
ms.author: jeedes
ms.openlocfilehash: f76f6bc77a26a8574218a07321ab72acc9280569
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92517991"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mongodb-cloud"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con MongoDB Cloud

Questa esercitazione descrive come integrare MongoDB Cloud con Azure Active Directory (Azure AD). Integrando MongoDB Cloud con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a MongoDB Cloud, MongoDB Atlas, a MongoDB Community, MongoDB University e MongoDB Support.
* Abilitare gli utenti per l'accesso automatico a MongoDB Cloud con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS (Software as a Service) con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, è necessario:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Un'organizzazione MongoDB Cloud abilitata per l'accesso Single Sign-On (SSO); è possibile effettuare la registrazione per un [cluster gratuito](https://www.mongodb.com/cloud)

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* MongoDB Cloud supporta l'accesso SSO avviato da **SP** e **IDP** .
* MongoDB Cloud supporta il provisioning utenti **JIT** .
* Dopo aver configurato MongoDB Cloud, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. Per altre informazioni, vedere [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-mongodb-cloud-from-the-gallery"></a>Aggiungere MongoDB Cloud dalla raccolta

Per configurare l'integrazione di MongoDB Cloud in Azure AD, è necessario aggiungere MongoDB Cloud dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro sinistro selezionare **Azure Active Directory** .
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni** .
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione** .
1. Nella sezione **Aggiungi dalla raccolta** digitare **MongoDB Cloud** nella casella di ricerca.
1. Selezionare **MongoDB Cloud** nei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-mongodb-cloud"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per MongoDB Cloud

Configurare e testare l'accesso SSO di Azure AD con MongoDB Cloud usando un utente di test di nome **B.Simon** . Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in MongoDB Cloud.

Per configurare e testare l'accesso SSO di Azure AD con MongoDB Cloud, completare le procedure di base seguenti:

1. [Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso): per consentire agli utenti di usare questa funzionalità.
    1. [Creare un utente di test di Azure AD](#create-an-azure-ad-test-user) per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. [Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user) per consentire a B.Simon di usare l'accesso Single Sign-On di Azure AD.
1. [Configurare l'accesso Single Sign-On di MongoDB Cloud](#configure-mongodb-cloud-sso): per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. [Creare un utente di test di MongoDB Cloud](#create-a-mongodb-cloud-test-user): per avere una controparte di B.Simon in MongoDB Cloud collegata alla rappresentazione dell'utente in Azure AD.
1. [Testare l'accesso Single Sign-On](#test-sso): per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **MongoDB Cloud** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On** .
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML** .
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Screenshot della pagina Configura l'accesso Single Sign-On con SAML, in cui è evidenziata l'icona della matita](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP** :

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente:`https://www.okta.com/saml2/service-provider/<Customer_Unique>`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://auth.mongodb.com/sso/saml2/<Customer_Unique>`

1. Selezionare **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP** :

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://cloud.mongodb.com/sso/<Customer_Unique>`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di MongoDB Cloud](https://support.mongodb.com/). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. L'applicazione MongoDB Cloud prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![Screenshot degli attributi predefiniti](common/default-attributes.png)

1. Oltre a quelli elencati in precedenza, l'applicazione MongoDB Cloud prevede il passaggio di qualche altro attributo nella risposta SAML. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base alle esigenze.
    
    | Nome | Attributo di origine|
    | ---------------| --------- |
    | email | user.userprincipalname |
    | firstName | user.givenname |
    | lastName | user.surname |

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il file **XML dei metadati della federazione** . Selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Screenshot della sezione Certificato di firma SAML con il collegamento Scarica evidenziato](common/metadataxml.png)

1. Nella sezione **Configura MongoDB Cloud** copiare gli URL appropriati in base alle esigenze.

    ![Screenshot della sezione Configura MongoDB Cloud, in cui sono evidenziati gli URL](common/copy-configuration-urls.png)
### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione viene creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory** > **Utenti** > **Tutti gli utenti** .
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi annotare la password.
   1. Selezionare **Create** (Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a MongoDB Cloud.

1. Nel portale di Azure selezionare **Applicazioni aziendali** > **Tutte le applicazioni** .
1. Nell'elenco delle applicazioni selezionare **MongoDB Cloud** .
1. Nella pagina di panoramica dell'app individuare la sezione **Gestione** e selezionare **Utenti e gruppi** .

   ![Screenshot della sezione Gestione con Utenti e gruppi evidenziato](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** . Nella finestra di dialogo **Aggiungi assegnazione** selezionare quindi **Utenti e gruppi** .

    ![Screenshot della pagina Utenti e gruppi, in cui è evidenziato il pulsante Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** nell'elenco degli utenti. Scegliere quindi **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco. Scegliere quindi **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Assegna** .

## <a name="configure-mongodb-cloud-sso"></a>Configurare l'accesso Single Sign-On di MongoDB Cloud

Per configurare l'accesso Single Sign-On sul lato MongoDB Cloud, sono necessari gli URL appropriati copiati dal portale di Azure. È anche necessario configurare l'applicazione di federazione per l'organizzazione MongoDB Cloud. Seguire le istruzioni incluse nella [documentazione di MongoDB Cloud](https://docs.atlas.mongodb.com/security/federated-auth-azure-ad/). In caso di problemi, contattare il [team di supporto di MongoDB Cloud](https://support.mongodb.com/).

### <a name="create-a-mongodb-cloud-test-user"></a>Creare un utente di test di MongoDB Cloud

MongoDB Cloud supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario eseguire altre azioni. Se non esiste già un utente in MongoDB Cloud, ne viene creato uno nuovo dopo l'autenticazione.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di MongoDB Cloud nel pannello di accesso, si accede automaticamente all'istanza di MongoDB Cloud per cui si è configurato l'accesso SSO. Per altre informazioni, vedere [Accedere e avviare app dal portale App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Esercitazioni per l'integrazione di applicazioni SaaS con Azure Active Directory](./tutorial-list.md)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)

- [Iscrizione a MongoDB Atlas in Azure](https://azuremarketplace.microsoft.com/marketplace/apps/mongodb.mongodb_atlas_may_2020?tab=Overview)

- [Provare MongoDB Cloud con Azure AD](https://aad.portal.azure.com/)

- [Informazioni sul controllo sessione in Microsoft Cloud App Security](/cloud-app-security/proxy-intro-aad)

- [Proteggere MongoDB Cloud con visibilità e controlli avanzati](/cloud-app-security/proxy-intro-aad)