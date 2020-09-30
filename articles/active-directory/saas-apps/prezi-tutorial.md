---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Prezi | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Prezi.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/20/2020
ms.author: jeedes
ms.openlocfilehash: 2f3e540174643f20c87396b8568f6e5b0a1ab16d
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89658928"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-prezi"></a>Esercitazione: Integrazione dell'accesso Single Sign-On di Azure Active Directory con Prezi

Questa esercitazione descrive come integrare Prezi con Azure Active Directory (Azure AD). Integrando Prezi con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Prezi.
* Abilitare gli utenti per l'accesso automatico a Prezi con gli account Azure AD personali.
* Gestire gli account nel portale di Azure.

Per altre informazioni sull'integrazione di app SaaS (Software as a Service) con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Una sottoscrizione di Prezi abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Prezi supporta l'accesso SSO avviato da SP e IDP.
* Prezi supporta il provisioning utenti JIT.
* Dopo aver configurato Prezi, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. Per altre informazioni, vedere [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.

## <a name="add-prezi-from-the-gallery"></a>Aggiungere Prezi dalla raccolta

Per configurare l'integrazione di Prezi in Azure AD, è necessario aggiungere Prezi dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro all'estrema sinistra selezionare **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** immettere **Prezi** nella casella di ricerca.
1. Selezionare **Prezi** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-prezi"></a>Configurare e testare l'accesso SSO di Azure AD per Prezi

Configurare e testare l'accesso SSO di Azure AD con Prezi usando un utente di test di nome B.Simon. Per consentire il funzionamento dell'accesso Single Sign-On, stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Prezi.

Per configurare e testare l'accesso SSO di Azure AD con Prezi, completare le procedure di base seguenti:

1. [Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso): per consentire agli utenti di usare questa funzionalità.
    1. [Creare un utente di test di Azure AD](#create-an-azure-ad-test-user) per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. [Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user) per consentire a B.Simon di usare l'accesso Single Sign-On di Azure AD.
1. [Configurare l'accesso Single Sign-On di Prezi](#configure-prezi-sso): per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. [Creare l'utente di test di Prezi](#create-a-prezi-test-user): per avere una controparte di B.Simon in Prezi collegata alla rappresentazione dell'utente in Azure AD.
1. [Testare l'accesso Single Sign-On](#test-sso): per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure:

1. Nella pagina di integrazione dell'applicazione **Prezi** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** selezionare l'icona **Modifica** per modificare le impostazioni in **Configurazione SAML di base**.

   ![Modificare le impostazioni di Configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** l'utente non deve eseguire alcuna operazione perché l'app è già preintegrata in Azure.

1. Selezionare **Impostare URL aggiuntivi** e quindi seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella **URL di accesso** immettere l'URL `https://prezi.com/login/sso/`.

1. Selezionare **Salva**.

1. L'applicazione Prezi prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![Attributi utente e attestazioni](common/default-attributes.png)

1. L'applicazione Prezi prevede inoltre il passaggio di altri attributi nella risposta SAML, come illustrato di seguito. Anche questi attributi vengono prepopolati, ma è possibile modificarli in base alle esigenze.
    
    | Nome | Attributo di origine|
    | ---------------| --------------- |
    | given_name | user.givenname |
    | family_name | user.surname |

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** . Selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura Prezi** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel portale di Azure selezionare **Azure Active Directory** nel riquadro all'estrema sinistra. Passare a **Utenti** e quindi selezionare **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. Nelle proprietà utente seguire questa procedura:
   1. Nella casella **Nome** immettere **B.Simon**.
   1. Nella casella **Nome utente** immettere un valore per `username@companydomain.extension`, ad esempio `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password**. Prendere quindi nota del valore visualizzato nella casella **Password**.
   1. Selezionare **Create** (Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso SSO di Azure concedendole l'accesso a Prezi.

1. Nel portale di Azure selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Prezi**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento Utenti e gruppi](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic su **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic su **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Assegna**.

## <a name="configure-prezi-sso"></a>Configurare l'accesso Single Sign-On di Prezi

1. In un'altra finestra del Web browser accedere a Prezi con l'account del team e passare a [Admin Console](https://prezi.com/organizations/manage) (Console di amministrazione).

1. In **Admin Console** (Console di amministrazione) selezionare la scheda **Settings** (Impostazioni).

    ![Scheda Settings](./media/prezi-tutorial/settings-image.png)

1. Passare alla sezione **Single Sign-On (SSO)** e attivare l'interruttore per abilitare l'accesso SSO.
    
    ![Interruttore Single Sign-On (SSO)](./media/prezi-tutorial/single-signon.png)

1. Nella sezione **Single Sign-On (SSO)** seguire questa procedura:

    ![Sezione Single Sign-On (SSO)](./media/prezi-tutorial/configuration.png)

    1. Nella casella **Identifier or Issuer URL** (Identificatore o URL autorità di certificazione) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    1. Nella casella **SAML 2.0 Endpoint (HTTP)** (Endpoint SAML 2.0 (HTTP)) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    1. Aprire il **certificato (Base64)** scaricato dal portale di Azure nel Blocco note. Copiare il contenuto del certificato e quindi incollarlo nella casella **Certificate (X.509)** (Certificato - X.509).

    1. Selezionare **Save** (Salva).

### <a name="create-a-prezi-test-user"></a>Creare l'utente di test di Prezi

In questa sezione viene creato un utente di nome Britta Simon in Prezi. Prezi supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in Prezi, ne viene creato uno nuovo dopo l'autenticazione.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso SSO di Azure AD usando il pannello di accesso.

Quando si seleziona il riquadro di Prezi nel pannello di accesso, si dovrebbe accedere automaticamente all'account Prezi per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Provare Prezi con Azure AD](https://aad.portal.azure.com/)
- [Informazioni sul controllo sessioni in Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
- [Come proteggere Prezi con visibilità e controlli avanzati](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

