---
title: 'Esercitazione: Integrazione di Azure Active Directory con Carbonite Endpoint Backup | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Carbonite Endpoint Backup.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b78091f1-2f06-4c2c-8541-72aee0b5a322
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/30/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf5bf49538823ea50bca197a78ec54d4ddcae1ff
ms.sourcegitcommit: 6ad03fa28a0f60cb6dce6144f728c2ceb56ff6e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68708444"
---
# <a name="tutorial-integrate-carbonite-endpoint-backup-with-azure-active-directory"></a>Esercitazione: Integrare Carbonite Endpoint Backup con Azure Active Directory

Questa esercitazione descrive come integrare Carbonite Endpoint Backup con Azure Active Directory (Azure AD). Integrando Carbonite Endpoint Backup con Azure AD è possibile:

* Controllare in Azure AD chi può accedere a Carbonite Endpoint Backup.
* Consentire agli utenti l'accesso automatico a Carbonite Endpoint Backup con i rispettivi account Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Carbonite Endpoint Backup abilitata all'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Carbonite Endpoint Backup supporta l'accesso SSO avviato da **provider di servizi e provider di identità**

## <a name="adding-carbonite-endpoint-backup-from-the-gallery"></a>Aggiunta di Carbonite Endpoint Backup dalla raccolta

Per configurare l'integrazione di Carbonite Endpoint Backup in Azure AD, è necessario aggiungere Carbonite Endpoint Backup dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Carbonite Endpoint Backup** nella casella di ricerca.
1. Selezionare **Carbonite Endpoint Backup** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

Configurare e testare l'accesso SSO di Azure AD con Carbonite Endpoint Backup usando un utente di test di nome **B.Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Carbonite Endpoint Backup.

Per configurare e testare l'accesso SSO di Azure AD con Carbonite Endpoint Backup, completare le procedure di base seguenti.

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso SSO di Carbonite Endpoint Backup](#configure-carbonite-endpoint-backup-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Carbonite Endpoint Backup](#create-carbonite-endpoint-backup-test-user)** : per avere una controparte di B.Simon in Carbonite Endpoint Backup collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Carbonite Endpoint Backup** del [portale di Azure](https://portal.azure.com/) trovare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Nella casella di testo **Identificatore** digitare uno degli URL seguenti:

    | | |
    |-|-|
    | `https://red-us.mysecuredatavault.com`|
    | `https://red-apac.mysecuredatavault.com`|
    | `https://red-fr.mysecuredatavault.com`|
    | `https://red-emea.mysecuredatavault.com`|
    | `https://kamino.mysecuredatavault.com`|
    | | |

    b. Nella casella di testo **URL di risposta** digitare uno degli URL seguenti:

    | | |
    |-|-|
    | `https://red-us.mysecuredatavault.com/AssertionConsumerService.aspx`|
    | `https://red-apac.mysecuredatavault.com/AssertionConsumerService.aspx`|
    | `https://red-fr.mysecuredatavault.com/AssertionConsumerService.aspx`|
    | `https://red-emea.mysecuredatavault.com/AssertionConsumerService.aspx`|
    | | |

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL di accesso** digitare uno degli URL seguenti:

    | | |
    |-|-|
    | `https://red-us.mysecuredatavault.com/`|
    | `https://red-apac.mysecuredatavault.com/`|
    | `https://red-fr.mysecuredatavault.com/`|
    | `https://red-emea.mysecuredatavault.com/`|
    | | |

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura Carbonite Endpoint Backup** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="configure-carbonite-endpoint-backup-sso"></a>Configurare l'accesso SSO di Carbonite Endpoint Backup

1. In un'altra finestra del Web browser accedere al sito aziendale di Carbonite Endpoint Backup come amministratore.

1. Fare clic su **Company** (Società) nel riquadro sinistro.

    ![Configurazione di Carbonite Endpoint Backup ](media/carbonite-endpoint-backup-tutorial/configure1.png)

1. Fare clic su **Single sign-on**.

    ![Configurazione di Carbonite Endpoint Backup ](media/carbonite-endpoint-backup-tutorial/configure2.png)

1. Fare clic su **Enable** (Abilita) e quindi su **Edit settings** (Modifica impostazioni) per eseguire la configurazione.

    ![Configurazione di Carbonite Endpoint Backup ](media/carbonite-endpoint-backup-tutorial/configure3.png)

1. Nella pagina di impostazioni **Single sign-on** seguire questa procedura:

    ![Configurazione di Carbonite Endpoint Backup ](media/carbonite-endpoint-backup-tutorial/configure4.png)

    1. Nella casella di testo **Identity provider name** (Nome provider di identità) immettere il nome del provider di identità, ad esempio **Microsoft Azure AD**.

    1. Nella casella di testo **Identity Provider URL** (URL provider di identità) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    1. Fare clic su **Choose file** (Scegli file) per caricare il file di **Certificato (Base64)** scaricato dal portale di Azure.

    1. Fare clic su **Save**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Carbonite Endpoint Backup.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Carbonite Endpoint Backup**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-carbonite-endpoint-backup-test-user"></a>Creare un utente di test di Carbonite Endpoint Backup

1. In un'altra finestra del Web browser accedere al sito aziendale di Carbonite Endpoint Backup come amministratore.

1. Fare clic su **Users** (Utenti) nel riquadro sinistro e quindi su **Add user** (Aggiungi utente).

    ![Aggiungere un utente in Carbonite Endpoint Backup](media/carbonite-endpoint-backup-tutorial/adduser1.png)

1. Nella pagina **Add user** (Aggiungi utente) seguire questa procedura:

    ![Aggiungere un utente in Carbonite Endpoint Backup](media/carbonite-endpoint-backup-tutorial/adduser2.png)

    1. Immettere i dati dell'utente in **Email** (Posta elettronica), **First name** (Nome) e **Last name** (Cognome) e assegnare all'utente le autorizzazioni necessarie in base ai requisiti dell'organizzazione.

    1. Fare clic su **Add User**.

### <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Facendo clic sul riquadro Carbonite Endpoint Backup nel pannello di accesso si dovrebbe accedere automaticamente all'applicazione Carbonite Endpoint Backup per cui è stato configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)