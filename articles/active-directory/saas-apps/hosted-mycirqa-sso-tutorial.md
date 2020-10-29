---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Hosted MyCirqa SSO | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Hosted MyCirqa SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/26/2019
ms.author: jeedes
ms.openlocfilehash: 06918f6d9c62624915be327b61aa6a3ee4e2b12a
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92442755"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-hosted-mycirqa-sso"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Hosted MyCirqa SSO

Questa esercitazione descrive come integrare Hosted MyCirqa SSO con Azure Active Directory (Azure AD). Integrando Hosted MyCirqa SSO con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Hosted MyCirqa SSO.
* Abilitare gli utenti per l'accesso automatico a Hosted MyCirqa SSO con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Hosted MyCirqa SSO abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Hosted MyCirqa SSO supporta l'accesso SSO avviato da **SP**

## <a name="adding-hosted-mycirqa-sso-from-the-gallery"></a>Aggiunta di Hosted MyCirqa SSO dalla raccolta

Per configurare l'integrazione di Hosted MyCirqa SSO in Azure AD, è necessario aggiungere Hosted MyCirqa SSO dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory** .
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni** .
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione** .
1. Nella sezione **Aggiungi dalla raccolta** digitare **Hosted MyCirqa SSO** nella casella di ricerca.
1. Selezionare **Hosted MyCirqa SSO** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-hosted-mycirqa-sso"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Hosted MyCirqa SSO

Configurare e testare l'accesso SSO di Azure AD con Hosted MyCirqa SSO usando un utente di test di nome **B.Simon** . Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Hosted MyCirqa SSO.

Per configurare e testare l'accesso SSO di Azure AD con Hosted MyCirqa SSO, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    * **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    * **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Hosted MyCirqa](#configure-hosted-mycirqa-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    * **[Creare l'utente di test di Hosted MyCirqa SSO](#create-hosted-mycirqa-sso-test-user)** : per avere una controparte di B.Simon in Hosted MyCirqa SSO collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Hosted MyCirqa SSO** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On** .
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML** .
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<SUBDOMAIN>.cirqahosting.com/CirqaIdentity/external?`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `https://isoxford.com/<CUSTOMID>/cirqaidentity/saml2`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Hosted MyCirqa SSO](not sure). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante Copia per copiare l' **URL dei metadati di federazione dell'app** e salvarlo nel computer.

    ![Collegamento di download del certificato](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory** , **Utenti** e quindi **Tutti gli utenti** .
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password** .
   1. Fare clic su **Crea** .

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Hosted MyCirqa SSO.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni** .
1. Nell'elenco delle applicazioni selezionare **Hosted MyCirqa SSO** .
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi** .

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione** .

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna** .

## <a name="configure-hosted-mycirqa-sso"></a>Configurare l'accesso Single Sign-On di Hosted MyCirqa

Per configurare l'accesso Single Sign-On sul lato **Hosted MyCirqa SSO** , è necessario inviare l' **URL dei metadati di federazione dell'app** al [team di supporto di Hosted MyCirqa SSO](mailto:support@isoxford.com). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

## <a name="create-hosted-mycirqa-sso-test-user"></a>Creare l'utente di test di Hosted MyCirqa SSO

In questa sezione viene creato un utente di nome Britta Simon in Hosted MyCirqa SSO. Collaborare con il [team di supporto di Hosted MyCirqa SSO](mailto:support@isoxford.com) per aggiungere gli utenti alla piattaforma Hosted MyCirqa SSO. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Hosted MyCirqa SSO nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Hosted MyCirqa SSO per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](./tutorial-list.md)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)

- [Provare Hosted MyCirqa SSO con Azure AD](https://aad.portal.azure.com/)