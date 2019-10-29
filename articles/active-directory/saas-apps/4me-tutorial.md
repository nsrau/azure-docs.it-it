---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con 4me | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e 4me.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 983eecc6-41f8-49b7-b7f6-dcf833dde121
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3e8b753a9afb6b6d27466adb086717b52838e3a
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596303"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-4me"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con 4me

Questa esercitazione descrive come integrare 4me con Azure Active Directory (Azure AD). Integrando 4me con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a 4me.
* Abilitare gli utenti per l'accesso automatico a 4me con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di 4me abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* 4me supporta l'accesso SSO avviato da **SP**
* 4me supporta il provisioning utenti **JIT**

## <a name="adding-4me-from-the-gallery"></a>Aggiunta di 4me dalla raccolta

Per configurare l'integrazione di 4me in Azure AD è necessario aggiungere 4me dalla raccolta all'elenco di app SaaS gestite..

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **4me** nella casella di ricerca.
1. Selezionare **4me** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-4me"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per 4me

Configurare e testare l'accesso SSO di Azure AD con 4me usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in 4me.

Per configurare e testare l'accesso SSO di Azure AD con 4me, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    * **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    * **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di 4me](#configure-4me-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    * **[Creare l'utente di test di 4me](#create-4me-test-user)** : per avere una controparte di B.Simon in 4me collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **4me** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente:

    | Environment| URL|
    |---|---|
    | PRODUZIONE | `https://<SUBDOMAIN>.4me.com`|
    | QA| `https://<SUBDOMAIN>.4me.qa`|
    | | |

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente:

    | Environment| URL|
    |---|---|
    | PRODUZIONE | `https://<SUBDOMAIN>.4me.com`|
    | QA| `https://<SUBDOMAIN>.4me.qa`|
    | | |

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi. Per ottenere questi valori contattare il [team di supporto clienti di 4me](mailto:support@4me.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. L'applicazione 4me prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/default-attributes.png)

1. Oltre quelli elencati in precedenza, l'applicazione 4me prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.

    | Nome | Attributo di origine|
    | ---------------| --------------- |
    | first_name | user.givenname |
    | last_name | user.surname |

1. Nella sezione **Certificato di firma SAML**  fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Certificato di firma SAML** .

    ![Modificare il certificato di firma SAML](common/edit-certificate.png)

1. Nella sezione **Certificato di firma SAML** copiare il valore **IDENTIFICAZIONE PERSONALE** e salvarlo nel computer.

    ![Copiare il valore di Identificazione personale](common/copy-thumbprint.png)

1. Nella sezione **Configura 4me** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a 4me.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **4me**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-4me-sso"></a>Configurare l'accesso Single Sign-On di 4me

1. In un'altra finestra del Web browser accedere a 4me come amministratore.

1. In alto a sinistra fare clic sul logo **Settings** (Impostazioni) e sulla barra sinistra fare clic su **Single Sign-On**.

    ![Impostazioni di 4me](./media/4me-tutorial/tutorial_4me_settings.png)

1. Nella pagina **Single Sign-On** seguire questa procedura:

    ![Accesso Single Sign-On di 4me](./media/4me-tutorial/tutorial_4me_singlesignon.png)

    a. Selezionare l'opzione **Enabled** (Abilitato).

    b. Nella casella di testo **Remote logout URL** (URL di disconnessione remota) incollare il valore di **URL di disconnessione** copiato dal portale di Azure.

    c. Nella casella di testo Under **SAML SSO URL** (URL SSO SAML) della sezione **SAML** incollare il valore di **URL di accesso** copiato dal portale di Azure.

    d. Nella casella di testo **Certificate fingerprint** (Impronta digitale certificato) incollare il valore **IDENTIFICAZIONE PERSONALE** separato da due punti in un ordine basato su dupletti (AA:BB:CC:DD:EE:FF:GG:HH:II), copiato dal portale di Azure.

    e. Fare clic su **Save**.

### <a name="create-4me-test-user"></a>Creare l'utente di test di 4me

In questa sezione viene creato un utente di nome Britta Simon in 4me. 4me supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in 4me, ne viene creato uno nuovo dopo l'autenticazione.

> [!Note]
> Se è necessario creare un utente manualmente, contattare il [team di supporto di 4me](mailto:support@4me.com).

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di 4me nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di 4me per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare 4me con Azure AD](https://aad.portal.azure.com/)
