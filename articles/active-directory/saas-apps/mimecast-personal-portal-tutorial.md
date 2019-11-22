---
title: 'Esercitazione: integrazione di Azure Active Directory con Mimecast Personal Portal | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Mimecast Personal Portal.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 345b22be-d87e-45a4-b4c0-70a67eaf9bfd
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 259635613855e4d7687cf569c94bbd3dd04027fe
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160620"
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-personal-portal"></a>Esercitazione: integrazione di Azure Active Directory con Mimecast Personal Portal

Questa esercitazione descrive come integrare Mimecast Personal Portal con Azure Active Directory (Azure AD).
L'integrazione di Mimecast Personal Portal con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Mimecast Personal Portal.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Mimecast Personal Portal con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Mimecast Personal Portal, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Mimecast Personal Portal abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Mimecast Personal Portal supporta l'accesso SSO avviato da **SP**

## <a name="adding-mimecast-personal-portal-from-the-gallery"></a>Aggiunta di Mimecast Personal Portal dalla raccolta

Per configurare l'integrazione di Mimecast Personal Portal in Azure AD, è necessario aggiungere Mimecast Personal Portal dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Mimecast Personal Portal dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Mimecast Personal Portal**, selezionare **Mimecast Personal Portal** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Mimecast Personal Portal nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Mimecast Personal Portal usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Mimecast Personal Portal.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Mimecast Personal Portal, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Mimecast Personal Portal](#configure-mimecast-personal-portal-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Mimecast Personal Portal](#create-mimecast-personal-portal-test-user)** : per avere una controparte di Britta Simon in Mimecast Personal Portal collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Mimecast Personal Portal, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Mimecast Personal Portal** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni sull'accesso Single Sign-On a URL e dominio di Mimecast Personal Portal](common/sp-identifier-reply.png)

    a. Nella casella di testo **URL di accesso** digitare un URL: 

    | Region  |  Valore | 
    | --------------- | --------------- | 
    | Europa          | `https://eu-api.mimecast.com/login/saml`|
    | Stati Uniti   | `https://us-api.mimecast.com/login/saml`|
    | Sud Africa    | `https://za-api.mimecast.com/login/saml`|
    | Australia       | `https://au-api.mimecast.com/login/saml`|
    | Estero        | `https://jer-api.mimecast.com/login/saml`|

    b. Nella casella di testo **Identificatore** digitare un URL nel formato seguente:

    | Region  |  Valore | 
    | --------------- | --------------- |
    | Europa          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | Stati Uniti   | `https://us-api.mimecast.com/sso/<accountcode>`|    
    | Sud Africa    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | Australia       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | Estero        | `https://jer-api.mimecast.com/sso/<accountcode>`|

    c. Nella casella di testo **URL di risposta** digitare un URL: 

    | Region  |  Valore | 
    | --------------- | --------------- | 
    | Europa          | `https://eu-api.mimecast.com/login/saml`|
    | Stati Uniti   | `https://us-api.mimecast.com/login/saml`|
    | Sud Africa    | `https://za-api.mimecast.com/login/saml`|
    | Australia       | `https://au-api.mimecast.com/login/saml`|
    | Estero        | `https://jer-api.mimecast.com/login/saml`|

    > [!NOTE]
    > Il valore dell'identificatore non è reale. È necessario aggiornare questo valore con l'ID effettivo. Per ottenerlo, contattare il [team di supporto clienti di Mimecast Personal Portal](https://www.mimecast.com/customer-success/technical-support/). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

4. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura Mimecast Personal Portal** copiare gli URL appropriati in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-mimecast-personal-portal-single-sign-on"></a>Configurare l'accesso Single Sign-On per Mimecast Personal Portal

1. In un'altra finestra del Web browser accedere a Mimecast Personal Portal come amministratore.

2. Passare a **Services (Servizi) \> Applications (Applicazioni)** .
   
    ![Applicazioni](./media/mimecast-personal-portal-tutorial/ic794998.png "APPLICAZIONI")

3. Fare clic su **Authentication Profiles**.
   
    ![Profili di autenticazione](./media/mimecast-personal-portal-tutorial/ic794999.png "Authentication Profiles")

4. Fare clic su **New Authentication Profile**.
   
    ![Nuovo profilo di autenticazione](./media/mimecast-personal-portal-tutorial/ic795000.png "Nuovo profilo di autenticazione")

5. Nella sezione **Authentication Profile** , eseguire la procedura seguente:
   
    ![Profilo di autenticazione](./media/mimecast-personal-portal-tutorial/ic795001.png "Authentication Profile")
   
    a. Nella casella di testo **Description** digitare un nome per la configurazione.
   
    b. Selezionare **Enforce SAML Authentication for Mimecast Personal Portal**.
   
    c. Come **Provider** selezionare **Azure Active Directory**.
   
    d. Nella casella di testo **URL autorità di certificazione** incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.
   
    e. Nella casella di testo **URL di accesso** incollare il valore di **URL di accesso** copiato dal portale di Azure.
   
    f. Nella casella di testo **URL disconnessione** incollare il valore di **URL disconnessione** copiato dal portale di Azure.

    g. Aprire nel Blocco note il certificato con codifica **Base 64** scaricato dal portale di Azure, copiarne il contenuto negli Appunti e incollarlo nella casella di testo **Identity Provider Certificate (Metadata)** (Certificato provider di identità (metadati)).

    h. Selezionare **Allow Single Sign On**.
   
    i. Fare clic su **Save**.

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

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Mimecast Personal Portal.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Mimecast Personal Portal**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni digitare e selezionare **Mimecast Personal Portal**.

    ![Collegamento a Mimecast Personal Portal nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-mimecast-personal-portal-test-user"></a>Creare l'utente di test di Mimecast Personal Portal

Per consentire agli utenti di Azure AD di accedere a Mimecast Personal Portal, è necessario eseguirne il provisioning in Mimecast Personal Portal. Nel caso di Mimecast Personal Portal, il provisioning è un’attività manuale.

Per poter creare gli utenti è necessario registrare un dominio.

**Per configurare il provisioning utenti, seguire questa procedura:**

1. Accedere a **Mimecast Personal Portal** come amministratore.

2. Accedere a **Directories \> Internal**.
   
    ![Directories](./media/mimecast-personal-portal-tutorial/ic795003.png "Directory")

3. Fare clic su **Register New Domain**.
   
    ![Registrare un nuovo dominio](./media/mimecast-personal-portal-tutorial/ic795004.png "Register New Domain")

4. Dopo aver creato il nuovo dominio, fare clic su **New Address**.
   
    ![Nuovo indirizzo](./media/mimecast-personal-portal-tutorial/ic795005.png "New Address")

5. Nella finestra di dialogo del nuovo indirizzo eseguire i passaggi seguenti per un account Azure AD valido di cui si vuole eseguire il provisioning:
   
    ![Salva](./media/mimecast-personal-portal-tutorial/ic795006.png "Salva")
   
    a. Nella casella di testo **Email Address** (Indirizzo di posta elettronica) digitare l'**indirizzo di posta elettronica** dell'utente, ad esempio **BrittaSimon\@contoso.com**.
    
    b. Nella casella di testo **Global Name** (Nome globale) digitare il **nome utente** **BrittaSimon**.

    c. Nelle caselle di testo **Password** e **Conferma password** digitare la **password** dell'utente.
   
    b. Fare clic su **Save**.

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente offerta da Mimecast Personal Portal per eseguire il provisioning degli account utente di Azure AD.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Mimecast Personal Portal nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Mimecast Personal Portal per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

