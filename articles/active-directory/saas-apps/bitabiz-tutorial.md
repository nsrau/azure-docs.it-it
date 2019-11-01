---
title: 'Esercitazione: Integrazione di Azure Active Directory con BitaBIZ | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e BitaBIZ.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1a51e677-c62b-4aee-9c61-56926aaaa899
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f827945cbeccacfdf048865b6e89b6947fe7de9
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73159378"
---
# <a name="tutorial-azure-active-directory-integration-with-bitabiz"></a>Esercitazione: Integrazione di Azure Active Directory con BitaBIZ

Questa esercitazione descrive come integrare BitaBIZ con Azure Active Directory (Azure AD).
L'integrazione di BitaBIZ con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a BitaBIZ.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a BitaBIZ con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con BitaBIZ, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di BitaBIZ abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* BitaBIZ supporta l'accesso SSO avviato da **SP e IDP**

## <a name="adding-bitabiz-from-the-gallery"></a>Aggiunta di BitaBIZ dalla raccolta

Per configurare l'integrazione di BitaBIZ in Azure AD, è necessario aggiungere BitaBIZ dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere BitaBIZ dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **BitaBIZ** selezionare **BitaBIZ** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![BitaBIZ nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con BitaBIZ usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in BitaBIZ.

Per configurare e testare l'accesso Single Sign-On di Azure AD con BitaBIZ, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On per BitaBIZ](#configure-bitabiz-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di BitaBIZ](#create-bitabiz-test-user)** : per avere una controparte di Britta Simon in BitaBIZ collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con BitaBIZ, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **BitaBIZ** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di BitaBIZ](common/idp-identifier.png)

    Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://www.bitabiz.com/<instanceId>`

    > [!NOTE]
    > Il valore nell'URL precedente è solo a scopo dimostrativo. Aggiornare il valore con l'attuale identificatore, come viene illustrato più avanti nell'esercitazione.

5. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![image](common/both-preintegrated-signon.png)

    Nella casella di testo **URL di accesso** digitare l'URL: `https://www.bitabiz.com/dashboard`

6. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

7. Nella sezione **Configura BitaBIZ** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-bitabiz-single-sign-on"></a>Configurare l'accesso Single Sign-On per BitaBIZ

1. In un'altra finestra del browser Web accedere al tenant BitaBIZ come amministratore.

2. Fare clic su **SETUP ADMIN** (CONFIGURA AMMINISTRATORE).

    ![Configurazione di BitaBIZ](./media/bitabiz-tutorial/settings1.png)

3. Fare clic su **Microsoft integrations** (integrazioni Microsoft) nella sezione **Add value** (Aggiungi valore).

    ![Configurazione di BitaBIZ](./media/bitabiz-tutorial/settings2.png)

4. Scorrere fino alla sezione **Microsoft Azure AD (Enable single sign on)** (Microsoft Azure AD (Abilita single sign-on)) ed eseguire la procedura seguente:

    ![Configurazione di BitaBIZ](./media/bitabiz-tutorial/settings3.png)

    a. Copiare il valore della casella di testo **Entity ID (”Identifier” in Azure AD)** (ID entità ("Identificatore" in Azure AD)) e incollarlo nella casella di testo **Identificatore** nella sezione **Configurazione SAML di base** del portale di Azure. 

    b. Nella casella di testo **Azure AD Single Sign-On Service URL** (URL del servizio Single Sign-On Azure AD) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    c. Nella casella di testo **Azure AD SAML Entity ID** (ID entità SAML Azure AD) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    d. Aprire il file del **certificato (Base64)** scaricato nel Blocco note, copiarne il contenuto negli Appunti e incollarlo nella casella di testo **Certificato di firma di Azure AD (con codifica Base64)** .

    e. Aggiungere il nome di dominio della posta elettronica aziendale, ovvero nome azienda.com nella casella di testo **Nome di dominio** per assegnare agli utenti nell'azienda il dominio di posta elettronica SSO (NON OBBLIGATORIO).

    f. Contrassegnare l'account BitaBIZ come **SSO enabled** (Abilitato a SSO).

    g. Fare clic su **Save Azure AD configuration** (Salva configurazione Azure AD) per salvare e attivare la configurazione di SSO.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon\@dominioaziendale.estensione**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a BitaBIZ.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **BitaBIZ**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **BitaBIZ**.

    ![Collegamento di BitaBIZ nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-bitabiz-test-user"></a>Creare l'utente di test di BitaBIZ

Per consentire agli utenti di Azure AD di accedere a BitaBIZ, è necessario eseguirne il provisioning in BitaBIZ.  
Nel caso di BitaBIZ, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di BitaBIZ come amministratore.

2. Fare clic su **SETUP ADMIN** (CONFIGURA AMMINISTRATORE).

    ![Aggiungi utente BitaBIZ](./media/bitabiz-tutorial/settings1.png)

3. Fare clic su **Add users** (Aggiungi utenti) nella sezione **Organization** (Organizzazione).

    ![Aggiungi utente BitaBIZ](./media/bitabiz-tutorial/user1.png)

4. Fare clic su **Add new employee** (Aggiungi nuovo dipendente).

    ![Aggiungi utente BitaBIZ](./media/bitabiz-tutorial/user2.png)

5. Nella finestra di dialogo **Add new employee** (Aggiungi nuovo dipendente) seguire questa procedura:

    ![Aggiungi utente BitaBIZ](./media/bitabiz-tutorial/user3.png)

    a. Nella casella di testo **First Name** (Nome) digitare il nome dell'utente, ad esempio Britta.

    b. Nella casella di testo **Last name** (Cognome) digitare il cognome dell'utente, ad esempio Simon.

    c. Nella casella di testo **Email** digitare l'indirizzo di posta elettronica dell'utente, ad esempio Brittasimon@contoso.com.

    d. Selezionare una data in **Date of employment** (Data di assunzione).

    e. Esistono altri attributi dell'utente non obbligatori che possono essere impostati per l'utente. Consultare il [documento relativo alla configurazione dei dipendenti](https://help.bitabiz.dk/manage-or-set-up-your-account/on-boarding-employees/new-employee) per altri dettagli.

    f. Fare clic su **Save employee** (Salva dipendente).

    > [!NOTE]
    > Il titolare dell'account Azure Active Directory riceve un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di BitaBIZ nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione BitaBIZ per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
