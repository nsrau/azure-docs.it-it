---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con OrgVitality SSO | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e OrgVitality SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/29/2020
ms.author: jeedes
ms.openlocfilehash: 4cb46487904f1f909686119d074dff5dcfad40c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91604049"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-orgvitality-sso"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con OrgVitality SSO

Questa esercitazione descrive come integrare OrgVitality SSO con Azure Active Directory (Azure AD). Integrando OrgVitality SSO con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a OrgVitality SSO.
* Abilitare gli utenti per l'accesso automatico a OrgVitality SSO con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di OrgVitality SSO abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* OrgVitality SSO supporta l'accesso SSO avviato da **IDP**

## <a name="adding-orgvitality-sso-from-the-gallery"></a>Aggiunta di OrgVitality SSO dalla raccolta

Per configurare l'integrazione di OrgVitality SSO in Azure AD, è necessario aggiungere OrgVitality SSO dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **OrgVitality SSO** nella casella di ricerca.
1. Selezionare **OrgVitality SSO** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-sso-for-orgvitality-sso"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per OrgVitality SSO

Configurare e testare l'accesso SSO di Azure AD con OrgVitality SSO usando un utente di test di nome **B. Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in OrgVitality SSO.

Per configurare e testare l'accesso SSO di Azure AD con OrgVitality SSO, completare i passaggi seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di OrgVitality SSO](#configure-orgvitality-sso-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di OrgVitality SSO](#create-orgvitality-sso-test-user)** : per avere una controparte di B. Simon in OrgVitality SSO collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **OrgVitality SSO** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://rpt.orgvitality.com/<COMPANY_NAME>/`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://rpt.orgvitality.com/<COMPANY_NAME>Auth/default.aspx`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di risposta effettivi. Per ottenere tali valori contattare il [team di supporto di OrgVitality SSO](https://orgvitality.com/contact-us/). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. L'applicazione OrgVitality SSO prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. La schermata seguente illustra l'elenco degli attributi predefiniti in cui **nameidentifier** è associato a **user.userprincipalname**. L'applicazione OrgVitality SSO prevede che **nameidentifier** sia associato a **user.employeeid**, di conseguenza è necessario modificare il mapping dell'attributo. A questo scopo, fare clic sull'icona **Modifica**.

    ![image](common/default-attributes.png)

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura OrgVitality SSO** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B. Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a OrgVitality SSO.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **OrgVitality SSO**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-orgvitality-sso-sso"></a>Configurare l'accesso Single Sign-On per OrgVitality SSO

Per configurare l'accesso Single Sign-On sul lato **OrgVitality SSO** è necessario inviare il **certificato (Base64)** scaricato e gli URL appropriati, copiati dal portale di Azure, al [team di supporto di OrgVitality SSO](https://orgvitality.com/contact-us/). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-orgvitality-sso-test-user"></a>Creare un utente di test di OrgVitality SSO

In questa sezione viene creato un utente di nome Britta Simon in OrgVitality SSO. Collaborare con il [team di supporto di OrgVitality SSO](https://orgvitality.com/contact-us/) per aggiungere gli utenti alla piattaforma OrgVitality SSO. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti.

1. Dopo aver fatto clic su Testa l'applicazione nel portale di Azure, si dovrebbe accedere automaticamente all'istanza di OrgVitality SSO per cui si è configurato l'accesso Single Sign-On

1. È possibile usare il Pannello di accesso Microsoft. Quando si fa clic sul riquadro di OrgVitality SSO nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di OrgVitality SSO per cui si è configurato l'accesso Single Sign-On. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato OrgVitality SSO, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
