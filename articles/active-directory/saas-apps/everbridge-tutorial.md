---
title: 'Esercitazione: Integrazione di Azure Active Directory con Everbridge | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Everbridge.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 58d7cd22-98c0-4606-9ce5-8bdb22ee8b3e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: f8dd11e7fb0b9fda0e0f1c7d3f794f6bfd766cdf
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231459"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Esercitazione: Integrazione di Azure Active Directory con Everbridge

Questa esercitazione descrive come integrare Everbridge con Azure Active Directory (Azure AD).
Quando si integra Everbridge con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Everbridge.
* Consentire agli utenti di eseguire l'accesso automatico (Single Sign-On) a Everbridge con gli account Azure AD personali. Questo controllo di accesso è denominato accesso Single Sign-On (SSO).
* È possibile gestire gli account in un'unica posizione centrale usando il portale di Azure.
Per altre informazioni sull'integrazione di app SaaS (Software as a Service) con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Everbridge, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Everbridge che usa l'accesso Single Sign-On.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Everbridge supporta l'accesso SSO avviato da IDP.

## <a name="add-everbridge-from-the-azure-marketplace"></a>Aggiungere Everbridge da Azure Marketplace

Per configurare l'integrazione di Everbridge in Azure AD, aggiungere Everbridge da Azure Marketplace all'elenco di app SaaS gestite.

