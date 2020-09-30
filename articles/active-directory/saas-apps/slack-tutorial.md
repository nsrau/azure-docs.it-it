---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Slack | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Slack.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/24/2020
ms.author: jeedes
ms.openlocfilehash: a631ab7190891ae3716a28615bcdbfe4d219ea27
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/13/2020
ms.locfileid: "90053464"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-slack"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Slack

Questa esercitazione descrive come integrare Slack con Azure Active Directory (Azure AD). Integrando Slack con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Slack.
* Abilitare gli utenti per l'accesso automatico a Slack con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Slack abilitata per l'accesso Single Sign-On (SSO).

> [!NOTE]
> Se è necessario eseguire l'integrazione con più di un'istanza di Slack in un tenant, l'identificatore di ogni applicazione può essere una variabile.

> [!NOTE]
> È possibile usare questa integrazione anche dall'ambiente cloud US Government di Azure AD. Questa applicazione è disponibile nella raccolta di applicazioni cloud US Government di Azure AD e la procedura di configurazione è analoga a quella eseguita dal cloud pubblico.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Slack supporta l'accesso SSO avviato da **provider di servizi**
* Slack supporta il provisioning utenti **JIT**
* Slack supporta il [provisioning utenti **automatico**](https://docs.microsoft.com/azure/active-directory/saas-apps/slack-provisioning-tutorial)
* Dopo aver configurato Slack, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.

## <a name="adding-slack-from-the-gallery"></a>Aggiunta di Slack dalla raccolta

Per configurare l'integrazione di Slack in Azure AD, è necessario aggiungere Slack dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Slack** nella casella di ricerca.
1. Selezionare **Slack** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-slack"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Slack

Configurare e testare l'accesso SSO di Azure AD con Slack usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Slack.

Per configurare e testare l'accesso SSO di Azure AD con Slack, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    * **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    * **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Slack](#configure-slack-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    * **[Creare l'utente di test di Slack](#create-slack-test-user)** : per avere una controparte di B.Simon in Slack collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Slack** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<DOMAIN NAME>.slack.com/sso/saml/start`

    b. Nella casella di testo **Identificatore (ID entità)** digitare l'URL: `https://slack.com`
    
    c. Per **URL di risposta** immettere un URL in uno dei formati seguenti:
    
    | URL di risposta|
    |----------|
    | `https://<DOMAIN NAME>.slack.com/sso/saml` |
    | `https://<DOMAIN NAME>.enterprise.slack.com/sso/saml` |

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'URL di accesso e l'URL di risposta effettivi. Per ottenere tale valore, contattare il [team di supporto clienti di Slack](https://slack.com/help/contact). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

    > [!NOTE]
    > Il valore di **Identificatore (ID entità)** può essere una variabile se è necessario integrare più di un'istanza di Slack con il tenant. Usare il modello `https://<DOMAIN NAME>.slack.com`. In questo scenario è necessario associare anche un'altra impostazione in Slack usando lo stesso valore.

1. L'applicazione Slack prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/edit-attribute.png)

1. Oltre quelli elencati in precedenza, l'applicazione Slack prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti. È necessario aggiungere anche l'attributo `email`. Se l'utente non ha un indirizzo di posta elettronica, eseguire il mapping di **emailaddress** e **user.userprincipalname** e di **email** a **user.userprincipalname**.

    | Nome | Attributo di origine |
    | -----|---------|
    | emailaddress | user.userprincipalname |
    | email | user.userprincipalname |

   > [!NOTE]
   > Per configurare la configurazione del provider di servizi (SP), è necessario fare clic su **Espandi** accanto a **Opzioni avanzate** nella pagina di configurazione SAML. Nella casella **Service Provider Issuer** (Autorità di certificazione del provider di servizi) immettere l'URL dell'area di lavoro. Il valore predefinito è slack.com. 

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura Slack** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Slack.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Slack**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-slack-sso"></a>Configurare l'accesso Single Sign-On di Slack

1. In un'altra finestra del Web browser accedere al sito aziendale di Slack come amministratore.

2. Accedere a **Microsoft Azure AD**, quindi andare su **Impostazioni team**.

     ![Configurare l'accesso Single Sign-On in Microsoft Azure AD](./media/slack-tutorial/tutorial-slack-team-settings.png)

3. Nella sezione **Impostazioni team** fare clic sulla scheda **Autenticazione**, quindi fare clic su **Cambia impostazioni**.

    ![Configurare l'accesso Single Sign-On in Impostazioni team](./media/slack-tutorial/tutorial-slack-authentication.png)

4. Nella finestra di dialogo **Impostazioni di autenticazione SAML** eseguire la procedura seguente:

    ![Configurare l'accesso Single Sign-On in Impostazioni di autenticazione SAML](./media/slack-tutorial/tutorial-slack-save-authentication.png)

    a.  Nella casella di testo **SAML 2.0 Endpoint (HTTP)** (Endpoint SAML 2.0 - HTTP) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    b.  Nella casella di testo **Identity Provider Issuer** (Autorità di certificazione del provider di identità) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    c.  Aprire il certificato scaricato nel Blocco note, copiarne il contenuto negli Appunti e incollarlo nella casella di testo **Certificato pubblico**.

    d. Configurare le tre impostazioni riportate sopra nel modo appropriato per il proprio team Slack. Per altre informazioni sulle impostazioni, vedere la **guida alla configurazione dell'accesso SSO di Slack** disponibile a questo indirizzo: `https://get.slack.help/hc/articles/220403548-Guide-to-single-sign-on-with-Slack%60`

    ![Configurare l'accesso Single Sign-On sul lato app](./media/slack-tutorial/tutorial-slack-expand.png)

    e. Fare clic su **Espandi**  e immettere `https://slack.com` nella casella di testo **Service Provider Issuer** (Autorità di certificazione del provider di servizi).

    f.  Fare clic su **Salva configurazione**.
    
    > [!NOTE]
    > Se si vuole integrare più di un'istanza di Slack con Azure AD, impostare `https://<DOMAIN NAME>.slack.com` su **Service provider issuer** (Autorità di certificazione del provider di servizi) in modo che possa essere associata all'impostazione **Identificatore** dell'applicazione di Azure.

### <a name="create-slack-test-user"></a>Creare l'utente di test di Slack

Questa sezione descrive come creare un utente di nome B.Simon in Slack. Slack supporta il provisioning JIT, abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Durante un tentativo di accesso a Slack viene creato un nuovo utente, se non esiste ancora. Slack supporta anche il provisioning utenti automatico; [qui](slack-provisioning-tutorial.md) è possibile trovare altre informazioni su come configurare il provisioning utenti automatico.

> [!NOTE]
> Per creare un utente manualmente, è necessario contattare il [team di supporto di Slack](https://slack.com/help/contact).

> [!NOTE]
> Azure AD Connect è lo strumento capace di sincronizzare le identità di Active Directory locali con Azure AD. Questi utenti sincronizzati possono quindi usare le applicazioni come gli altri utenti del cloud.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Facendo clic sul riquadro Slack nel pannello di accesso si dovrebbe accedere automaticamente all'istanza di Slack per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare Slack con Azure AD](https://aad.portal.azure.com/)

- [Informazioni sul controllo sessioni in Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
