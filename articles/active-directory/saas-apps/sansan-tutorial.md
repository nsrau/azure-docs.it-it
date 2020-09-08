---
title: 'Esercitazione: Integrazione di Azure Active Directory con Sansan | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Sansan.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/27/2020
ms.author: jeedes
ms.openlocfilehash: fa4e91a087c7dcfce247cacc2dff83458bc87f64
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89079983"
---
# <a name="tutorial-integrate-sansan-with-azure-active-directory"></a>Esercitazione: Integrazione di Sansan con Azure Active Directory

Questa esercitazione descrive come integrare Sansan con Azure Active Directory (Azure AD). Integrando Sansan con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Sansan.
* Abilitare gli utenti per l'accesso automatico a Sansan con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Sansan abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.
* Sansan supporta l'accesso Single Sign-On avviato da **SP**.
* Dopo aver configurato Sansan è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-sansan-from-the-gallery"></a>Aggiunta di Sansan dalla raccolta

Per configurare l'integrazione di Sansan in Azure AD, è necessario aggiungere Sansan dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Sansan** nella casella di ricerca.
1. Selezionare **Sansan** nel riquadro dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso"></a>Configurare e testare l'accesso SSO di Azure AD

Configurare e testare l'accesso Single Sign-On di Azure AD con Sansan usando un utente di test di nome **Britta Simon**. Per consentire il funzionamento del Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Sansan.

Per configurare e testare l'accesso SSO di Azure AD con Sansan, completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** per consentire agli utenti di usare questa funzionalità.
   * **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
   * **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare Sansan](#configure-sansan)** per configurare le impostazioni di Single Sign-On sul lato applicazione.
   * **[Creare un utente di test di Sansan](#create-sansan-test-user)** per avere una controparte di Britta Simon in Sansan collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Sansan** del [portale di Azure](https://portal.azure.com/) trovare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella pagina **Configurazione SAML di base** immettere i valori per i campi seguenti:

    1. Nella casella di testo **URL di accesso** digitare l'URL: `https://ap.sansan.com/`

   1. Nella casella di testo **Identificatore (ID entità)** digitare l'URL:   
   `https://ap.sansan.com/saml2/<company name>`

   1. Nella casella di testo **URL di risposta** digitare uno degli URL nel formato seguente:

    
       | Environment | URL |
      |:--- |:--- |
      | PC |`https://ap.sansan.com/v/saml2/<company name>/acs` |
      | App per smartphone |`https://internal.api.sansan.com/<company name>/acs` |
      | Web smartphone |`https://ap.sansan.com/s/saml2/<company name>/acs` |

    > [!NOTE]
    > Poiché questi non sono i valori reali, Controllare i valori effettivi nelle **impostazioni di amministrazione di Sansan**.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML**, trovare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer in uso.

   ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura Sansan** copiare gli URL appropriati in base alle proprie esigenze.

   ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `Britta Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `BrittaSimon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione Britta Simon verrà abilitata per l'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a Sansan.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Sansan**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-sansan"></a>Configurare Sansan

Per eseguire le impostazioni **Single Sign-on** sul lato **Sansan**, seguire questa procedura in base ai requisiti.

   * Versione [giapponese](https://jp-help.sansan.com/hc/ja/articles/900001551383 ).

   * Versione [inglese](https://jp-help.sansan.com/hc/en-us/articles/900001551383 ).


### <a name="create-sansan-test-user"></a>Creare un utente di test di Sansan

In questa sezione viene creato un utente chiamato Britta Simon in Sansan. Per altre informazioni su come creare un utente, vedere [questa](https://jp-help.sansan.com/hc/en-us/articles/206508997-Adding-users) procedura.

## <a name="test-sso"></a>Testare l'accesso SSO

Quando si fa clic sul riquadro di Sansan nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Sansan per cui è stato configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)