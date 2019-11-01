---
title: 'Esercitazione: Integrazione di Azure Active Directory con ClickUp Productivity Platform | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e ClickUp Productivity Platform.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 06853edd-e8da-4ad2-a4e6-5555d592cee5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: 4647d78255f611959bb86ca00ff6920bc823d49a
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73158559"
---
# <a name="tutorial-azure-active-directory-integration-with-clickup-productivity-platform"></a>Esercitazione: Integrazione di Azure Active Directory con ClickUp Productivity Platform

Questa esercitazione descrive come integrare ClickUp Productivity Platform con Azure Active Directory (Azure AD).
L'integrazione di ClickUp Productivity Platform con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a ClickUp Productivity Platform.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a ClickUp Productivity Platform con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con ClickUp Productivity Platform, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di ClickUp Productivity Platform abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* ClickUp Productivity Platform supporta l'accesso SSO avviato da **SP**

## <a name="adding-clickup-productivity-platform-from-the-gallery"></a>Aggiunta di ClickUp Productivity Platform dalla raccolta

Per configurare l'integrazione di ClickUp Productivity Platform in Azure AD, è necessario aggiungere ClickUp Productivity Platform dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere ClickUp Productivity Platform dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **ClickUp Productivity Platform**, selezionare **ClickUp Productivity Platform** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![ClickUp Productivity Platform nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con ClickUp Productivity Platform usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in ClickUp Productivity Platform.

Per configurare e testare l'accesso Single Sign-On di Azure AD con ClickUp Productivity Platform, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On per ClickUp Productivity Platform](#configure-clickup-productivity-platform-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di ClickUp Productivity Platform](#create-clickup-productivity-platform-test-user)** : per avere una controparte di Britta Simon in ClickUp Productivity Platform collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con ClickUp Productivity Platform, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **ClickUp Productivity Platform** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di ClickUp Productivity Platform](common/sp-identifier.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL `https://app.clickup.com/login/sso`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `https://api.clickup.com/v1/team/<team_id>/microsoft`

    > [!NOTE]
    > Il valore dell'identificatore non è reale. Aggiornare questo valore con l'identificatore effettivo, come illustrato più avanti nell'esercitazione.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e salvarlo nel computer.

    ![Collegamento di download del certificato](common/copy-metadataurl.png)

### <a name="configure-clickup-productivity-platform-single-sign-on"></a>Configurare l'accesso Single Sign-On per ClickUp Productivity Platform

1. In un'altra finestra del Web browser accedere al tenant ClickUp Productivity Platform come amministratore.

2. Fare clic su **User profile** (Profilo utente) e selezionare **Settings** (Impostazioni).

    ![Configurazione di ClickUp Productivity](./media/clickup-productivity-platform-tutorial/configure1.png)

3. In Single Sign-On (SSO) Provider (Provider SSO) selezionare **Microsoft**.

    ![Configurazione di ClickUp Productivity](./media/clickup-productivity-platform-tutorial/configure2.png)

4. Nella pagina **Configure Microsoft Single Sign On** (Configura Single Sign-On Microsoft) seguire questa procedura:

    ![Configurazione di ClickUp Productivity](./media/clickup-productivity-platform-tutorial/configure3.png)

    a. Fare clic su **Copy** (Copia) per copiare il valore dell'ID entità e incollarlo nella casella di testo **Identificatore (ID entità)** della sezione **Configurazione SAML di base** del portale di Azure.
    
    b. Nella casella di testo **Azure Federation Metadata URL** (URL dei metadati di federazione di Azure) incollare il valore dell'URL dei metadati di federazione dell'app copiato dal portale di Azure e quindi fare clic su **Save** (Salva).

5. Per completare la configurazione, fare clic su **Authenticate With Microsoft to complete setup** (Esegui l'autenticazione con Microsoft per completare la configurazione) ed eseguire l'autenticazione con l'account Microsoft.

    ![Configurazione di ClickUp Productivity](./media/clickup-productivity-platform-tutorial/configure4.png)

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

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a ClickUp Productivity Platform.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **ClickUp Productivity Platform**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **ClickUp Productivity Platform**.

    ![Collegamento di ClickUp Productivity Platform nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-clickup-productivity-platform-test-user"></a>Creare l'utente di test di ClickUp Productivity Platform

1. In un'altra finestra del Web browser accedere al tenant ClickUp Productivity Platform come amministratore.

2. Fare clic su **User profile** (Profilo utente) e selezionare **Users** (Utenti).

    ![Configurazione di ClickUp Productivity](./media/clickup-productivity-platform-tutorial/user1.png)

3. Immettere l'indirizzo di posta elettronica dell'utente nella casella di testo e fare clic su **Invite** (Invita).

    ![Configurazione di ClickUp Productivity](./media/clickup-productivity-platform-tutorial/user2.png)

    > [!NOTE]
    > L'utente riceverà la notifica e dovrà accettare l'invito per attivare l'account.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di ClickUp Productivity Platform nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione ClickUp Productivity Platform per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

