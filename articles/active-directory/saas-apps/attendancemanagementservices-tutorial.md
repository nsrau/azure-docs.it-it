---
title: 'Esercitazione: Integrazione di Azure Active Directory con Attendance Management Services | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Attendance Management Services.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1f56e612-728b-4203-a545-a81dc5efda00
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9449e196a59fa20288bca9d4401c5e56a97b86cd
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65901211"
---
# <a name="tutorial-azure-active-directory-integration-with-attendance-management-services"></a>Esercitazione: Integrazione di Azure Active Directory con Attendance Management Services

Questa esercitazione descrive come integrare Attendance Management Services con Azure Active Directory (Azure AD).
L'integrazione di Attendance Management Services con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere ad Attendance Management Services.
* È possibile abilitare gli utenti per l'accesso automatico ad Attendance Management Services (Single Sign-On) con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Attendance Management Services, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Attendance Management Services abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Attendance Management Services supporta l'accesso Single Sign-On avviato da **SP**

## <a name="adding-attendance-management-services-from-the-gallery"></a>Aggiunta di Attendance Management Services dalla raccolta

Per configurare l'integrazione di Attendance Management Services in Azure AD, è necessario aggiungere Attendance Management Services dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Attendance Management Services dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Attendance Management Services**, selezionare **Attendance Management Services** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Attendance Management Services nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Attendance Management Services usando un utente di test di nome **Britta Simon**.
Per il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Attendance Management Services.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Attendance Management Services, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On per Attendance Management Services](#configure-attendance-management-services-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Attendance Management Services](#create-attendance-management-services-test-user)** : per avere una controparte di Britta Simon in Attendance Management Services collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Attendance Management Services, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione [Attendance Management Services](https://portal.azure.com/) del **portale di Azure** fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Attendance Management Services](common/sp-identifier.png)

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://id.obc.jp/<tenant information >/`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL usando il modello seguente: `https://id.obc.jp/<tenant information >/`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Attendance Management Services](https://www.obcnet.jp/). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Set up Attendance Management Services** (Configura Attendance Management Services) copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-attendance-management-services-single-sign-on"></a>Configura accesso Single Sign-On di Attendance Management Services

1. In un'altra finestra del browser accedere al sito aziendale di Attendance Management Services come amministratore.

1. Fare clic su **SAML authentication** (Autenticazione SAML) nella sezione **Security management** (Gestione sicurezza).

    ![Configurazione di Attendance Management Services](./media/attendancemanagementservices-tutorial/user1.png)

1. Eseguire la procedura seguente:

    ![Configurazione di Attendance Management Services](./media/attendancemanagementservices-tutorial/user2.png)

    a. Selezionare **Use SAML authentication** (Usa autenticazione SAML).

    b. Nella casella di testo **Identifier** (Identificatore) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    c. Nella casella di testo **Authentication endpoint URL** (URL endpoint di autenticazione) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    d. Fare clic su **Select a file** (Seleziona un file) per caricare il certificato scaricato da Azure AD.

    e. Selezionare **Disable password authentication** (Disabilita autenticazione password).

    f. Fare clic su **Registration** (Registrazione).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare `brittasimon@yourcompanydomain.extension`. Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad Attendance Management Services.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Attendance Management Services**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Attendance Management Services**.

    ![Collegamento di Attendance Management Services nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-attendance-management-services-test-user"></a>Creare un utente di test di Attendance Management Services

Per consentire agli utenti di Azure AD di accedere ad Attendance Management Services, è necessario effettuarne il provisioning in Attendance Management Services. Nel caso di Attendance Management Services, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di Attendance Management Services come amministratore.

1. Fare clic su **User management** (Gestione utenti) nella sezione **Security management** (Gestione sicurezza).

    ![Aggiungere un dipendente](./media/attendancemanagementservices-tutorial/user5.png)

1. Fare clic su **New rules login** (Nuovo accesso con regole).

    ![Aggiungere un dipendente](./media/attendancemanagementservices-tutorial/user3.png)

1. Nella sezione **OBCiD information** (Informazioni OBCiD) seguire questa procedura:

    ![Aggiungere un dipendente](./media/attendancemanagementservices-tutorial/user4.png)

    a. Nella casella di testo **OBCiD** digitare l'indirizzo di posta elettronica dell'utente, ad esempio `BrittaSimon\@contoso.com`.

    b. Nella casella di testo **Password** digitare la password dell'utente.

    c. Fare clic su **Registration** (Registrazione).

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Attendance Management Services nel pannello di accesso, si dovrebbe accedere automaticamente agli Attendance Management Services per cui è stato configurato l'accesso Single Sign-On. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)