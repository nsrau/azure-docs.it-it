---
title: 'Esercitazione: Integrazione di Azure Active Directory con New Relic | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e New Relic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3186b9a8-f4d8-45e2-ad82-6275f95e7aa6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: jeedes
ms.openlocfilehash: dfa5fbcf5df8ee314aab3e4a8228d81e7e14565b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233525"
---
# <a name="tutorial-azure-active-directory-integration-with-new-relic"></a>Esercitazione: Integrazione di Azure Active Directory con New Relic

Questa esercitazione descrive come integrare New Relic con Azure Active Directory (Azure AD).
L'integrazione di New Relic con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a New Relic.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a New Relic con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con New Relic, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di New Relic abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* New Relic supporta l'accesso SSO avviato da **SP**

## <a name="adding-new-relic-from-the-gallery"></a>Aggiunta di New Relic dalla raccolta

Per configurare l'integrazione di New Relic in Azure AD, è necessario aggiungere New Relic dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere New Relic dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **New Relic**, nel pannello dei risultati selezionare **New Relic** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![New Relic nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con New Relic usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in New Relic.

Per configurare e testare l'accesso Single Sign-On di Azure AD con New Relic, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On per New Relic](#configure-new-relic-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di New Relic](#create-new-relic-test-user)** : per avere una controparte di Britta Simon in New Relic collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con New Relic, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **New Relic** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di New Relic](common/sp-identifier.png)

    a. Nella casella di testo **URL di accesso** digitare un URL usando il modello seguente: `https://rpm.newrelic.com/accounts/{acc_id}/sso/saml/login`. Assicurarsi di sostituire l'ID account New Relic.

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL: `rpm.newrelic.com`

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura New Relic** copiare gli URL appropriati in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-new-relic-single-sign-on"></a>Configurare l'accesso Single Sign-On per New Relic

1. In un'altra finestra del Web browser accedere al sito aziendale di **New Relic** come amministratore.

2. Nel menu in alto fare clic su **Impostazioni account**.
   
    ![Impostazioni dell'account](./media/new-relic-tutorial/ic797036.png "Impostazioni account")

3. Fare clic sulla scheda **Security and authentication** (Sicurezza e autenticazione), quindi fare clic sulla scheda **Single sign on**.
   
    ![Single Sign-On](./media/new-relic-tutorial/ic797037.png "Single Sign-On")

4. Nella pagina della finestra di dialogo SAML eseguire la procedura seguente:
   
    ![SAML](./media/new-relic-tutorial/ic797038.png "SAML")
   
    a. Fare clic su **Choose File** per caricare il certificato di Azure Active Directory scaricato.

    b. Nella casella di testo**Remote login URL** (URL accesso remoto) incollare il valore di **URL di accesso** copiato dal portale di Azure.
   
    c. Nella casella di testo **Logout landing URL** (URL di destinazione di disconnessione) incollare il valore dell'**URL di disconnessione** copiato dal portale di Azure.

    d. Fare clic su **Save my changes**.

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

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a New Relic.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **New Relic**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **New Relic**.

    ![Collegamento di New Relic nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-new-relic-test-user"></a>Creare l'utente di test di New Relic

Per consentire agli utenti di Azure Active Directory di accedere a New Relic, è necessario eseguirne il provisioning in New Relic. Nel caso di New Relic, il provisioning è un’attività manuale.

**Per effettuare il provisioning di un account utente in New Relic, seguire questa procedura:**

1. Accedere al sito aziendale di **New Relic** come amministratore.

2. Nel menu in alto fare clic su **Impostazioni account**.
   
    ![Impostazioni dell'account](./media/new-relic-tutorial/ic797040.png "Impostazioni account")

3. Nel riquadro **Account** a sinistra fare clic su **Summary** (Riepilogo), quindi su **Add user** (Aggiungi utente).
   
    ![Impostazioni dell'account](./media/new-relic-tutorial/ic797041.png "Impostazioni account")

4. Nella finestra di dialogo **Active users** eseguire la procedura seguente:
   
    ![Utenti attivi](./media/new-relic-tutorial/ic797042.png "Active users")
   
    a. Nella casella di testo **Posta elettronica** digitare l'indirizzo di posta elettronica di un utente valido di Azure Active Directory di cui si desidera eseguire il provisioning.

    b. Come **Role** (Ruolo) selezionare **User** (Utente).

    c. Fare clic su **Add this user**.

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente fornito da New Relic per effettuare il provisioning degli account utente Azure AD.
> 

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si seleziona il riquadro di New Relic nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione New Relic per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

