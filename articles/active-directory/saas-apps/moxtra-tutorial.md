---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Moxtra | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Moxtra.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2aed2d4b-1dcd-4839-8fed-9419d107c61c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3e53ba11744b0e78287ffc46c4aac7b99b16b23
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74889635"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-moxtra"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Moxtra

Questa esercitazione descrive come integrare Moxtra con Azure Active Directory (Azure AD). Integrando Moxtra con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Moxtra.
* Abilitare gli utenti per l'accesso automatico a Moxtra con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Moxtra abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Moxtra supporta l'accesso SSO avviato da **SP**

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.

## <a name="adding-moxtra-from-the-gallery"></a>Aggiunta di Moxtra dalla raccolta

Per configurare l'integrazione di Moxtra in Azure AD, è necessario aggiungere Moxtra dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Moxtra** nella casella di ricerca.
1. Selezionare **Moxtra** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-moxtra"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Moxtra

Configurare e testare l'accesso SSO di Azure AD con Moxtra usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Moxtra.

Per configurare e testare l'accesso SSO di Azure AD con Moxtra, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Moxtra](#configure-moxtra-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Moxtra](#create-moxtra-test-user)** : per avere una controparte di B.Simon in Moxtra collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Moxtra** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    Nella casella di testo **URL di accesso** digitare un URL: `https://www.moxtra.com/service/#login`

1. L'applicazione Moxtra prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti. Fare clic su **Modifica** per aprire la finestra di dialogo Attributi utente.

    ![image](common/edit-attribute.png)

1. Oltre quelli elencati in precedenza, l'applicazione Moxtra prevede il passaggio di qualche altro attributo nella risposta SAML. Nella sezione Attestazioni utente della finestra di dialogo Attributi utente eseguire la procedura seguente per aggiungere l'attributo del token SAML come illustrato nella tabella seguente:

    | NOME | Attributo di origine|
    | ------------------- | -------------------- |    
    | firstname | user.givenname |
    | lastname | user.surname |
    | idpid    | < Identificatore Azure AD >

    > [!Note]
    > Il valore dell'attributo **idpid** non è reale. È possibile ottenere il valore effettivo dalla sezione **Configura Moxtra** dal passaggio 8. 

    1. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    1. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    1. Lasciare vuota la casella **Spazio dei nomi**.

    1. Per Origine selezionare **Attributo**.

    1. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo indicato per la riga.

    1. Fare clic su **OK**.

    1. Fare clic su **Save**.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura Moxtra** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Moxtra.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Moxtra**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-moxtra-sso"></a>Configurare l'accesso Single Sign-On di Moxtra

1. In un'altra finestra del browser, accedere al sito aziendale di Moxtra come amministratore.

2. Nella barra degli strumenti a sinistra, fare clic su**Admin Console > SAML Single Sign-On** (Console di amministrazione > Single Sign-On SAML), quindi su **New** (Nuovo).
   
    ![Configure Single Sign-On](./media/moxtra-tutorial/tutorial_moxtra_06.png) 

3. Nella pagina **SAML** eseguire i passaggi seguenti:
   
    ![Configure Single Sign-On](./media/moxtra-tutorial/tutorial_moxtra_08.png)   
 
    a. Nella casella di testo **Name** (Nome) digitare un nome per la configurazione, ad esempio *SAML*. 
  
    b. Nella casella di testo **IdP Entity ID** (ID entità provider di identità) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure. 
 
    c. Nella casella di testo **Login URL** (URL di accesso) incollare il valore di **URL di accesso** copiato dal portale di Azure. 
 
    d. Nella casella di testo **AuthnContextClassRef** digitare **urn:oasis:names:tc:SAML:2.0:ac:classes:Password**. 
 
    e. Nella casella di testo **NameID Format** (Formato NameID) digitare **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**. 
 
    f. Aprire il certificato scaricato dal portale di Azure nel Blocco note, copiarne il contenuto e incollarlo nella casella di testo **Certificate** (Certificato).    
 
    g. Nella casella di testo dominio email SAML, digitare il dominio email SAML.    
  
    >[!NOTE]
    >Per verificare il dominio, fare clic su “**i**” di seguito.

    h. Fare clic su **Update**.

### <a name="create-moxtra-test-user"></a>Creare l'utente di test di Moxtra

Questa sezione descrive come creare un utente di nome B.Simon in Moxtra.

**Per creare un utente di nome B.Simon in Moxtra, seguire questa procedura:**

1. Accedere al sito aziendale di Moxtra come amministratore.

1. Nella barra degli strumenti a sinistra, fare clic su**Admin Console > Gestione utente** e quindi su **Aggiungi utente**.
   
    ![Configure Single Sign-On](./media/moxtra-tutorial/tutorial_moxtra_10.png) 

1. Nella finestra di dialogo **Aggiungi utente** seguire questa procedura:
  
    a. Nella casella di testo **First Name** (Nome) digitare **B**.
  
    b. Nella casella di testo **Cognome** digitare **Simon**.
  
    c. Nella casella di testo **Email** (Posta elettronica) digitare l'indirizzo di posta elettronica di B.Simon come nel portale di Azure.
  
    d. Nella casella di testo **Divisione**, digitare **Dev**.
  
    e. Nella casella di testo **Reparto**, digitare **IT**.
  
    f. Selezionare **Administrator**.
  
    g. Fare clic su **Aggiungi**.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Moxtra nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Moxtra per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare Moxtra con Azure AD](https://aad.portal.azure.com/)

