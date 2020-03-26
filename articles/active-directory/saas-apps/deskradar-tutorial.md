---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Deskradar | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Deskradar.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5ff3e014-b79f-4a6c-bb0b-38462bea5d10
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ccb0f63476db733749c2b9bd9c297e29fe6fba6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75608534"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-deskradar"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Deskradar

Questa esercitazione descrive come integrare Deskradar con Azure Active Directory (Azure AD). Integrando Deskradar con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Deskradar.
* Abilitare gli utenti per l'accesso automatico a Deskradar con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Deskradar abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.



* Deskradar supporta l'accesso SSO avviato da **SP e IDP**



## <a name="adding-deskradar-from-the-gallery"></a>Aggiunta di Deskradar dalla raccolta

Per configurare l'integrazione di Deskradar in Azure AD, è necessario aggiungere Deskradar dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Deskradar** nella casella di ricerca.
1. Selezionare **Deskradar** nel riquadro dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-deskradar"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Deskradar

Configurare e testare l'accesso SSO di Azure AD con Deskradar usando un utente di test di nome **B. Simon**. Per il corretto funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Deskradar.

Per configurare e testare l'accesso SSO di Azure AD con Deskradar, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Deskradar](#configure-deskradar-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Deskradar](#create-deskradar-test-user)** : per avere una controparte di B.Simon in Deskradar collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Deskradar** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<YOURDOMAIN>.deskradar.cloud`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<YOURDOMAIN>.deskradar.cloud/auth/sso/saml/consume`

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<YOURDOMAIN>.deskradar.cloud/auth/sso/saml/login`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Sostituire **YOURDOMAIN** con il dominio dell'istanza di Deskradar. Per ottenere questi valori, contattare il [team di supporto clienti di Deskradar](mailto:support@deskradar.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. L'applicazione Deskradar prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/edit-attribute.png)

1. Oltre quelli elencati in precedenza, l'applicazione Deskradar prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.

    | Nome | Attributo di origine|
    | ---------------| --------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | Email | user.userprincipalname |
    | | |

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura Deskradar** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B. Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Deskradar.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Deskradar**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="configure-deskradar-sso"></a>Configurare l'accesso Single Sign-On per Deskradar

1. Per automatizzare la configurazione all'interno di Deskradar, è necessario installare l'**estensione del browser per l'accesso sicuro alle app personali** facendo clic su **Installa l'estensione**.

    ![Estensione MyApps](common/install-myappssecure-extension.png)

1. Dopo aver aggiunto l'estensione al browser, fare clic su **Configura Deskradar** per passare direttamente all'applicazione Deskradar. Nell'applicazione specificare le credenziali di amministratore per accedere a Deskradar. L'estensione del browser configurerà automaticamente l'applicazione per l'utente e automatizzerà i passaggi da 3 a 7.

    ![Eseguire la configurazione](common/setup-sso.png)

1. Se si vuole configurare manualmente Deskradar, aprire una nuova finestra del Web browser, accedere al sito aziendale di Deskradar come amministratore e seguire questa procedura:

1. Aprire il pannello **Team** facendo clic sull'icona nella barra laterale.

1. Passare alla scheda **Autenticazione**.

1. Nella scheda **SAML 2.0** seguire questa procedura:

    ![Configurazione di Deskradar](./media/deskradar-tutorial/14-paste-urls.jpg)

    a. Abilitare il metodo di autenticazione **SAML**.

    b. Nella casella di testo **URL SSO SAML** immettere il valore di **URL di accesso** copiato dal portale di Azure.

    c. Nella casella di testo **Identity Provider Issuer** (Autorità di certificazione provider di identità) immettere il valore di **Identificatore Azure AD** copiato dal portale di Azure.

1. Aprire il file **Certificato (Base64)** scaricato in un editor di testo, quindi copiarne e incollarne il contenuto nel campo **Public Certificate** (Certificato pubblico) in Deskradar.

    ![Configurazione di Deskradar](./media/deskradar-tutorial/15-paste-certificate.jpg)

### <a name="create-deskradar-test-user"></a>Creare l'utente di test di Deskradar

In questa sezione viene creato un utente di nome B.Simon in Deskradar. Collaborare con il [team di supporto clienti di Deskradar](mailto:support@deskradar.com) per aggiungere gli utenti alla piattaforma Deskradar. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Deskradar nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Deskradar per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare Deskradar con Azure AD](https://aad.portal.azure.com/)

