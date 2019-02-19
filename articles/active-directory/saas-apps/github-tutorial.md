---
title: 'Esercitazione: Integrazione di Azure Active Directory con GitHub | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e GitHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 8761f5ca-c57c-4a7e-bf14-ac0421bd3b5e
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0a648665f607234d585bb2bde8f6adc10ffbfea
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56236398"
---
# <a name="tutorial-azure-active-directory-integration-with-github"></a>Esercitazione: Integrazione di Azure Active Directory con GitHub

Questa esercitazione descrive come integrare GitHub con Azure Active Directory (Azure AD).
L'integrazione di GitHub con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a GitHub.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a GitHub con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con GitHub, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Un'organizzazione GitHub creata in [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise), che richiede il [piano di fatturazione GitHub Enterprise](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* GitHub supporta l'accesso SSO avviato da **SP**

* GitHub supporta il provisioning utenti [**automatico**](github-provisioning-tutorial.md)

## <a name="adding-github-from-the-gallery"></a>Aggiunta di GitHub dalla raccolta

Per configurare l'integrazione di GitHub in Azure AD, è necessario aggiungere GitHub dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere GitHub dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **GitHub**, selezionare **GitHub** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![GitHub nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con GitHub usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in GitHub.

Per configurare e testare l'accesso Single Sign-On di Azure AD con GitHub, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On per GitHub](#configure-github-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di GitHub](#create-github-test-user)**: per avere una controparte di Britta Simon in GitHub collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con GitHub, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione [GitHub](https://portal.azure.com/) del **portale di Azure** selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare l'accesso Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di GitHub](common/sp-identifier.png)

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://github.com/orgs/<entity-id>/sso`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `https://github.com/orgs/<entity-id>`

    > [!NOTE]
    > Si noti che questi non sono i valori reali. È necessario aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. In questo caso, è consigliabile di usare il valore univoco della stringa nell'identificatore. Passare alla sezione Admin di GitHub per recuperare questi valori.

5. L'applicazione GitHub prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. La schermata seguente illustra l'elenco degli attributi predefiniti in cui **nameidentifier** è associato a **user.userprincipalname**. L'applicazione GitHub prevede che **nameidentifier** sia associato a **user.mail**, di conseguenza è necessario modificare il mapping dell'attributo. A questo scopo, fare clic sull'icona **Modifica** e modificare il mapping.

    ![image](common/edit-attribute.png)

6. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/certificatebase64.png)

7. Nella sezione **Configura GitHub** copiare gli URL appropriati in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-github-single-sign-on"></a>Configurare l'accesso Single Sign-On per GitHub

1. In un'altra finestra del Web browser accedere al sito aziendale di GitHub come amministratore.

2. Passare a **Settings** (Impostazioni) e fare clic su **Security** (Sicurezza).

    ![Impostazioni](./media/github-tutorial/tutorial_github_config_github_03.png)

3. Selezionare la casella **Enable SAML authentication** (Abilita autenticazione SAML) mostrando i campi di configurazione dell'accesso Single Sign-On. Usare quindi il valore URL di Single Sign-On per aggiornare l'URL di Single Sign-On nella configurazione di Azure AD.

    ![Impostazioni](./media/github-tutorial/tutorial_github_config_github_13.png)

4. Configurare i campi seguenti:

    ![Impostazioni](./media/github-tutorial/tutorial_github_config_github_051.png)

    a. Nella casella di testo **URL di accesso** incollare il valore di **URL di accesso** copiato dal portale di Azure.

    b. Nella casella di testo **Autorità di certificazione** incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    c. Aprire il certificato scaricato nel Blocco note dal portale di Azure e incollarne il contenuto nella casella di testo **Certificato pubblico**.

    d. Fare clic sull'icona **Modifica** icona per modificare **Metodo di firma** e **Metodo digest** da **RSA-SHA1** e **SHA1**alla **RSA-SHA256** e **SHA256** come illustrato di seguito.

    ![image](./media/github-tutorial/tutorial_github_sha.png)

5. Fare clic su **Test SAML configuration** (Test configurazione SAML) per confermare l'assenza di errori di convalida o errori durante l'accesso SSO.

    ![Impostazioni](./media/github-tutorial/tutorial_github_config_github_06.png)

6. Fare clic su **Save** (Salva).

> [!NOTE]
> L'accesso Single Sign-On in GitHub esegue l'autenticazione in un'organizzazione specifica in GitHub e non sostituisce l'autenticazione di GitHub. Pertanto, se la sessione github.com dell'utente è scaduta, potrebbe essere richiesto di eseguire l'autenticazione con l'ID/la password GitHub durante il processo di accesso Single Sign-On.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD 

Questa sezione descrive come creare un utente di test denominato Britta Simon nel portale di Azure.

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

    d. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a GitHub.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **GitHub**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **GitHub**.

    ![Collegamento di GitHub nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-github-test-user"></a>Creare l'utente di test di GitHub

Questa sezione descrive come creare un utente chiamato Britta Simon in GitHub. GitHub supporta il provisioning utenti automatico, che è abilitato per impostazione predefinita. È possibile scoprire più dettagli [qui](github-provisioning-tutorial.md) su come configurare il provisioning utenti automatico.

**Per creare un utente manualmente, seguire questa procedura:**

1. Accedere al sito aziendale di GitHub come amministratore.

2. Fare clic su **Persone**.

    ![Persone](./media/github-tutorial/tutorial_github_config_github_08.png "Persone")

3. Fare clic su **Invite member** (Invita membro).

    ![Invitare utenti](./media/github-tutorial/tutorial_github_config_github_09.png "Invitare utenti")

4. Nella finestra di dialogo **Invite member** (Invita membro) seguire questa procedura:

    a. Nella casella di testo **Email** (Posta elettronica) digitare l'indirizzo di posta elettronica dell'account di Britta Simon.

    ![Invitare persone](./media/github-tutorial/tutorial_github_config_github_10.png "Invitare persone")

    b. Fare clic su **Send Invitation** (Invia invito).

    ![Invitare persone](./media/github-tutorial/tutorial_github_config_github_11.png "Invitare persone")

    > [!NOTE]
    > Il titolare dell'account Azure Active Directory riceverà un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di GitHub nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione GitHub per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
