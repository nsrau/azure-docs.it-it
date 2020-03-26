---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Nintex Promapp | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Nintex Promapp.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 418d0601-6e7a-4997-a683-73fa30a2cfb5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/30/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 581c850801c153996031378cbf470457264cad3d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "76984462"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nintex-promapp"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Nintex Promapp

Questa esercitazione descrive come integrare Nintex Promapp con Azure Active Directory (Azure AD). Integrando Nintex Promapp with Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Nintex Promapp.
* Abilitare gli utenti per l'accesso automatico a Nintex Promapp con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Nintex Promapp abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Nintex Promapp supporta l'accesso SSO avviato da **SP e IDP**
* Nintex Promapp supporta il provisioning utenti **JIT**

## <a name="adding-nintex-promapp-from-the-gallery"></a>Aggiunta di Nintex Promapp dalla raccolta

Per configurare l'integrazione di Nintex Promapp in Azure AD, è necessario aggiungere Nintex Promapp dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Nintex Promapp** nella casella di ricerca.
1. Selezionare **Nintex Promapp** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-nintex-promapp"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Nintex Promapp

Configurare e testare l'accesso SSO di Azure AD con Nintex Promapp usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Nintex Promapp.

Per configurare e testare l'accesso SSO di Azure AD con Nintex Promapp, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    * **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    * **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Nintex Promapp](#configure-nintex-promapp-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    * **[Creare l'utente di test di Nintex Promapp](#create-nintex-promapp-test-user)** : per avere una controparte di B.Simon in Nintex Promapp collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Nintex Promapp** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    1. Nella casella **Identificatore** immettere un URL nel formato seguente:

        | |
        |--|
        | `https://go.promapp.com/TENANTNAME/`|
        | `https://au.promapp.com/TENANTNAME/`|
        | `https://us.promapp.com/TENANTNAME/`|
        | `https://eu.promapp.com/TENANTNAME/`|
        | `https://ca.promapp.com/TENANTNAME/`|
        |   |

       > [!NOTE]
       > L'integrazione di Azure AD con Nintex Promapp è configurata solo per l'autenticazione avviata dal servizio, vale a dire che, passando a un URL di Nintex Promapp, viene avviato il processo di autenticazione, ma il campo **URL di risposta** è un campo obbligatorio.

    1. Nella casella **URL di risposta** immettere un URL nel formato seguente:

       `https://<DOMAINNAME>.promapp.com/TENANTNAME/saml/authenticate.aspx`

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella **URL di accesso** immettere un URL nel formato seguente: `https://<DOMAINNAME>.promapp.com/TENANTNAME/saml/authenticate`

    > [!NOTE]
    > Questi valori sono segnaposto. È necessario usare l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere i valori, contattare il [team di supporto di Nintex Promapp](https://www.promapp.com/about-us/contact-us/). È anche possibile fare riferimento ai modelli disponibili nella finestra di dialogo **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura Nintex Promapp** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Nintex Promapp.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Nintex Promapp**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-nintex-promapp-sso"></a>Configurare l'accesso Single Sign-On di Nintex Promapp

1. Accedere al sito aziendale di Nintex Promapp come amministratore.

2. Nel menu nella parte superiore della finestra selezionare **Admin** (Amministratore):

    ![Selezionare Admin (Amministratore)][12]

3. Selezionare **Configure** (Configura):

    ![Selezionare Configure (Configura)][13]

4. Nella finestra di dialogo **Security** (Sicurezza) seguire questa procedura.

    ![Finestra di dialogo Security (Sicurezza)][14]

    1. Incollare l'**URL di accesso** copiato dal portale di Azure nella casella **SSO-Login URL** (URL di accesso SSO).

    1. Nell'elenco **SSO - Single Sign-on Mode** (SSO - Modalità Single Sign-On) selezionare **Optional** (Facoltativo). Selezionare **Salva**.

       > [!NOTE]
       > La modalità facoltativa serve solo a scopo di test. Quando si è soddisfatti della configurazione, selezionare **Required** (Obbligatorio) nell'elenco **SSO - Single Sign-on Mode** (SSO - Modalità Single Sign-On) per imporre a tutti gli utenti l'autenticazione con Azure AD.

    1. Nel Blocco note aprire il certificato scaricato nella sezione precedente. Copiare il contenuto del certificato senza la prima riga ( **-----BEGIN CERTIFICATE-----** ) o l'ultima riga ( **-----END CERTIFICATE-----** ). Incollare il contenuto del certificato nella casella **SSO-x.509 Certificate** (SSO - Certificato x.509) e quindi selezionare **Save** (Salva).

### <a name="create-nintex-promapp-test-user"></a>Creare l'utente di test di Nintex Promapp

In questa sezione viene creato un utente di nome B.Simon in Nintex Promapp. Nintex Promapp supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in Nintex Promapp, ne viene creato uno nuovo dopo l'autenticazione.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Nintex Promapp nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Nintex Promapp per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare Nintex Promapp con Azure AD](https://aad.portal.azure.com/)

<!--Image references-->

[12]: ./media/promapp-tutorial/tutorial_promapp_05.png
[13]: ./media/promapp-tutorial/tutorial_promapp_06.png
[14]: ./media/promapp-tutorial/tutorial_promapp_07.png