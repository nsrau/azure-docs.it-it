---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Screencast-O-Matic | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Screencast-O-Matic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 525ad47d-5488-40e2-aeaf-ae6183745682
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc887e95b6fa6f8b17fbbb3dbaae5105385a07fa
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132147"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-screencast-o-matic"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Screencast-O-Matic

Questa esercitazione descrive come integrare Screencast-O-Matic con Azure Active Directory (Azure AD). Integrando Screencast-O-Matic con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Screencast-O-Matic.
* Abilitare gli utenti per l'accesso automatico a Screencast-O-Matic con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Screencast-O-Matic abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Screencast-O-Matic supporta l'accesso SSO avviato da **SP**
* Screencast-O-Matic supporta il provisioning utenti **Just-In-Time**

## <a name="adding-screencast-o-matic-from-the-gallery"></a>Aggiunta di Screencast-O-Matic dalla raccolta

Per configurare l'integrazione di Screencast-O-Matic in Azure AD, è necessario aggiungere Screencast-O-Matic dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Screencast-O-Matic** nella casella di ricerca.
1. Selezionare **Screencast-O-Matic** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-screencast-o-matic"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Screencast-O-Matic

Configurare e testare l'accesso SSO di Azure AD con Screencast-O-Matic usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Screencast-O-Matic.

Per configurare e testare l'accesso SSO di Azure AD con Screencast-O-Matic, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    * **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    * **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Screencast-O-Matic](#configure-screencast-o-matic-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    * **[Creare l'utente di test di Screencast-O-Matic](#create-screencast-o-matic-test-user)** : per avere una controparte di B.Simon in Screencast-O-Matic collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Screencast-O-Matic** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://screencast-o-matic.com/<InstanceName>`

    > [!NOTE]
    > Poiché non è reale, è necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere questo valore, contattare il [team di supporto clienti di Screencast-O-Matic](mailto:support@screencast-o-matic.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il file **XML dei metadati della federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura Screencast-O-Matic** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Screencast-O-Matic.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Screencast-O-Matic**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-screencast-o-matic-sso"></a>Configurare l'accesso Single Sign-On di Screencast-O-Matic

1. Per automatizzare la configurazione all'interno di Screencast-O-Matic, è necessario installare l'**estensione del browser per l'accesso sicuro ad App personali** facendo clic su **Installa l'estensione**.

    ![Estensione per App personali](common/install-myappssecure-extension.png)

1. Dopo aver aggiunto l'estensione al browser, fare clic su **Configura Screencast-O-Matic** per passare direttamente all'applicazione Screencast-O-Matic. Nell'applicazione specificare le credenziali di amministratore per accedere a Screencast-O-Matic. L'estensione del browser configurerà automaticamente l'applicazione e automatizzerà i passaggi da 3 a 11.

    ![Eseguire la configurazione](common/setup-sso.png)

1. Se si vuole configurare manualmente Screencast-O-Matic, aprire una nuova finestra del Web browser, accedere al sito aziendale di Screencast-O-Matic come amministratore e seguire questa procedura:

1. Fare clic su **Subscription** (Sottoscrizione).

    ![Sottoscrizione](./media/screencast-tutorial/tutorial_screencast_sub.png)

1. Nella sezione **Access page** (Pagina di accesso) fare clic su **Setup** (Configurazione).

    ![Accesso](./media/screencast-tutorial/tutorial_screencast_setup.png)

1. In **Setup Access Page** (Pagina di accesso di configurazione) seguire questa procedura.

1. Nella sezione **Access URL** (URL di accesso) immettere il nome dell'istanza nella casella di testo specificata.

    ![Accesso](./media/screencast-tutorial/tutorial_screencast_access.png)

1. Selezionare **Require Domain User** (Richiedi utente di dominio) nella sezione **SAML User Restriction (optional)** (Restrizione utente SAML (facoltativa)).

1. In **Upload IDP Metadata XML File** (Carica file XML metadati IDP) fare clic su **Choose File** (Scegli file) per caricare i metadati scaricati dal portale di Azure.

1. Fare clic su **OK**.

    ![Accesso](./media/screencast-tutorial/tutorial_screencast_save.png)

### <a name="create-screencast-o-matic-test-user"></a>Creare l'utente di test di Screencast-O-Matic

In questa sezione viene creato un utente di nome Britta Simon in Screencast-O-Matic. Screencast-O-Matic supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in Screencast-O-Matic, ne viene creato uno nuovo dopo l'autenticazione. Per creare un utente manualmente, contattare il [team del supporto clienti di Screencast-O-Matic](mailto:support@screencast-o-matic.com).

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Screencast-O-Matic nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Screencast-O-Matic per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare Screencast-O-Matic con Azure AD](https://aad.portal.azure.com/)