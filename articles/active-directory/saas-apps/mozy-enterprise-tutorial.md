---
title: 'Esercitazione: Integrazione di Azure Active Directory con Mozy Enterprise | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Mozy Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 489b5e62-85c2-45c9-8766-326632d48114
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: a0f21165af0bcbd8bda28f0eae20d3ee837f3be9
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59275658"
---
# <a name="tutorial-azure-active-directory-integration-with-mozy-enterprise"></a>Esercitazione: Integrazione di Azure Active Directory con Mozy Enterprise

Questa esercitazione descrive come integrare Mozy Enterprise con Azure Active Directory (Azure AD).
L'integrazione di Mozy Enterprise con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Mozy Enterprise.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Mozy Enterprise con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Mozy Enterprise, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Mozy Enterprise abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Mozy Enterprise supporta l'accesso SSO avviato da **SP**

## <a name="adding-mozy-enterprise-from-the-gallery"></a>Aggiunta di Mozy Enterprise dalla raccolta

Per configurare l'integrazione di Mozy Enterprise in Azure AD, è necessario aggiungere Mozy Enterprise dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Mozy Enterprise dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Mozy Enterprise** selezionare **Mozy Enterprise** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Mozy Enterprise nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Mozy Enterprise usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Mozy Enterprise.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Mozy Enterprise, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On per Mozy Enterprise](#configure-mozy-enterprise-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Mozy Enterprise](#create-mozy-enterprise-test-user)**: per avere una controparte di Britta Simon in Mozy Enterprise collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Mozy Enterprise, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Mozy Enterprise** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Mozy Enterprise](common/sp-signonurl.png)

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<tenantname>.Mozyenterprise.com`

    > [!NOTE]
    > Poiché non è reale, è necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere il valore, contattare il [team di supporto clienti di Mozy Enterprise Client](http://support.mozy.com/). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura Mozy Enterprise** copiare gli URL appropriati in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-mozy-enterprise-single-sign-on"></a>Configurare l'accesso Single Sign-On per Mozy Enterprise

1. In un'altra finestra del Web browser accedere al sito aziendale di Mozy Enterprise come amministratore.

2. Nella sezione **Configuration** fare clic su **Authentication Policy**.
   
    ![Criteri di autenticazione](./media/mozy-enterprise-tutorial/ic777314.png "Criteri di autenticazione")

3. Nella sezione **Authentication Policy** seguire questa procedura:
   
    ![Criteri di autenticazione](./media/mozy-enterprise-tutorial/ic777315.png "Criteri di autenticazione")
   
    a. Selezionare **Directory Service** come **Provider**.
   
    b. Selezionare **Use LDAP Push**.
   
    c. Fare clic sulla scheda **SAML Authentication** .
   
    d. Incollare il valore di **URL di accesso**, copiato dal portale di Azure, nella casella di testo **Authentication URL** (URL di autenticazione).
   
    e. Incollare il valore di **Identificatore Azure AD**, copiato dal portale di Azure, nella casella di testo **SAML Endpoint** (Endpoint SAML).
   
    f. Aprire il certificato con codifica Base 64 scaricato nel Blocco note, copiarne il contenuto negli Appunti e incollare l'intero certificato nella casella di testo **SAML Certificate** (Certificato SAML).
   
    g. Selezionare **Abilita SSO per consentire agli amministratori di accedere con le proprie credenziali di rete**.
   
    h. Fare clic su **Salva modifiche**.

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

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Mozy Enterprise.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Mozy Enterprise**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Mozy Enterprise**.

    ![Collegamento di Mozy Enterprise nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-mozy-enterprise-test-user"></a>Creare un utente di test di Mozy Enterprise

Per consentire agli utenti di Azure AD di accedere a Mozy Enterprise, è necessario eseguirne il provisioning in Mozy Enterprise. Nel caso di Mozy Enterprise, il provisioning è un’attività manuale.

>[!NOTE]
>È possibile utilizzare qualsiasi altro strumento di creazione di account utente di Mozy Enterprise o le API fornite da Mozy Enterprise per eseguire il provisioning degli account utente di AAD.

**Per effettuare il provisioning di un account utente, seguire questa procedura:**

1. Accedere al tenant **Mozy Enterprise** .

2. Fare clic su **Users** (Utenti) e quindi fare clic su **Add New User** (Aggiungi nuovo utente).
   
    ![Utenti](./media/mozy-enterprise-tutorial/ic777317.png "Utenti")
   
    >[!NOTE]
    >L'opzione **Add New User** (Aggiungi nuovo utente) viene visualizzata solo se **Mozy** è selezionato come provider in **Authentication policy** (Criteri di autenticazione). Se è configurata l'autenticazione SAML gli utenti vengono aggiunti automaticamente al primo accesso tramite Single Sign-On.
    
3. Nella finestra di dialogo Nuovo utente eseguire la procedura seguente:
   
    ![Aggiungere utenti](./media/mozy-enterprise-tutorial/ic777318.png "Aggiungere utenti")
   
    a. Dall’elenco **Choose a group** selezionare un gruppo.
   
    b. Dall’elenco **What tupe of user** selezionare un tipo.
   
    c. Nella casella di testo **Nome utente** digitare il nome dell'utente di Azure AD.
   
    d. Nella casella di testo **Email** digitare l'indirizzo di posta elettronica dell'utente di Azure AD.
   
    e. Selezionare **Send user instruction email**.
   
    f. Fare clic su **Add User(s)**.

     >[!NOTE]
     > Dopo aver creato l'utente, verrà inviato un messaggio di posta elettronica all'utente di Azure AD con un collegamento da selezionare per confermare l'account e attivarlo.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Mozy Enterprise nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Mozy Enterprise per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

