---
title: 'Esercitazione: Integrazione di Azure Active Directory con N2F - Expense reports | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e N2F - Expense reports.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f56d53d7-5a08-490a-bfb9-78fefc2751ec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: 7380cf6727817d99edbedf5552c8bea42f177074
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "65744479"
---
# <a name="tutorial-azure-active-directory-integration-with-n2f---expense-reports"></a>Esercitazione: Integrazione di Azure Active Directory con N2F - Expense reports

Questa esercitazione descrive come integrare N2F - Expense reports con Azure Active Directory (Azure AD).
L'integrazione di N2F - Expense reports con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a N2F - Expense reports.
* È possibile abilitare gli utenti per l'accesso automatico a N2F - Expense reports (Single Sign-On) con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con N2F - Expense reports, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di N2F - Expense reports abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* N2F - Expense reports supporta l'accesso SSO avviato da **SP** e **IDP**

## <a name="adding-n2f---expense-reports-from-the-gallery"></a>Aggiunta di N2F - Expense reports dalla raccolta

Per configurare l'integrazione di N2F - Expense reports in Azure AD, è necessario aggiungere N2F - Expense reports dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere N2F - Expense reports dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **N2F - Expense reports**, selezionare **N2F - Expense reports** dal pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![N2F - Expense reports nell'elenco dei risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con N2F - Expense reports usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in N2F - Expense reports.

Per configurare e testare l'accesso Single Sign-On di Azure AD con N2F - Expense reports, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di N2F - Expense reports](#configure-n2f---expense-reports-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di N2F - Expense reports](#create-n2f---expense-reports-test-user)**: per avere una controparte di Britta Simon in N2F - Expense reports collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con N2F - Expense reports, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **N2F - Expense reports** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base**, se si vuole configurare l'applicazione in modalità avviata da **IDP**, non è necessario eseguire alcun passaggio, perché l'app è già preintegrata in Azure.

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di N2F - Expense reports](common/preintegrated.png)

5. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di N2F - Expense reports](common/metadata-upload-additional-signon.png)

    Nella casella di testo **URL di accesso** digitare un URL: `https://www.n2f.com/app/`

6. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/copy-metadataurl.png)

7. Nella sezione **Configura myPolicies** copiare gli URL appropriati in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-n2f---expense-reports-single-sign-on"></a>Configurazione di N2F - Expense reports Single Sign-On

1. In un'altra finestra del Web browser accedere al sito aziendale di N2F - Expense reports come amministratore.

2. Fare clic su **Settings** (Impostazioni) e quindi selezionare **Advance Settings** (Impostazioni avanzate) dall'elenco a discesa.

    ![Configurazione di N2F - Expense reports](./media/n2f-expensereports-tutorial/configure1.png)

3. Selezionare la scheda **Account settings** (Impostazioni account).

    ![Configurazione di N2F - Expense reports](./media/n2f-expensereports-tutorial/configure2.png)

4. Selezionare **Authentication** (Autenticazione) e quindi la scheda **Add an authentication method** (Aggiungi un metodo di autenticazione).

    ![Configurazione di N2F - Expense reports](./media/n2f-expensereports-tutorial/configure3.png)

5. Selezionare **SAML Microsoft Office 365** come metodo di autenticazione.

    ![Configurazione di N2F - Expense reports](./media/n2f-expensereports-tutorial/configure4.png)

6. Nella sezione **Authentication method** (Metodo di autenticazione) seguire questa procedura:

    ![Configurazione di N2F - Expense reports](./media/n2f-expensereports-tutorial/configure5.png)

    a. Nella casella di testo **Entity ID** (ID entità) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    b. Nella casella di testo **Metadata URL** (URL dei metadati) incollare il valore dell'**URL dei metadati di federazione dell'app** copiato dal portale di Azure.

    c. Fare clic su **Save**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD 

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

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a N2F - Expense reports.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **N2F - Expense reports**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **N2F - Expense reports**.

    ![Collegamento di N2F - Expense reports nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-n2f---expense-reports-test-user"></a>Creazione di un utente di test di N2F - Expense reports

Per consentire agli utenti di Azure AD di accedere a N2F - Expense reports, è necessario effettuarne il provisioning in N2F - Expense reports. Nel caso di N2F - Expense reports, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di N2F - Expense reports come amministratore.

2. Fare clic su **Settings** (Impostazioni) e quindi selezionare **Advance Settings** (Impostazioni avanzate) dall'elenco a discesa.

    ![Aggiungere un utente in N2F - Expense reports](./media/n2f-expensereports-tutorial/configure1.png)

3. Selezionare la scheda **Users** (Utenti) dal riquadro di spostamento a sinistra.

    ![Configurazione di N2F - Expense reports](./media/n2f-expensereports-tutorial/user1.png)

4. Selezionare la scheda **New user** (Nuovo utente).

    ![Configurazione di N2F - Expense reports](./media/n2f-expensereports-tutorial/user2.png)

5. Nella sezione **User** (Utente) seguire questa procedura:

    ![Configurazione di N2F - Expense reports](./media/n2f-expensereports-tutorial/user3.png)

    a. Nella casella di testo **Email address** (Indirizzo di posta elettronica) immettere l'indirizzo di posta elettronica dell'utente, ad esempio **brittasimon\@contoso.com**.

    b. Nella casella di testo **First name** (Nome) immettere il nome dell'utente, ad esempio **Britta**.

    c. Nella casella di testo **Name** (Nome) immettere il nome dell'utente, ad esempio **BrittaSimon**.

    d. Scegliere **Role, Direct manager (N+1)** (Ruolo, Responsabile diretto N+1) e **Division** (Reparto) in base alle esigenze della propria organizzazione.

    e. Fare clic su **Validate and send invitation** (Convalida e invia invito).

    > [!NOTE]
    > Se si riscontrano problemi durante l'aggiunta dell'utente, contattare il [team di supporto di N2F - Expense reports](mailto:support@n2f.com).

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro N2F - Expense reports nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione N2F - Expense reports per la quale si è impostato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

