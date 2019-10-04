---
title: 'Esercitazione: Integrazione di Azure Active Directory con InsideView | Microsoft Docs'
description: Questa esercitazione descrive come configurare l'accesso Single Sign-On tra Azure Active Directory e InsideView.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c489a7ab-6b1f-4efb-8a66-8bc13bca78c3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: jeedes
ms.openlocfilehash: 2149b8410104b39652b176895a31b42e094265f5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67100097"
---
# <a name="tutorial-azure-active-directory-integration-with-insideview"></a>Esercitazione: Integrazione di Azure Active Directory con InsideView

Questa esercitazione descrive come integrare InsideView con Azure Active Directory (Azure AD).
Questa integrazione offre i seguenti vantaggi:

* È possibile usare Azure AD per controllare chi può accedere a InsideView.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a InsideView con gli account Azure AD personali.
* È possibile gestire gli account da una posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con InsideView, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Una sottoscrizione di InsideView abilitata per l'accesso Single Sign-On.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione verranno eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* InsideView supporta l'accesso SSO avviato da IDP.

## <a name="add-insideview-from-the-gallery"></a>Aggiungere InsideView dalla raccolta

Per configurare l'integrazione di InsideView in Azure AD, è necessario aggiungere InsideView dalla raccolta all'elenco di app SaaS gestite.

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Azure Active Directory** nel riquadro sinistro:

    ![Selezionare Azure Active Directory](common/select-azuread.png)

2. Passare a **Applicazioni aziendali** > **Tutte le applicazioni**:

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere un'applicazione, selezionare **Nuova applicazione** nella parte superiore della finestra:

    ![Selezionare Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **InsideView**. Selezionare **InsideView** nei risultati della ricerca e quindi selezionare **Aggiungi**.

    ![Search Results](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione verrà configurato e testato l'accesso Single Sign-On di Azure AD con InsideView usando un utente di test di nome Britta Simon.
Per abilitare l'accesso Single Sign-On, è necessario stabilire una relazione tra un utente di Azure AD e l'utente corrispondente in InsideView.

Per configurare e testare l'accesso Single Sign-On di Azure AD con InsideView, è necessario completare questi passaggi:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** per abilitare questa funzionalità per gli utenti.
2. **[Configurare l'accesso Single Sign-On di InsideView](#configure-insideview-single-sign-on)** sul lato dell'applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** per testare l'accesso Single Sign-On di Azure AD.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** per abilitare l'accesso Single Sign-On di Azure AD per tale utente.
5. **[Creare un utente di test di InsideView](#create-an-insideview-test-user)** collegato alla rappresentazione di Azure AD dell'utente.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione verrà abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con InsideView, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione InsideView del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**:

    ![Selezionare Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare l'accesso Single Sign-On:

    ![Selezionare un metodo di accesso Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** selezionare l'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**:

    ![Icona Modifica](common/edit-urls.png)

4. Nella finestra di dialogo **Configurazione SAML di base** seguire questa procedura.

    ![Finestra di dialogo Configurazione SAML di base](common/idp-reply.png)

    Nella casella **URL di risposta** immettere un URL nel formato seguente:

    `https://my.insideview.com/iv/<STS Name>/login.iv`

    > [!NOTE]
    > Questo valore è un segnaposto. È necessario usare l'URL di risposta effettivo. Per ottenere il valore, contattare il [team di supporto di InsideView](mailto:support@insideview.com). È anche possibile fare riferimento ai modelli disponibili nella finestra di dialogo **Configurazione SAML di base** del portale di Azure.

5. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** selezionare il collegamento **Scarica** accanto a **Certificato (base)** , in base alle esigenze, quindi salvare il certificato nel computer:

    ![Collegamento di download del certificato](common/certificateraw.png)

6. Nella sezione **Configura InsideView** copiare gli URL appropriati in base alle esigenze:

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    1. **URL di accesso**.

    1. **Identificatore Azure AD**.

    1. **URL di disconnessione**.

### <a name="configure-insideview-single-sign-on"></a>Configurare l'accesso Single Sign-On di InsideView

1. In un'altra finestra del Web browser accedere al sito aziendale di InsideView come amministratore.

1. Nella parte superiore della finestra selezionare **Admin** (Amministrazione), **SingleSignOn Settings** (Impostazioni Single Sign-On) e quindi **Add SAML** (Aggiungi SAML).
   
   ![Impostazioni di Single Sign-On SAML](./media/insideview-tutorial/ic794135.png "Impostazioni di Single Sign-On SAML")

1. Nella sezione **Add a New SAML** (Aggiungi nuovo SAML) seguire questa procedura.

    ![Sezione Add a New SAML](./media/insideview-tutorial/ic794136.png "Sezione Add a New SAML")

    1. Nella casella di testo **STS Name** (Nome servizio token di sicurezza) digitare un nome per la configurazione.

    1. Nella casella **amlP/WS-Fed Unsolicited EndPoint** (Endpoint non richiesto amIP/WS-Fed) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    1. Aprire il certificato base scaricato dal portale di Azure. Copiare il contenuto del certificato negli Appunti e quindi incollarlo nella casella **STS Certificate** (Certificato servizio token di sicurezza).

    1. Nella casella **Crm User Id Mapping** (Mapping ID utente crm) immettere **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** .

    1. Nella casella **Crm Email Mapping** (Mapping indirizzo posta elettronica crm) immettere **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** .

    1. Nella casella **Crm First Name Mapping** (Mapping nome crm) immettere **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname** .

    1. Nella casella **Crm LastName Mapping** (Mapping cognome crm) immettere **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname** .  

    1. Selezionare **Salva**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

In questa sezione verrà creato un utente di test di nome Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**:

    ![Selezionare Tutti gli utenti](common/users.png)

2. Selezionare **Nuovo utente** in alto nella finestra:

    ![Selezionare Nuovo utente](common/new-user.png)

3. Nella finestra di dialogo **Utente** seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    1. Nella casella **Nome** immettere **BrittaSimon**.
  
    1. Nella casella **Nome utente** immettere **BrittaSimon@\<dominioaziendale>.\<estensione>** , ad esempio BrittaSimon@contoso.com.

    1. Selezionare **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    1. Selezionare **Create** (Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon verrà abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a InsideView.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **InsideView**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **InsideView**.

    ![Elenco delle applicazioni](common/all-applications.png)

3. Nel riquadro sinistro selezionare **Utenti e gruppi**:

    ![Selezionare Utenti e gruppi](common/users-groups-blade.png)

4. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Selezionare Aggiungi utente](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco utenti e quindi fare clic sul pulsante **Seleziona** in basso nella finestra.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco. Fare clic sul pulsante **Seleziona** in basso nella finestra.

7. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Assegna**.

### <a name="create-an-insideview-test-user"></a>Creare l'utente di test di InsideView

Per consentire agli utenti di Azure AD di accedere a InsideView, aggiungerli a InsideView. È necessario aggiungerli manualmente.

Per creare utenti o contatti in InsideView, contattare il [team di supporto di InsideView](mailto:support@insideview.com).

> [!NOTE]
> Per effettuare il provisioning degli account utente di Azure AD, è possibile usare qualsiasi API o strumento di creazione di account utente fornito da InsideView.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

Il passaggio finale consiste nel testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si seleziona il riquadro di InsideView nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di InsideView per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Accedere e usare le app nel portale App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Esercitazioni per l'integrazione di applicazioni SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
