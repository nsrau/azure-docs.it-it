---
title: 'Esercitazione: Integrazione di Azure Active Directory con Elium | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory ed Elium.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: fae344b3-5bd9-40e2-9a1d-448dcd58155f
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/05/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 85d9c56fa1f0da5bf4747693ebe2361205e83ba0
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56196740"
---
# <a name="tutorial-azure-active-directory-integration-with-elium"></a>Esercitazione: Integrazione di Azure Active Directory con Elium

Questa esercitazione descrive come integrare Elium con Azure Active Directory (Azure AD).
L'integrazione di Elium con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Elium.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Elium con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Elium, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Elium abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Elium supporta l'accesso SSO avviato da **SP** e **IDP**

* Elium supporta il provisioning utenti **JIT**

## <a name="adding-elium-from-the-gallery"></a>Aggiunta di Elium dalla raccolta

Per configurare l'integrazione di Elium in Azure AD, è necessario aggiungere Elium dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Elium dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Elium**, selezionare **Elium** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Elium nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Elium usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Elium.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Elium, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On per Elium](#configure-elium-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di Elium](#create-elium-test-user)**: per avere una controparte di Britta Simon in Elium collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Elium, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Elium** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare l'accesso Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Elium](common/idp-intiated.png)

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<platform-domain>.elium.com/login/saml2/metadata`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<platform-domain>.elium.com/login/saml2/acs`

5. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Elium](common/metadata-upload-additional-signon.png)

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<platform-domain>.elium.com/login/saml2/login`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, È possibile ottenere questi valori dal **file di metadati SP** che è possibile scaricare all'indirizzo `https://<platform-domain>.elium.com/login/saml2/metadata` e che viene illustrato più avanti in questa esercitazione.

6. L'applicazione Elium prevede un formato specifico per le asserzioni SAML. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente** nella pagina di integrazione dell'applicazione. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Attributi utente**.

    ![image](common/edit-attribute.png)

7. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** modificare le attestazioni usando l'**icona Modifica** o aggiungere le attestazioni usando l'opzione **Aggiungi nuova attestazione** per configurare l'attributo del token SAML come mostrato nell'immagine precedente e seguire questa procedura: 

    | Nome | Attributo di origine|
    | ---------------| ----------------|
    | email   |user.mail |
    | first_name| user.givenname |
    | last_name| user.surname|
    | job_title| user. jobtitle|
    | company| user.companyname|

    > [!NOTE]
    > Queste sono le attestazioni predefinite. **Solo l'attestazione email è obbligatoria**. Anche per il provisioning JIT è obbligatoria solo l'attestazione email. Altre attestazioni personalizzate possono variare in base alla piattaforma del cliente.

    a. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Lasciare vuota la casella **Spazio dei nomi**.

    d. Per Origine selezionare **Attributo**.

    e. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo indicato per la riga.

    f. Fare clic su **Ok**

    g. Fare clic su **Save**.

8. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

9. Nella sezione **Configura Elium** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-elium-single-sign-on"></a>Configurare l'accesso Single Sign-On per Elium

1. In una finestra del Web browser accedere al sito della società Elium come amministratore.

2. Fare clic **sul profilo**utente nell'angolo in alto a destra e quindi selezionare **Administration** (Amministrazione).

    ![Configurare l'accesso Single Sign-On](./media/elium-tutorial/user1.png)

3. Selezionare la scheda **Security** (Sicurezza).

    ![Configurare l'accesso Single Sign-On](./media/elium-tutorial/user2.png)

4. Scorrere fino alla sezione **Single sign-on (SSO)** e seguire questa procedura:

    ![Configurare l'accesso Single Sign-On](./media/elium-tutorial/user3.png)

    a. Copiare il valore di **Verify that SAML2 authentication works for your account** (Verificare il funzionamento dell'autenticazione SAML2 per l'account) e incollarlo nella casella di testo **URL accesso** nella sezione **Configurazione SAML di base** del portale di Azure.

    > [!NOTE]
    > Dopo avere configurato SSO, è sempre possibile accedere alla pagina di accesso remoto all'URL seguente: `https://<platform_domain>/login/regular/login` 

    b. Selezionare la casella di controllo **Enable SAML2 federation** (Abilita federazione SAML2).

    c. Selezionare la casella di controllo **JIT Provisioning** (Provisioning JIT).

    d. Aprire **SP Metadata** (Metadati SP) facendo clic su **Download**.

    e. Cercare **entityID** nel file **SP Metadata** (Metadati SP), copiare il valore di **entityID** e incollarlo nella casella di testo **Identificatore** nella sezione **Configurazione SAML di base** del portale di Azure. 

    ![Configurare l'accesso Single Sign-On](./media/elium-tutorial/user4.png)

    f. Cercare **AssertionConsumerService** nel file **SP Metadata** (Metadati SP), copiare il valore di **Location** e incollarlo nella casella di testo **URL di risposta** nella sezione **Configurazione SAML di base** del portale di Azure.

    ![Configurare l'accesso Single Sign-On](./media/elium-tutorial/user5.png)

    g. Copiare il contenuto del file dei metadati scaricato dal portale di Azure nel Blocco note e incollarlo nella casella di testo **IdP Metadata** (Metadati IdP).

    h. Fare clic su **Save**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD 

Questa sezione descrive come creare un utente di test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon@yourcompanydomain.extension**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Elium.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Elium**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Elium**.

    ![Collegamento di Elium nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-elium-test-user"></a>Creare l'utente di test di Elium

In questa sezione viene creato un utente di nome Britta Simon in Elium. Elium supporta il **provisioning JIT**, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in Elium, ne viene creato uno nuovo quando si prova ad accedere a Elium.

>[!Note]
>Se è necessario creare un utente manualmente, contattare il [team di supporto di Elium](mailto:support@elium.com).

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Elium nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Elium per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

