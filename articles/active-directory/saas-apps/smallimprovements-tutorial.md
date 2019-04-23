---
title: 'Esercitazione: Integrazione di Azure Active Directory con Small Improvements | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Small Improvements.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 59c8a112-41e1-4337-9ef3-3d7029780d61
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 19d9624c5bb60f47ef4bfa1b0629327780c2a9c7
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59266303"
---
# <a name="tutorial-azure-active-directory-integration-with-small-improvements"></a>Esercitazione: Integrazione di Azure Active Directory con Small Improvements

Questa esercitazione descrive come integrare Small Improvements Learn con Azure Active Directory (Azure AD).
L'integrazione di Small Improvements con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Small Improvements.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Small Improvements con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Small Improvements, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Small Improvements abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Small Improvements supporta l'accesso SSO avviato da **SP**

## <a name="adding-small-improvements-from-the-gallery"></a>Aggiunta di Small Improvements dalla raccolta

Per configurare l'integrazione di Small Improvements in Azure AD, è necessario aggiungere Small Improvements dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Small Improvements dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Small Improvements**, selezionare **Small Improvements** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Small Improvements nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Small Improvements usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Small Improvements.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Small Improvements, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Small Improvements](#configure-small-improvements-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di Small Improvements](#create-small-improvements-test-user)**: per avere una controparte di Britta Simon in Small Improvements collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Small Improvements, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Small Improvements** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Small Improvements](common/sp-identifier.png)

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<subdomain>.small-improvements.com`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL usando il modello seguente: `https://<subdomain>.small-improvements.com`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto client di Small Improvements](mailto:support@small-improvements.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura Small Improvements** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-small-improvements-single-sign-on"></a>Configurare l'accesso Single Sign-On di Small Improvements

1. In un'altra finestra del browser accedere al sito aziendale di Small Improvements come amministratore.

1. Dalla pagina dashboard principale fare clic sul pulsante **Amministrazione** a sinistra.

    ![Configure Single Sign-On](./media/smallimprovements-tutorial/tutorial_smallimprovements_06.png) 

1. Fare clic sul pulsante **SSO SAML** nella sezione **Integrazioni**.

    ![Configure Single Sign-On](./media/smallimprovements-tutorial/tutorial_smallimprovements_07.png) 

1. Nella pagina di configurazione dell’accesso Single Sign-On, seguire questa procedura:

    ![Configure Single Sign-On](./media/smallimprovements-tutorial/tutorial_smallimprovements_08.png)  

    a. Nella casella di testo **HTTP Endpoint** (Endpoint HTTP) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    b. Aprire il certificato scaricato nel Blocco note, copiarne il contenuto e incollarlo nella casella di testo **Certificato x509** . 

    c. Se si vuole che l'opzione di autenticazione al modulo di accesso e accesso Single Sign-On sia disponibile per gli utenti, selezionare l'opzione **Abilitare l'accesso anche tramite account di accesso e password** .  

    d. Immettere il valore appropriato per il nome del pulsante di accesso Single Sign-On nella casella di testo **Richiesta SAML**  

    e. Fare clic su **Save**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

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

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a Small Improvements.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Small Improvements**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Selezionare **Small Improvements**dall'elenco di applicazioni.

    ![Collegamento di Small Improvements nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-small-improvements-test-user"></a>Creare l'utente di test di Small Improvements

Per consentire agli utenti di Azure AD di accedere a Small Improvements, è necessario effettuarne il provisioning in Small Improvements. Nel caso di Small Improvements, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di Small Improvements come amministratore.

1. Dalla home page passare al menu sulla sinistra e fare clic su **Amministrazione**.

1. Fare clic sul pulsante **Directory utenti** nella sezione Gestione utenti.

    ![Creazione di un utente test di Azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_10.png) 

1. Fare clic su **Aggiungi utenti**.

    ![Creazione di un utente test di Azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_11.png) 

1. Nella finestra di dialogo **Aggiungi utenti** seguire questa procedura: 

    ![Creazione di un utente test di Azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_12.png)

    a. Immettere il **nome** dell'utente, ad esempio **Britta**.

    b. Immettere il **cognome** dell'utente, ad esempio **Simon**.

    c. Immettere l'**indirizzo di posta elettronica** dell'utente ad esempio **brittasimon@contoso.com**.

    d. È inoltre possibile scegliere di immettere il messaggio personale nella casella di testo **Invia messaggio di notifica** . Se non si vuole inviare la notifica, deselezionare questa casella di controllo.

    e. Fare clic su **Crea utenti**.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Small Improvements nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Small Improvements per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)