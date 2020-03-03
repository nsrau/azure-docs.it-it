---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con 8x8 | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e 8x8.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b34a6edf-e745-4aec-b0b2-7337473d64c5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/20/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f49c2b445af9acb0761d01b731250e068cb96a36
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562319"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-8x8"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con 8x8

Questa esercitazione descrive come integrare 8x8 con Azure Active Directory (Azure AD). Integrando 8x8 con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a 8x8.
* Abilitare gli utenti per l'accesso automatico a 8x8 con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Una sottoscrizione di 8x8.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* 8x8 supporta l'accesso Single Sign-On avviato da **SP e IDP**

* Dopo aver configurato 8x8, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.

## <a name="adding-8x8-from-the-gallery"></a>Aggiunta di 8x8 dalla raccolta

Per configurare l'integrazione di 8x8 in Azure AD è necessario aggiungere 8x8 dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **8x8** nella casella di ricerca.
1. Selezionare **8x8** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-8x8"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per 8x8

Configurare e testare l'accesso SSO di Azure AD con 8x8 usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in 8x8.

Per configurare e testare l'accesso SSO di Azure AD con 8x8, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di 8x8](#configure-8x8-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di 8x8](#create-8x8-test-user)** : per avere una controparte di B.Simon in 8x8 collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **8x8** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    a. Nella casella di testo **Identificatore** digitare un URL usando uno dei criteri seguenti:

    |||
    |-|-|
    | `https://sso.8x8.com/saml2`|
    | `https://sso.8x8pilot.com/saml2`|

    b. Nella casella di testo **URL di risposta** digitare un URL usando uno dei criteri seguenti:

    |||
    |-|-|
    | `https://sso.8x8.com/saml2`|
    | `https://sso.8x8pilot.com/saml2`|


1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer. Il certificato verrà usato più avanti nell'esercitazione nella sezione **Configurare l'accesso Single Sign-On di 8x8**.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura 8x8** copiare gli URL. I valori di tali URL verranno usati più avanti nell'esercitazione.

    ![Copiare gli URL di configurazione](./media/8x8virtualoffice-tutorial/copy-configuration-urls.png)

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a 8x8.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **8x8**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-8x8-sso"></a>Configurare l'accesso Single Sign-On di 8x8

La parte successiva dell'esercitazione dipende dal tipo di sottoscrizione 8x8 scelta.

* I clienti di 8x8 Editions e X Series che usano Configuration Manager per l'amministrazione possono fare riferimento a [Configurare Configuration Manager per 8x8](#configure-8x8-configuration-manager).
* I clienti di Virtual Office che usano Account Manager per l'amministrazione possono fare riferimento a [Configurare Account Manager per 8x8](#configure-8x8-account-manager).

### <a name="configure-8x8-configuration-manager"></a>Configurare Configuration Manager per 8x8

1. Accedere a [Configuration Manager](https://vo-cm.8x8.com/) in 8x8.

1. Nella home page fare clic su **Identity Management** (Gestione identità).

1. Selezionare **Single Sign-on (SSO)** e quindi **Microsoft Azure AD**.

1. Copiare i tre URL e il certificato di firma dalla pagina **Configura l'accesso Single Sign-On con SAML** nella sezione **Microsoft Azure AD SAML Settings** (Impostazioni SAML di Microsoft Azure AD) in Configuration Manager di 8x8.

    a. Copiare il valore di **URL di accesso** in **IDP Login URL** (URL di accesso IDP).

    b. Copiare il valore di **Identificatore Azure AD** in **IDP Issuer URL/URN** (URL/URN dell'autorità di certificazione IDP).

    c. Copiare il valore di **URL disconnessione** in **IDP Logout URL** (URL di disconnessione IDP).

    d. Scaricare il **certificato (Base64)** e caricarlo in **Certificate** (Certificato).

    e. Fare clic su **Salva**.

### <a name="configure-8x8-account-manager"></a>Configurare Account Manager per 8x8

1. Accedere al tenant di 8x8 Virtual Office come amministratore.

1. Selezionare **Virtual Office Account Mgr** (Account manager di Virtual Office) nel pannello dell'applicazione.

1. Selezionare l'account **Business** (Aziendale) da gestire e fare clic sul pulsante **Sign In** (Accedi).

1. Fare clic sulla scheda **ACCOUNTS** (ACCOUNT) nell'elenco di menu.

1. Fare clic su **Single Sign On** nell'elenco di account.

1. Selezionare **Single Sign-On** sotto Metodi di autenticazione e fare clic su **SAML**.

1. Nella sezione **SAML Single Sign-On** seguire questa procedura:

    a. Nella casella di testo **URL accesso** incollare il valore di **URL di accesso** copiato dal portale di Azure.

    b. Nella casella di testo **Sign Out URL** (URL disconnessione) incollare il valore di **URL disconnessione** copiato dal portale di Azure.

    c. Nella casella di testo **URL autorità di certificazione** incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    d. Fare clic sul pulsante **Sfoglia** per caricare il certificato scaricato dal portale di Azure.

    e. Fare clic sul pulsante **Salva**.

### <a name="create-8x8-test-user"></a>Creare l'utente di test di 8x8

In questa sezione viene creato un utente di nome Britta Simon in 8x8. Collaborare con il[team di supporto di 8x8](https://www.8x8.com/about-us/contact-us) per aggiungere gli utenti alla piattaforma 8x8. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di 8x8 nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di 8x8 per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare 8x8 con Azure AD](https://aad.portal.azure.com/)

- [Informazioni sul controllo sessioni in Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Come proteggere 8x8 con visibilità e controlli avanzati](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)