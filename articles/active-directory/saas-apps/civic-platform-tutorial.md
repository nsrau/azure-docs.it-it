---
title: 'Esercitazione: Integrazione di Azure Active Directory con Civic Platform | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Civic Platform.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1d790454-143e-40ac-b3cb-5a256977b4db
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/25/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ccf124c5a4160715df4e685e405dcd591c49ae7
ms.sourcegitcommit: 5604661655840c428045eb837fb8704dca811da0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2019
ms.locfileid: "68496446"
---
# <a name="tutorial-integrate-civic-platform-with-azure-active-directory"></a>Esercitazione: Integrare Civic Platform con Azure Active Directory

Questa esercitazione descrive come integrare Civic Platform con Azure Active Directory (Azure AD). Integrando Civic Platform con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Civic Platform.
* Abilitare gli utenti per l'accesso automatico a Civic Platform con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere una versione di valutazione gratuita per un mese [qui](https://azure.microsoft.com/pricing/free-trial/).
* Sottoscrizione di Civic Platform abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Civic Platform supporta l'accesso SSO avviato da **SP**





## <a name="adding-civic-platform-from-the-gallery"></a>Aggiunta di Civic Platform dalla raccolta

Per configurare l'integrazione di Civic Platform in Azure AD, è necessario aggiungere Civic Platform dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Civic Platform** nella casella di ricerca.
1. Selezionare **Civic Platform** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

Configurare e testare l'accesso Single Sign-On di Azure AD con Civic Platform usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Civic Platform.

Per configurare e testare l'accesso SSO di Azure AD con Civic Platform, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Civic Platform](#configure-civic-platform-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di Civic Platform](#create-civic-platform-test-user)** : per avere una controparte di B.Simon in Civic Platform collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Civic Platform** del [portale di Azure](https://portal.azure.com/) trovare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<SUBDOMAIN>.accela.com`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL: `civicplatform.accela.com`

    > [!NOTE]
    > Il valore dell'URL di accesso non è reale. Aggiornarlo con l'URL di accesso effettivo. Per ottenere questo valore, contattare il [team di supporto clienti di Civic Platform](mailto:skale@accela.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/copy-metadataurl.png)

1. Passare ad **Azure Active Directory** > **Registrazioni app** e quindi selezionare l'applicazione.

1. Copiare il valore di **ID della directory (tenant)** e archiviarlo nel Blocco note.

    ![Copiare il valore della directory (ID tenant) e archiviarla nel codice dell'app](media/civic-platform-tutorial/directoryid.png)

1. Copiare il valore di **ID applicazione** e archiviarlo nel Blocco note.

   ![Copiare il valore di ID applicazione (client)](media/civic-platform-tutorial/applicationid.png)

1. Passare ad **Azure Active Directory** > **Registrazioni app** e quindi selezionare l'applicazione. Selezionare **Certificati e segreti**.

1. Selezionare **Segreti client -> Nuovo segreto client**.

1. Specificare una descrizione del segreto e una durata. Al termine, fare clic su **Aggiungi**.

   > [!NOTE]
   > Il valore del segreto client verrà visualizzato dopo il salvataggio. Copiare il valore in quanto non sarà possibile recuperare la chiave in seguito.

   ![Copiare il valore del segreto perché non sarà possibile recuperarlo in seguito](media/civic-platform-tutorial/secretkey.png)

### <a name="configure-civic-platform-sso"></a>Configurare l'accesso SSO di Civic Platform

1. Aprire una nuova finestra del Web browser e accedere al sito aziendale di Atlassian Cloud come amministratore.

1. Fare clic su **Standard Choices** (Scelte standard).

    ![Collegamento di download del certificato](media/civic-platform-tutorial/standard-choices.png)

1. Creare una scelta standard **ssoconfig**.

1. Cercare **ssoconfig** e fare clic su Submit (Invia).

    ![Collegamento di download del certificato](media/civic-platform-tutorial/sso-config.png)

1. Espandere SSOCONFIG facendo clic sul punto rosso.

    ![Collegamento di download del certificato](media/civic-platform-tutorial/sso-config01.png)

1. Fornire le informazioni di configurazione correlato all'accesso SSO nel passaggio seguente:

    ![Collegamento di download del certificato](media/civic-platform-tutorial/sso-config02.png)

    1. Nel campo **applicationid** immettere il valore di **ID applicazione** copiato dal portale di Azure.

    1. Nel campo **clientSecret** immettere il valore di **Segreto** copiato dal portale di Azure.

    1. Nel campo **directoryId** immettere il valore di **ID della directory (tenant)** copiato dal portale di Azure.

    1. Immettere il valore di idpName, ad esempio `Azure`.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Civic Platform.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Civic Platform**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-civic-platform-test-user"></a>Creare l'utente di test di Civic Platform

In questa sezione viene creato un utente di nome B.Simon in Civic Platform. Collaborare con il [team di supporto clienti di Civic Platform](mailto:skale@accela.com) per aggiungere gli utenti alla piattaforma Civic Platform. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

### <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Civic Platform nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Civic Platform per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

