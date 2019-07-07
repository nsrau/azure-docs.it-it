---
title: 'Esercitazione: Integrazione di Azure Active Directory con Fluxx Labs | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Fluxx Labs.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d8fac770-bb57-4e1f-b50b-9ffeae239d07
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: e624520a9d1f39bc8115ac72e9df0398065928f1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67102384"
---
# <a name="tutorial-azure-active-directory-integration-with-fluxx-labs"></a>Esercitazione: Integrazione di Azure Active Directory con Fluxx Labs

Questa esercitazione descrive come integrare Fluxx Labs con Azure Active Directory (Azure AD).
L'integrazione di Fluxx Labs con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Fluxx Labs.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Fluxx Labs con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Fluxx Labs sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Fluxx Labs abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Fluxx Labs supporta l'accesso SSO avviato da **IDP**

## <a name="adding-fluxx-labs-from-the-gallery"></a>Aggiunta di Fluxx Labs dalla raccolta

Per configurare l'integrazione di Fluxx Labs in Azure AD, è necessario aggiungere Fluxx Labs dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Fluxx Labs dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Fluxx Labs**, selezionare **Fluxx Labs** dal pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Fluxx Labs nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Fluxx Labs usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Fluxx Labs.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Fluxx Labs, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Fluxx Labs](#configure-fluxx-labs-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di Fluxx Labs](#create-fluxx-labs-test-user)** : per avere una controparte di Britta Simon in Fluxx Labs collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Fluxx Labs, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Fluxx Labs** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella pagina **Configura l'accesso Single Sign-On con SAML** eseguire questa procedura:

    ![Informazioni sull'accesso Single Sign-On per URL e dominio di Fluxx Labs](common/idp-intiated.png)

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente:

    | Environment | Modello URL|
    |-------------|------------|
    | Produzione | `https://<subdomain>.fluxx.io` |
    | Preproduzione | `https://<subdomain>.preprod.fluxxlabs.com`|

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente:

    | Environment | Modello URL|
    |-------------|------------|
    | Produzione | `https://<subdomain>.fluxx.io/auth/saml/callback` |
    | Preproduzione | `https://<subdomain>.preprod.fluxxlabs.com/auth/saml/callback`|

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di risposta effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Fluxx Labs](mailto:travis@fluxxlabs.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura Fluxx Labs** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-fluxx-labs-single-sign-on"></a>Configurare l'accesso Single Sign-On di Fluxx Labs

1. In un'altra finestra del Web browser accedere al sito aziendale di Fluxx Labs come amministratore.

2. Selezionare **Amministrazione** sotto la sezione **Impostazioni**.

    ![Configurazione di Fluxx Labs](./media/fluxxlabs-tutorial/config1.png)

3. Nel pannello Amministrazione selezionare **Plug-in** > **Integrazioni** e quindi selezionare **SAML SSO-(Disabilitato)**

    ![Configurazione di Fluxx Labs](./media/fluxxlabs-tutorial/config2.png)

4. Nella sezione attribute seguire questa procedura:

    ![Configurazione di Fluxx Labs](./media/fluxxlabs-tutorial/config3.png)

    a. Selezionare la casella di controllo **SAML SSO** (SSO SAML).

    b. Nella casella di testo **Request Path** (Percorso richiesta) digitare **/auth/saml**.

    c. Nella casella di testo **Callback Path** (Percorso callback) digitare **/auth/saml/callback**.

    d. Nella casella di testo **Assertion Consumer Service URL (Single Sign-On URL)** (URL del servizio consumer di asserzione - URL Single Sign-On) immettere il valore di **URL di risposta** immesso nel portale di Azure.

    e. Nella casella di testo **Audience (SP Entity ID)** (Destinatari - ID entità provider di servizi) immettere il valore di **Identificatore** immesso nel portale di Azure.

    f. Nella casella di testo **Identity Provider SSO Target URL** (URL di destinazione SSO del provider di identità) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    g. Aprire il certificato con codifica Base 64 nel Blocco note, copiarne il contenuto negli Appunti e quindi incollarlo nella casella di testo **Certificato provider di identità**.

    h. Nella casella di testo **Formato identificatore nome** immettere il valore `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.

    i. Fare clic su **Save**.

    > [!NOTE]
    > Una volta salvato il contenuto, il campo appare vuoto per motivi di sicurezza, ma il valore è stato salvato nella configurazione.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD 

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare brittasimon@yourcompanydomain.extension. Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Fluxx Labs.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Fluxx Labs**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Fluxx Labs**.

    ![Collegamento di Fluxx Labs nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-fluxx-labs-test-user"></a>Creare l'utente di test di Fluxx Labs

Per consentire agli utenti di Azure AD di accedere a Fluxx Labs, è necessario effettuarne il provisioning in Fluxx Labs. Nel caso di Fluxx Labs il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di Fluxx Labs come amministratore.

2. Fare clic sull'**icona** visualizzata di seguito.

    ![Configurazione di Fluxx Labs](./media/fluxxlabs-tutorial/config6.png)

3. Nel dashboard fare clic sull'icona visualizzata di seguito per aprire la scheda **New PEOPLE** (Nuove PERSONE).

    ![Configurazione di Fluxx Labs](./media/fluxxlabs-tutorial/config4.png)

4. Nella sezione **NEW PEOPLE** (Nuove persone) seguire questa procedura:

    ![Configurazione di Fluxx Labs](./media/fluxxlabs-tutorial/config5.png)

    a. Fluxx Labs usa l'account di posta elettronica come identificatore univoco per l'accesso Single Sign-On. Compilare il campo **SSO UID** (UID SSO) con l'indirizzo di posta elettronica dell'utente, corrispondente all'indirizzo di posta elettronica usato come account di accesso con SSO.

    b. Fare clic su **Save**.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Fluxx Labs nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Fluxx Labs per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

