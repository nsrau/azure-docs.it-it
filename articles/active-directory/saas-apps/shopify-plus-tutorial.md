---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Shopify Plus | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Shopify Plus.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/18/2020
ms.author: jeedes
ms.openlocfilehash: 61d2eb05f7953cda16664f6b4e3317feb46aa8ad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88548573"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-shopify-plus"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Shopify Plus

Questa esercitazione descrive come integrare Shopify Plus con Azure Active Directory (Azure AD). Integrando Shopify Plus con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Shopify Plus.
* Abilitare gli utenti per l'accesso automatico a Shopify Plus con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Shopify Plus abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Shopify Plus supporta l'accesso SSO avviato da **SP e IDP**

* Dopo aver configurato Shopify Plus, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-shopify-plus-from-the-gallery"></a>Aggiunta di Shopify Plus dalla raccolta

Per configurare l'integrazione di Shopify Plus in Azure AD, è necessario aggiungere Shopify Plus dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Shopify Plus** nella casella di ricerca.
1. Selezionare **Shopify Plus** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-shopify-plus"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Shopify Plus

Configurare e testare l'accesso SSO di Azure AD con Shopify Plus usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Shopify Plus.

Per configurare e testare l'accesso SSO di Azure AD con Shopify Plus, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Shopify Plus](#configure-shopify-plus-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Shopify Plus](#create-shopify-plus-test-user)** : per avere una controparte di B.Simon in Shopify Plus collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Shopify Plus** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://accounts.shopify.com/saml/consume/organization/<ORGANIZATION_ID>`

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL di accesso** digitare l'URL: `https://shopify.plus/login`

    > [!NOTE]
    > Il valore di URL di risposta non è reale. è necessario aggiornare questo valore con l'URL di risposta effettivo. Per ottenere il valore, contattare il [team di supporto clienti di Shopify Plus](mailto:plus-user-management@shopify.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. L'applicazione Shopify Plus prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/default-attributes.png)

1. Oltre quelli elencati in precedenza, l'applicazione Shopify Plus prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.

    | Nome | Attributo di origine|
    | ---- | --------------- |
    | email | user.mail |

1. Modificare il formato di **Name ID** (ID nome) in **Persistente**. Selezionare l'opzione **Identificatore utente univoco (ID nome)** e quindi il formato **Identificatore nome**. Selezionare **Persistente** per questa opzione. Salvare le modifiche.
1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** selezionare il pulsante di copia per copiare l'**URL dei metadati di federazione dell'app** e salvarlo nel computer.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Shopify Plus.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Shopify Plus**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-shopify-plus-sso"></a>Configurare l'accesso Single Sign-On di Shopify Plus

Per la procedura completa, vedere la [documentazione di Shopify sulla configurazione delle integrazioni SAML](https://help.shopify.com/en/manual/shopify-plus/saml).

Per configurare Single Sign-On sul lato **Shopify Plus**, copiare l'**URL dei metadati di federazione dell'app** da Azure Active Directory. Accedere quindi alla schermata di [amministrazione dell'organizzazione](https://shopify.plus) e passare a **Users** > **Security** (Utenti > Sicurezza). Selezionare **Set up configuration** (Imposta configurazione) e incollare l'URl dei metadati di federazione dell'app nella sezione **Identity provider metadata URL** (URL dei metadati del provider di identità). Per completare questo passaggio, selezionare **Add** (Aggiungi).

### <a name="create-shopify-plus-test-user"></a>Creare l'utente di test di Shopify Plus

In questa sezione viene creato un utente di nome B.Simon in Shopify Plus. Tornare alla sezione **Users** (Utenti) e aggiungere un utente immettendo l'indirizzo di posta elettronica e le autorizzazioni. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

### <a name="enforce-saml-authentication"></a>Applicare l'autenticazione SAML

> [!NOTE]
> È consigliabile testare l'integrazione usando singoli utenti prima di applicarla a livello generale.

Singoli utenti:
1. Passare alla pagina di un singolo utente in Shopify Plus con un dominio di posta elettronica gestito da Azure AD e verificato in Shopify Plus.
1. Nella sezione di autenticazione SAML selezionare **Edit** (Modifica), **Required** (Obbligatoria) e quindi **Save** (Salva).
1. Verificare che l'utente possa accedere correttamente tramite i flussi avviati da IdP e SP.

Per tutti gli utenti di un dominio di posta elettronica:
1. Tornare alla pagina **Security** (Sicurezza).
1. Selezionare **Required** (Obbligatoria) per l'impostazione di autenticazione SAML. In questo modo l'autenticazione SAML verrà applicata a tutti gli utenti con tale dominio di posta elettronica in Shopify Plus.
1. Selezionare **Save** (Salva).

> [!IMPORTANT]
> L'abilitazione di SAML per tutti gli utenti in un dominio di posta elettronica ha effetto su tutti gli utenti che usano l'applicazione. Gli utenti non potranno accedere usando la normale pagina di accesso, ma potranno accedere all'app solo tramite Azure Active Directory. Shopify non fornisce un URL di accesso per il backup che gli utenti possono usare per accedere usando il nome utente e la password normali. Se necessario, è possibile contattare il supporto di Shopify per disattivare l'autenticazione SAML.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Shopify Plus nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Shopify Plus per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare Shopify Plus con Azure AD](https://aad.portal.azure.com/)

- [Informazioni sul controllo sessioni in Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Come proteggere Shopify Plus con visibilità e controlli avanzati](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
