---
title: 'Esercitazione: Integrazione di Azure Active Directory con Clarizen | Microsoft Docs'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e Clarizen.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 3908c354a8551d7b16c759a48ef3e5316bfa11fa
ms.openlocfilehash: 574c6877bddac8be7d6d541bfabbdc10f6be3101
ms.lasthandoff: 02/21/2017


---
# <a name="tutorial-azure-active-directory-integration-with-clarizen"></a>Esercitazione: Integrazione di Azure Active Directory con Clarizen

Questa esercitazione descrive come integrare Azure Active Directory (Azure AD) con Clarizen. Questa integrazione offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Clarizen.
- È possibile abilitare gli utenti per l'accesso automatico a Clarizen (Single Sign-On) con gli account Azure AD.
- È possibile gestire gli account da una posizione centrale, il portale di Azure.

Lo scenario in questa esercitazione prevede due attività principali:

1. Aggiungere Clarizen dalla raccolta.
2. Configurare e testare l'accesso Single Sign-On di Azure AD.

Per altre informazioni sull'integrazione di app SaaS (Software as a Service) con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD con Clarizen, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Una sottoscrizione di Clarizen abilitata per Single Sign-On

A questo scopo, seguire queste indicazioni:

- Testare l'accesso Single Sign-On di Azure AD in un ambiente di test. Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non è disponibile un ambiente di test di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="add-clarizen-from-the-gallery"></a>Aggiungere Clarizen dalla raccolta
Per configurare l'integrazione di Clarizen in Azure AD, aggiungere Clarizen dalla raccolta all'elenco di app SaaS gestite.

