---
title: 'Esercitazione: Integrazione di Azure Active Directory con Velpic SAML| Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Velpic SAML.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc8431e2c45cc3bfdfa08dd0078220ab2d290309
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65905393"
---
# <a name="tutorial-azure-active-directory-integration-with-velpic-saml"></a>Esercitazione: Integrazione di Azure Active Directory con Velpic SAML

Questa esercitazione spiega come integrare Velpic SAML con Azure Active Directory (Azure AD).
L'integrazione di Velpic SAML con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Velpic SAML.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Velpic SAML con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Velpic SAML, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Velpic SAML abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Velpic SAML supporta l'accesso SSO avviato da **SP**

## <a name="adding-velpic-saml-from-the-gallery"></a>Aggiungere Velpic SAML dalla raccolta

Per configurare l'integrazione di Velpic SAML in Azure AD, è necessario aggiungere Velpic SAML dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Velpic SAML dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Velpic SAML**, selezionare **Velpic SAML** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Velpic SAML nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Velpic SAML usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Velpic SAML.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Velpic SAML, è necessario completare i seguenti blocchi predefiniti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Velpic SAML](#configure-velpic-saml-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di Velpic SAML](#create-velpic-saml-test-user)**: per avere una controparte di Britta Simon in Velpic SAML collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Velpic SAML, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Velpic SAML** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Velpic SAML](common/sp-identifier.png)

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<sub-domain>.velpicsaml.net`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `https://auth.velpic.com/saml/v2/<entity-id>/login`

    > [!NOTE]
    > Si noti che l'URL di Single Sign-On verrà indicato dal team di Velpic SAML e il valore dell'identificatore sarà disponibile quando si configura il plug-in SSO sul lato Velpic SAML. È necessario copiare tale valore dalla pagina dell'applicazione Velpic SAML e incollarlo qui.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

6. Nella sezione **Configura Velpic SAML** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-velpic-saml-single-sign-on"></a>Configurare l'accesso Single Sign-On di Velpic SAML

1. In un'altra finestra del Web browser accedere al sito aziendale di Velpic SAML come amministratore.

2. Fare clic sulla scheda **Manage** (Gestisci), passare alla sezione **Integration** (Integrazione) e qui scegliere **Plugins** per creare un nuovo plug-in per l'accesso.

    ![Plug-in](./media/velpicsaml-tutorial/velpic_1.png)

3. Fare clic sul pulsante **Add plugin** (Aggiungi plug-in).
    
    ![Plug-in](./media/velpicsaml-tutorial/velpic_2.png)

4. Fare clic sul riquadro **SAML** nella pagina Add plugin (Aggiungi plug-in).
    
    ![Plug-in](./media/velpicsaml-tutorial/velpic_3.png)

5. Immettere il nome del nuovo plug-in SAML e scegliere il pulsante **Add** (Aggiungi).

    ![Plug-in](./media/velpicsaml-tutorial/velpic_4.png)

6. Immettere i dettagli seguenti:

    ![Plug-in](./media/velpicsaml-tutorial/velpic_5.png)

    a. Nella casella di testo **Name** (Nome) digitare il nome del plug-in SAML.

    b. Nella casella di testo **Issuer URL** (URL autorità di certificazione) incollare il valore di **Identificatore Azure AD** copiato dalla finestra **Configura accesso** del portale di Azure.

    c. Nel riquadro della **configurazione dei metadati del provider** caricare il file XML dei metadati scaricato dal portale di Azure.

    d. È anche possibile scegliere di abilitare SAML al momento del provisioning selezionando la casella di controllo **Auto create new users** (Crea automaticamente nuovi utenti). Se un utente non esiste in Velpic e questo flag non è abilitato, l'accesso da Azure avrà esito negativo. Se il flag è abilitato, verrà automaticamente eseguito il provisioning dell'utente in Velpic al momento dell'accesso. 

    e. Copiare l'**URL di Single Sign-On** dalla casella di testo e incollarlo nel portale di Azure.
    
    f. Fare clic su **Save**.

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

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Velpic SAML.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Velpic SAML**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **Velpic SAML**.

    ![Collegamento di Velpic SAML nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-velpic-saml-test-user"></a>Creare l'utente di test di Velpic SAML

Questo passaggio in genere non è necessario poiché l'applicazione supporta il provisioning degli utenti just-in-time. Se il provisioning automatico degli utenti non è abilitato, la creazione manuale dell'utente può essere eseguita come descritto di seguito.

Accedere al sito aziendale di Velpic SAML come amministratore e seguire questa procedura:
    
1. Fare clic sulla scheda Manage (Gestisci) e passare alla sezione Users (Utenti), quindi fare clic sul pulsante New (Nuovo) per aggiungere gli utenti.

    ![Aggiungi utente](./media/velpicsaml-tutorial/velpic_7.png)

2. Nella finestra di dialogo **Create New User** (Crea nuovo utente) effettuare le operazioni seguenti.

    ![user](./media/velpicsaml-tutorial/velpic_8.png)
    
    a. Nella casella di testo **First Name** (Nome) digitare il nome Britta.

    b. Nella casella di testo **Last Name** (Cognome) digitare il cognome Simon.

    c. Digitare il nome utente di Britta Simon nella casella di testo **User Name** (Nome utente).

    d. Nella casella di testo **Email** (Indirizzo di posta elettronica) digitare l'indirizzo di posta elettronica dell'account Brittasimon@contoso.com.

    e. Le informazioni rimanenti sono facoltative, se necessario si possono compilare.
    
    f. Fare clic su **SAVE**.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

1. Quando si fa clic sul riquadro Velpic SAML nel Pannello di accesso, viene visualizzata la pagina di accesso dell'applicazione Velpic SAML, in cui dovrebbe apparire il pulsante **Log In With Azure AD** (Accedi con Azure AD).

    ![Plug-in](./media/velpicsaml-tutorial/velpic_6.png)

1. Fare clic sul pulsante **Log In With Azure AD** (Accedi con Azure AD) per accedere a Velpic usando il proprio account Azure AD.

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

