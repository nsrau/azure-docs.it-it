---
title: 'Esercitazione: Integrazione di Azure Active Directory con 8x8 Virtual Office | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e 8x8 Virtual Office.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: b34a6edf-e745-4aec-b0b2-7337473d64c5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9385ec6a86c24e619ffafdae67bc66f66e099f3b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57842737"
---
# <a name="tutorial-azure-active-directory-integration-with-8x8-virtual-office"></a>Esercitazione: Integrazione di Azure Active Directory con 8x8 Virtual Office

Questa esercitazione descrive come integrare 8x8 Virtual Office con Azure Active Directory (Azure AD).
L'integrazione di 8x8 Virtual Office con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a 8x8 Virtual Office.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a 8x8 Virtual Office con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con 8x8 Virtual Office, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di 8x8 Virtual Office abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.


* 8x8 Virtual Office supporta l'accesso SSO avviato da **IDP**

* 8x8 Virtual Office supporta il provisioning utenti **JIT**

## <a name="adding-8x8-virtual-office-from-the-gallery"></a>Aggiunta di 8x8 Virtual Office dalla raccolta

Per configurare l'integrazione di 8x8 Virtual Office in Azure AD, è necessario aggiungere 8x8 Virtual Office dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere 8x8 Virtual Office dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **8x8 Virtual Office**, selezionare **8x8 Virtual Office** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![8x8 Virtual Office nell'elenco dei risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con 8x8 Virtual Office usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in 8x8 Virtual Office.

Per configurare e testare l'accesso Single Sign-On di Azure AD con 8x8 Virtual Office è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di 8x8 Virtual Office](#configure-8x8-virtual-office-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di 8x8 Virtual Office](#create-8x8-virtual-office-test-user)**: per avere una controparte di Britta Simon in 8x8 Virtual Office collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con 8x8 Virtual Office, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **8x8 Virtual Office** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella finestra di dialogo **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni sull'accesso Single Sign-On per URL e dominio di 8x8 Virtual Office](common/idp-intiated.png)

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://sso.8x8.com/saml2`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://sso.8x8.com/saml2`

4. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **Certificato (base)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/certificateraw.png)

6. Nella sezione **Configura 8x8 Virtual Office** copiare gli URL appropriati in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-8x8-virtual-office-single-sign-on"></a>Configurare l'accesso Single Sign-On per 8x8 Virtual Office

1. Accedere al tenant di 8x8 Virtual Office come amministratore.

1. Selezionare **Virtual Office Account Mgr** (Account manager di Virtual Office) nel pannello dell'applicazione.

    ![Configurazione sul lato app](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)

1. Selezionare l'account **Business** (Aziendale) da gestire e fare clic sul pulsante **Sign In** (Accedi).

    ![Configurazione sul lato app](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)

1. Fare clic sulla scheda **ACCOUNTS** (ACCOUNT) nell'elenco di menu.

   ![Configurazione sul lato app](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)

1. Fare clic su **Single Sign On** nell'elenco di account.
  
   ![Configurazione sul lato app](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)

1. Selezionare **Single Sign-On** sotto Metodi di autenticazione e fare clic su **SAML**.

   ![Configurazione sul lato app](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)

1. Nella sezione **SAML Single Sign-On** seguire questa procedura:

   ![Configurazione sul lato app](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)

   a. Nella casella di testo **URL accesso** incollare il valore di **URL di accesso** copiato dal portale di Azure.

   b. Nella casella di testo **Sign Out URL** (URL disconnessione) incollare il valore di **URL disconnessione** copiato dal portale di Azure.

   c. Nella casella di testo **URL autorità di certificazione** incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

   d. Fare clic sul pulsante **Sfoglia** per caricare il certificato scaricato dal portale di Azure.

   e. Fare clic sul pulsante **Salva** .

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

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a 8x8 Virtual Office.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **8x8 Virtual Office**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni digitare e selezionare **8x8 Virtual Office**.

    ![Collegamento di 8x8 Virtual Office nell'elenco Applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-8x8-virtual-office-test-user"></a>Creare l'utente di test di 8x8 Virtual Office

In questa sezione viene creato un utente di nome Britta Simon in 8x8 Virtual Office. 8x8 Virtual Office supporta il **provisioning utenti JIT**, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in 8x8 Virtual Office, ne viene creato uno nuovo dopo l'autenticazione.

> [!NOTE]
> Per creare un utente manualmente è necessario contattare il [team di supporto di 8x8 Virtual Office](https://www.8x8.com/about-us/contact-us).

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di 8x8 Virtual Office nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione 8x8 Virtual Office per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