1. Nel [portale di Azure](https://portal.azure.com) fare clic sull'icona **Azure Active Directory** nel riquadro sinistro.

    ![Icona Azure Active Directory][1]

2. Fare clic su **Applicazioni aziendali**. Fare quindi clic su **Tutte le applicazioni**.

    ![Clic su "Applicazioni aziendali" e su "Tutte le applicazioni"][2]

3. Fare clic sul pulsante **Aggiungi** nella parte inferiore della finestra di dialogo.

    ![Pulsante "Aggiungi"][3]

4. Nella casella di ricerca digitare **Clarizen**.

    ![Digitare "Clarizen" nella casella di ricerca](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_000.png)

5. Nel riquadro dei risultati selezionare **Clarizen** e quindi fare clic su **Aggiungi** per aggiungere l'applicazione.

    ![Selezione di Clarizen nel riquadro dei risultati](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_0001.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD
Nelle sezioni seguenti viene configurato e testato l'accesso Single Sign-On di Azure AD con Clarizen con un utente di test di nome Britta Simon.

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Clarizen che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Clarizen. La relazione di collegamento viene stabilita assegnando al valore **nome utente** in Azure AD lo stesso valore di **Username** (Nome utente) in Clarizen.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Clarizen, completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente test di Azure AD](#create-an-azure-ad-test-user)** per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di Clarizen](#create-a-clarizen-test-user)** per avere una controparte di Britta Simon in Clarizen collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)** per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD
Abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure e configurare l'accesso Single Sign-On nell'applicazione Clarizen.

1. Nella pagina di integrazione dell'applicazione **Clarizen** del portale di Azure fare clic su **Single Sign-On**.

    ![Clic su "Single Sign-On"][4]

2. Nella finestra di dialogo **Single Sign-On** per **Modalità** selezionare **Accesso basato su SAML** per abilitare l'accesso Single Sign-On.

    ![Selezione di "Accesso basato su SAML"](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_01.png)

3. Nella sezione **URL e dominio Clarizen** seguire questa procedura:

    ![Caselle per identificatore e URL di risposta](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_02.png)

    a. Nella casella **Identificatore** digitare il valore **Clarizen**

    b. Nella casella **URL di risposta** digitare un URL usando il modello seguente: **https://<company name>.clarizen.com/Clarizen/Pages/Integrations/SAML/SamlResponse.aspx**

    > [!NOTE]
    > Questi non sono i valori reali. È necessario usare l'identificatore e l'URL di risposta effettivi. In questo caso è consigliabile usare come identificatore il valore univoco di una stringa. Per ottenere i valori effettivi, contattare il [team di supporto Clarizen](https://success.clarizen.com/hc/en-us/requests/new).

4. Nella sezione **Certificato di firma SAML** fare clic su **Crea nuovo certificato**.

    ![Clic su "Crea nuovo certificato"](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_03.png)     

5. Nella finestra di dialogo **Crea nuovo certificato** fare clic sull'icona del calendario e selezionare una data di scadenza. Fare quindi clic su **Salva**.

    ![Selezione e salvataggio di una data di scadenza](./media/active-directory-saas-clarizen-tutorial/tutorial_general_300.png)

6. Nella sezione **Certificato di firma SAML** selezionare **Rendi attivo il certificato nuovo** e quindi fare clic su **Salva**.

    ![Selezione della casella di controllo per rendere attivo il certificato nuovo](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_04.png)

7. Nella finestra di dialogo **Certificato di rollover** fare clic su **OK**.

    ![Clic su "OK" per confermare che si vuole rendere attivo il certificato](./media/active-directory-saas-clarizen-tutorial/tutorial_general_400.png)

8. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Clic su "Certificato (Base64)" per avviare il download](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_05.png)

9. Nella sezione **Configurazione di Clarizen** fare clic su **Configura Clarizen** per aprire la finestra **Configura accesso**.

    ![Clic su "Configure Clarizen"](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_06.png)

    ![Finestra "Configura accesso", con i file e gli URL](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_07.png)

10. In un'altra finestra del Web browser accedere al sito aziendale di Clarizen come amministratore.

11. Fare clic sul nome utente e quindi su **Settings** (Impostazioni).

    ![Clic su "Settings" (Impostazioni) sotto il nome utente](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_001.png "Settings (Impostazioni)")

12. Fare clic sulla scheda **Global Settings** (Impostazioni globali), quindi accanto a **Federated Authentication** (Autenticazione federata) fare clic su **edit** (modifica).

    ![Scheda "Global Settings" (Impostazioni globali)](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_002.png "Global Settings (Impostazioni globali)")

13. Nella finestra di dialogo **Federated Authentication** (Autenticazione federata) seguire questa procedura:

    ![Finestra di dialogo "Federated Authentication"(Autenticazione federata)](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_003.png "Federated Authentication (Autenticazione federata)")

    a. Selezionare **Enable Federated Authentication** (Abilita autenticazione federata).

    b. Per caricare il certificato scaricato, fare clic su **Carica** .

    c. Nella casella di testo **Sign-in URL** (URL di accesso) immettere il valore di **SAML Single Sign-On Service URL** (URL servizio Single Sign-On SAML) dalla finestra di configurazione dell'applicazione di Azure AD.

    d. Nella casella **Sign-out URL** (URL di disconnessione) immettere il valore di **Sign-Out URL** (URL di disconnessione) dalla finestra di configurazione dell'applicazione di Azure AD.

    e. Selezionare **Utilizza POST**.

    f. Fare clic su **Save**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD
Nel portale di Azure creare un utente di test chiamato Britta Simon.

![Nome e indirizzo di posta elettronica dell'utente di test di Azure AD][100]

1. Nel portale di Azure fare clic sull'icona **Azure Active Directory** nel riquadro sinistro.

    ![Icona Azure Active Directory](./media/active-directory-saas-clarizen-tutorial/create_aaduser_01.png)

2. Fare clic su **Utenti e gruppi** e quindi su **Tutti gli utenti** per visualizzare l'elenco di utenti.

    ![Clic su "Utenti e gruppi" e su "Tutti gli utenti"](./media/active-directory-saas-clarizen-tutorial/create_aaduser_02.png)

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.

    ![Pulsante "Aggiungi"](./media/active-directory-saas-clarizen-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo "Utente" con nome, indirizzo di posta elettronica e password inseriti](./media/active-directory-saas-clarizen-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'account di Britta Simon.

    c. Selezionare **Mostra password** e prendere nota del valore di **Password**.

    d. Fare clic su **Crea**.

### <a name="create-a-clarizen-test-user"></a>Creare un utente di test di Clarizen
Per consentire agli utenti di Azure AD di accedere a Clarizen, è necessario effettuare il provisioning degli account utente. Nel caso di Clarizen, il provisioning è un'attività manuale.

1. Accedere al sito aziendale di Clarizen come amministratore.

2. Fare clic su **Persone**.

    ![Clic su "People" (Persone)](./media/active-directory-saas-clarizen-tutorial/create_aaduser_001.png "People (Persone)")

3. Fare clic su **Invite User**.

    ![Pulsante "Invite User" (Invita l'utente)](./media/active-directory-saas-clarizen-tutorial/create_aaduser_002.png "Invitare utenti")

4. Nella finestra di dialogo **Invite People** (Invita persone) seguire questa procedura:

    ![Finestra di dialogo "Invite People" (Invita persone)](./media/active-directory-saas-clarizen-tutorial/create_aaduser_003.png "Invite People (Invita persone)")

    a. Nella casella **Email** (Posta elettronica) digitare l'indirizzo di posta elettronica dell'account di Britta Simon.

    b. Fare clic su **Invita**.

    > [!NOTE]
    > Il titolare dell'account Azure Active Directory riceverà un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD
Consentire a Britta Simon di usare l'accesso Single Sign-On di Azure concedendole l'accesso a Clarizen.

![Utente di test assegnato][200]

1. Nel portale di Azure aprire la visualizzazione applicazioni, passare alla visualizzazione directory, fare clic su **Applicazioni aziendali** e quindi su **Tutte le applicazioni**.

    ![Clic su "Applicazioni aziendali" e su "Tutte le applicazioni"][201]

2. Nell'elenco delle applicazioni, selezionare **Clarizen**.

    ![Selezione di Clarizen nell'elenco](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_50.png)

3. Scegliere **Utenti e gruppi** nel riquadro a sinistra.

    ![Clic su "Utenti e gruppi"][202]

4. Fare clic su **Add** . Nella finestra di dialogo **Aggiungi assegnazione** selezionare quindi **Utenti e gruppi**.

    ![Pulsante "Aggiungi" e finestra di dialogo "Aggiungi assegnazione"][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco di utenti.

6. Nella finestra di dialogo **Utenti e gruppi** fare clic sul pulsante **Seleziona**.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On
Testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Clarizen nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Clarizen.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_203.png