Per aggiungere Everbridge da Azure Marketplace, seguire questa procedura.

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Azure Active Directory** nel riquadro di spostamento a sinistra.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Everbridge**. Selezionare **Everbridge** nel pannello dei risultati e quindi selezionare **Aggiungi**.

     ![Everbridge nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Everbridge usando l'utente di test Britta Simon.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Everbridge.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Everbridge, completare le procedure di base seguenti:

- [Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on) per consentire agli utenti di usare questa funzionalità.
- [Configurare l'accesso Single Sign-On di Everbridge come portale di gestione di Everbridge](#configure-everbridge-as-everbridge-manager-portal-single-sign-on): per configurare le impostazioni di Single Sign-On sul lato applicazione.
- [Configurare l'accesso Single Sign-On di Everbridge come portale dei membri di Everbridge](#configure-everbridge-as-everbridge-member-portal-single-sign-on): per configurare le impostazioni di Single Sign-On sul lato applicazione.
- [Creare un utente di test di Azure AD](#create-an-azure-ad-test-user) per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
- [Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user) per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
- [Creare un utente di test di Everbridge](#create-an-everbridge-test-user): per avere una controparte di Britta Simon in Everbridge collegata alla rappresentazione dell'utente in Azure AD.
- [Testare l'accesso Single Sign-On](#test-single-sign-on) per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Everbridge, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Everbridge** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare l'accesso Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** selezionare **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

    >[!NOTE]
    >Configurare l'applicazione come portale di gestione *o* come portale dei membri sia nel portale di Azure che nel portale Everbridge.

4. Per configurare l'applicazione **Everbridge** come **portale di gestione di Everbridge** nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Everbridge](common/idp-intiated.png)

    a. Nella casella **Identificatore** immettere un URL nel formato `https://sso.everbridge.net/<API_Name>`

    b. Nella casella **URL di risposta** immettere un URL nel formato `https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore e l'URL di risposta effettivi. Per ottenere questi valori, contattare il [team di supporto di Everbridge](mailto:support@everbridge.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Per configurare l'applicazione **Everbridge** come **portale dei membri di Everbridge** nella sezione **Configurazione SAML di base** seguire questa procedura:

  * Se si vuole configurare l'applicazione in modalità avviata da IDP, seguire questa procedura:

     ![Informazioni su URL e dominio per l'accesso Single Sign-On di Everbridge per la modalità avviata da IDP](common/idp-intiated.png)

    a. Nella casella **Identificatore** immettere un URL nel formato `https://sso.everbridge.net/<API_Name>/<Organization_ID>`

    b. Nella casella **URL di risposta** immettere un URL nel formato `https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias`

   * Se si vuole configurare l'applicazione in modalità avviata da SP, selezionare **Impostare URL aggiuntivi** ed eseguire il passaggio seguente:

     ![Informazioni su URL e dominio per l'accesso Single Sign-On di Everbridge per la modalità avviata da SP](common/both-signonurl.png)

     a. Nella casella **URL di accesso** immettere un URL nel formato `https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true`

     > [!NOTE]
     > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto di Everbridge](mailto:support@everbridge.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

6. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** selezionare **Scarica** per scaricare il file **XML dei metadati della federazione**. Salvarlo nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

7. Nella sezione **Configura Everbridge** copiare gli URL necessari in base alle esigenze:

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    - URL di accesso
    - Identificatore di Azure AD
    - URL di chiusura sessione

### <a name="configure-everbridge-as-everbridge-manager-portal-single-sign-on"></a>Configurare l'accesso Single Sign-On di Everbridge come portale di gestione di Everbridge

Per configurare l'accesso SSO in **Everbridge** come applicazione del **portale di gestione di Everbridge**, seguire questa procedura.
 
1. In un'altra finestra del Web browser accedere a Everbridge come amministratore.

1. Selezionare la scheda **Settings** (Impostazioni) nel menu in alto. In **Security** (Sicurezza) selezionare **Single Sign-On**.
   
     ![Configura accesso Single Sign-On](./media/everbridge-tutorial/tutorial_everbridge_002.png)
   
     a. Nella casella **Name** (Nome) immettere il nome del provider dell'identificatore, ad esempio il nome della società.
   
     b. Nella casella **API Name** (Nome API) immettere il nome dell'API.
   
     c. Selezionare **Choose File** (Scegli file) per caricare il file di metadati scaricato dal portale di Azure.
   
     d. In **SAML Identity Location**(Percorso identità SAML) selezionare **Identity is in the NameIdentifier element of the Subject statement** (L'identità è nell'elemento NameIdentifier dell'istruzione Subject).
   
     e. Nella casella **Identity Provider Login URL** (URL di accesso del provider di identità) incollare il valore di **URL di accesso** copiato dal portale di Azure.
   
     f. Per **Service Provider Initiated Request Binding** (Binding delle richieste avviate dal provider di servizi) selezionare **HTTP Redirect** (Reindirizzamento HTTP).

     g. Selezionare **Salva**.

### <a name="configure-everbridge-as-everbridge-member-portal-single-sign-on"></a>Configurare l'accesso Single Sign-On di Everbridge come portale dei membri di Everbridge

Per configurare l'accesso Single Sign-On in **Everbridge** come **portale dei membri di Everbridge**, inviare il file **XML dei metadati della federazione** scaricato al [team di supporto di Everbridge](mailto:support@everbridge.com). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD 

Per creare l'utente di test Britta Simon nel portale di Azure, seguire questa procedura.

1. Nel riquadro di sinistra del portale di Azure selezionare **Azure Active Directory** > **Utenti** > **Tutti gli utenti**.

    ![Collegamenti Utenti e Tutti gli utenti](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. Nella finestra di dialogo **Utente** seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nella casella **Nome** immettere **BrittaSimon**.
  
    b. Nella casella **Nome utente** immettere `brittasimon@yourcompanydomain.extension`. Un esempio è BrittaSimon@contoso.com.

    c. Selezionare la casella di controllo **Mostra password**. Prendere quindi nota del valore visualizzato nella casella **Password**.

    d. Selezionare **Create**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

Abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Everbridge.

1. Nel portale di Azure selezionare **Applicazioni aziendali** > **Tutte le applicazioni** >**Everbridge**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Everbridge**.

    ![Collegamento di Everbridge nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento Utenti e gruppi](common/users-groups-blade.png)

4. Selezionare **Aggiungi utente**. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Utenti e gruppi**.

    ![Finestra di dialogo Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Users and groups** (Utenti e gruppi) selezionare **Britta Simon** nell'elenco di utenti. Scegliere **Seleziona** nella parte inferiore della schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco. Scegliere **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Assegna**.

### <a name="create-an-everbridge-test-user"></a>Creare un utente di test di Everbridge

In questa sezione viene creato l'utente di test Britta Simon in Everbridge. Per aggiungere gli utenti alla piattaforma Everbridge, collaborare con il [team di supporto di Everbridge](mailto:support@everbridge.com). Gli utenti devono essere creati e attivati in Everbridge prima di usare l'accesso Single Sign-On. 

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

Testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si seleziona il riquadro di Everbridge nel pannello di accesso, si dovrebbe accedere automaticamente all'account Everbridge per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

