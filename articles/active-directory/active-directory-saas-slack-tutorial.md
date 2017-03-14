---
title: 'Esercitazione: Integrazione di Azure Active Directory con Slack | Microsoft Docs'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e Slack.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: ffc5e73f-6c38-4bbb-876a-a7dd269d4e1c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 9c027decf5d982519bc9f41aa9588fa431ef3975
ms.openlocfilehash: 17ad8938cbee539e74f87210077a12c72e777edc
ms.lasthandoff: 02/24/2017


---
# <a name="tutorial-azure-ad-integration-with-slack"></a>Esercitazione: Integrazione di Azure AD con Slack

Questa esercitazione descrive come integrare Slack con Azure Active Directory (Azure AD).

Integrando Slack con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Slack.
* Abilitare gli utenti per l'accesso automatico con Single Sign-On (SSO) a Slack con i propri account Azure AD.
* Gestire gli account in una posizione centrale, il portale di Azure.

Per informazioni sull'integrazione di app SaaS (Software as a Service) con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Slack, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure AD.
* Sottoscrizione di Slack abilitata per l'accesso SSO

> [!NOTE]
> Non è consigliabile testare i passaggi di questa esercitazione in un ambiente di produzione.

A questo scopo, seguire queste indicazioni:

* Usare l'ambiente di produzione solo se necessario.
* Se non è disponibile un ambiente di prova di Azure AD, ottenere una [versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene testato l'accesso SSO di Azure AD in un ambiente di testing. Lo scenario da seguire prevede due blocchi predefiniti principali:

* Aggiunta di Slack dalla raccolta
* Configurazione e test dell'accesso SSO di Azure AD

## <a name="add-slack-from-the-gallery"></a>Aggiungere Slack dalla raccolta
Per configurare l'integrazione di Slack con Azure AD, aggiungere Slack dalla raccolta al proprio elenco di app SaaS gestite seguendo questa procedura:

1. Aprire il [portale di Azure](https://portal.azure.com).
2. Nel riquadro sinistro fare clic sul pulsante **Azure Active Directory**.

    ![Pulsante "Azure Active Directory"][1]

3. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pulsante "Tutte le applicazioni" nel pannello "Applicazioni aziendali"][2]

4. Nella parte superiore della finestra di dialogo **Tutte le applicazioni** fare clic su **Aggiungi**.

    ![Pulsante "Aggiungi" nella finestra di dialogo "Tutte le applicazioni"][3]

5. Nella casella di ricerca digitare **Slack**.

    ![Casella di ricerca in "Aggiungi applicazione"](./media/active-directory-saas-slack-tutorial/tutorial_slack_000.png)

6. Nel riquadro dei risultati selezionare **Slack** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Selezionare "Slack" nel riquadro dei risultati](./media/active-directory-saas-slack-tutorial/tutorial_slack_0001.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configurare e testare l'accesso SSO di Azure AD
In questa sezione viene configurato e testato l'accesso SSO di Azure AD con Slack usando l'utente di test "Britta Simon".

Per il funzionamento dell'accesso SSO, è necessario che Azure AD stabilisca una relazione di collegamento tra l'utente di Azure AD e l'utente corrispondente in Slack. La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente) in Slack.

Per configurare e testare l'accesso SSO di Azure AD con Slack, completare i blocchi predefiniti seguenti:

1. [Configurare l'accesso SSO di Azure AD](#configuring-azure-ad-single-sign-on) per consentire all'utente di usare questa funzionalità.
2. [Creare un utente di test di Azure AD](#creating-an-azure-ad-test-user) per testare l'accesso SSO di Azure AD con l'utente Britta Simon.
3. [Creare un utente di test di Slack](#creating-a-slack-test-user) per fornire all'utente di Azure AD Britta Simon una controparte in Slack a cui collegarsi.
4. [Assegnare l'utente di test di Azure AD](#assigning-the-azure-ad-test-user) per abilitare l'utente Britta Simon all'uso dell'accesso SSO di Azure AD.
5. [Testare l'accesso SSO](#testing-single-sign-on) per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

In questa sezione viene abilitato l'accesso SSO di Azure AD nel portale di Azure e viene configurato l'accesso SSO nell'applicazione Slack seguendo questa procedura:

1. Nella pagina di integrazione dell'applicazione **Slack** del portale di Azure fare clic su **Single Sign-On**.

    ![Pagina di integrazione dell'applicazione Slack][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** nell'elenco **Modalità** per abilitare l'accesso SSO.

    ![Finestra di dialogo "Single Sign-On"](./media/active-directory-saas-slack-tutorial/tutorial_slack_01.png)

3. In **URL e dominio Slack** seguire questa procedura:

    ![Finestra di dialogo "Single Sign-On"](./media/active-directory-saas-slack-tutorial/tutorial_slack_02.png)

    a. Nella casella **URL di accesso** digitare un URL con la convenzione di denominazione *https://<company name>.slack.com*.

    b. Nella casella **Identificatore** digitare **https://slack.com**.

    > [!NOTE]
    > I valori precedenti non sono valori reali. In questo contesto è consigliabile usare valori univoci per l'URL e l'identificatore. Successivamente si aggiorneranno i valori con l'URL e l'identificatore effettivi. Per ottenere i valori, contattare il [team di supporto di Slack](https://slack.com/help/contact).

4. L'applicazione Slack prevede che le asserzioni SAML (Security Assertion Markup Language) vengano visualizzate in un formato specifico. Configurare le attestazioni e gestire i valori degli attributi nella sezione **Attributi utente** della pagina di integrazione dell'applicazione Slack, come illustrato nello screenshot seguente:

    ![Configurare le attestazioni nella sezione Attributi utente](./media/active-directory-saas-slack-tutorial/tutorial_slack_03.png)

5. Nella sezione **Attributi utente** della finestra di dialogo **Single Sign-On** selezionare **user.mail** come **Identificatore utente**. Per ogni riga della tabella, seguire questa procedura:

    | Nome attributo | Valore attributo |
    | --- | --- |    
    | User.Email | user.userprincipalname |
    | first_name | user.givenname |
    | last_name | user.surname |
    | User.Username | extractmailprefix([userprincipalname]) |

    a. Fare clic sul pulsante **Aggiungi attributo**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-slack-tutorial/tutorial_slack_04.png)

    b. Nella casella **Nome** della finestra di dialogo **Aggiungi attributo** immettere il primo nome riportato nella colonna **Nome attributo** della tabella.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-slack-tutorial/tutorial_slack_05.png)

    c. Nella casella **Valore** immettere il primo valore riportato nella colonna **Valore attributo** della tabella.

    d. Fare clic su **OK**.

    e. Ripetere i passaggi da "a" a "d" per le tre righe successive della tabella.

6. In **Certificato di firma SAML** fare clic su **Crea nuovo certificato**.

    ![Creare un certificato](./media/active-directory-saas-slack-tutorial/tutorial_slack_06.png)     

7. Nella finestra di dialogo **Crea nuovo certificato** fare clic sul pulsante **Calendario**, selezionare una data di scadenza e quindi fare clic su **Salva**.

    ![Selezionare una data di scadenza per il certificato](./media/active-directory-saas-slack-tutorial/tutorial_general_300.png)

8. In **Certificato di firma SAML** selezionare la casella di controllo **Make new certificate active** (Rendi attivo nuovo certificato) e quindi fare clic su **Salva**.

    ![Attivare il certificato di firma SAML](./media/active-directory-saas-slack-tutorial/tutorial_slack_07.png)

9. Nella finestra popup **Certificato di rollover** fare clic su **OK**.

    ![Finestra popup Certificato di rollover](./media/active-directory-saas-slack-tutorial/tutorial_general_400.png)

10. In **Certificato di firma SAML** fare clic su **Certificato (base64)** e quindi salvare il file del certificato nell'unità locale.

    ![Salvare il certificato nell'unità locale](./media/active-directory-saas-slack-tutorial/tutorial_slack_08.png)

11. In **Configurazione di Slack** fare clic su **Configura Slack** per aprire la finestra **Configura accesso**.

    ![Fare clic su "Configura Slack" per aprire la finestra Configura accesso](./media/active-directory-saas-slack-tutorial/tutorial_slack_09.png)

    ![Finestra "Configura accesso"](./media/active-directory-saas-slack-tutorial/tutorial_slack_10.png)

12. Aprire una nuova finestra del browser e quindi accedere al sito aziendale di Slack come amministratore.

13. Passare a **Microsoft Azure AD** e quindi a **Team Settings** (Impostazioni team).

    ![Pulsante "Team Settings" (Impostazioni team) di Microsoft Azure AD nel sito aziendale di Slack](./media/active-directory-saas-slack-tutorial/tutorial_slack_001.png)

14. In **Team Settings** (Impostazioni team) fare clic sulla scheda **Authentication** (Autenticazione) e quindi su **Change Settings** (Cambia impostazioni).

    ![Pulsante "Change Settings" (Cambia impostazioni) nella pagina Team Settings (Impostazioni team)](./media/active-directory-saas-slack-tutorial/tutorial_slack_002.png)

15. Nella finestra di dialogo **SAML Authentication Settings** (Impostazioni di autenticazione SAML) seguire questa procedura:

    ![Finestra di dialogo "SAML Authentication Settings" (Impostazioni di autenticazione SAML)](./media/active-directory-saas-slack-tutorial/tutorial_slack_003.png)

    a. Nella casella **SAML 2.0 Endpoint (HTTP)** (Endpoint SAML 2.0 - HTTP) immettere il valore di **SAML Single Sign-On Service URL** (URL servizio Single Sign-On SAML) dalla finestra di configurazione dell'applicazione di Azure AD.

    b. Nella casella **Identity Provider Issuer** (Autorità di certificazione del provider di identità) immettere il valore di **SAML Entity ID** (ID entità SAML) dalla finestra di configurazione dell'applicazione di Azure AD.

    c. Aprire il certificato scaricato nel Blocco note, copiarne il contenuto e quindi incollarlo nella casella **Public Certificate** (Certificato pubblico).

    d. Configurare le tre impostazioni precedenti nel modo appropriato per il proprio team Slack. Per altre informazioni sulle impostazioni, vedere [Guide to single sign-on with Slack](https://get.slack.help/hc/en-us/articles/220403548-Guide-to-single-sign-on-with-Slack) (Guida all'accesso Single Sign-On con Slack).

    e. Fare clic su **Salva configurazione**.

    <!-- Deselezionare **Allow users to change their email address** (Consenti agli utenti di modificare l'indirizzo di posta elettronica).

    e. Selezionare **Consenti agli utenti di scegliere il proprio nome utente**.

    f. In **Authentication for your team must be used by** (L'autenticazione per il team deve essere usata da) selezionare **It’s optional** (Facoltativo). -->


### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD
In questa sezione si crea un utente di test di nome Britta Simon nel portale di Azure seguendo questa procedura:

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante "Azure Active Directory"](./media/active-directory-saas-slack-tutorial/create_aaduser_01.png)

2. Passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.

    ![Pulsante "Tutti gli utenti" in Azure AD](./media/active-directory-saas-slack-tutorial/create_aaduser_02.png)

3. Nella parte superiore della finestra di dialogo **Tutti gli utenti** fare clic su **Aggiungi**.

    ![Pulsante "Aggiungi" nella finestra di dialogo Tutti gli utenti](./media/active-directory-saas-slack-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** immettere le informazioni seguenti:

    ![Finestra di dialogo "Utente"](./media/active-directory-saas-slack-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Create**(Crea).

### <a name="create-a-slack-test-user"></a>Creare un utente di test di Slack

In questa sezione si crea un utente di nome Britta Simon in Slack. Slack supporta il provisioning JIT, abilitato per impostazione predefinita.

Non è necessario alcun intervento dell'utente in questa sezione. Se un utente non esiste in Slack, ne viene creato uno nuovo quando si tenta di accedere a Slack.

> [!NOTE]
> Se è necessario creare un utente manualmente, contattare il [team di supporto di Slack](https://slack.com/help/contact).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abilita l'utente Britta Simon all'uso dell'accesso SSO di Azure concedendole l'accesso a Slack.

![Assegnare un utente per l'accesso SSO di Azure][200]

Per assegnare l'utente Britta Simon a Slack, seguire questa procedura:

1. Nel portale di Azure aprire la visualizzazione applicazioni, passare alla visualizzazione directory, andare ad **Applicazioni aziendali** e quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201]

2. Nell'**elenco delle applicazioni** selezionare **Slack**.

    ![Elenco delle applicazioni nel portale di Azure](./media/active-directory-saas-slack-tutorial/tutorial_slack_50.png)

3. Nel riquadro sinistro fare clic su **Utenti e gruppi**.

    ![Pulsante "Utenti e gruppi" nel riquadro sinistro][202]

4. Fare clic sul pulsante **Aggiungi** e quindi nel pannello **Aggiungi assegnazione** selezionare **Utenti e gruppi**.

    ![Pulsante "Aggiungi" e pannello "Aggiungi assegnazione"][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco **Utenti**.

6. Fare clic sul pulsante **Seleziona**.

7. Nel pannello **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso SSO di Azure AD usando il pannello di accesso.

Per testare la configurazione, passare al pannello di accesso e quindi fare clic sul riquadro **Slack**. L'utente dovrebbe accedere automaticamente all'applicazione Slack.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-slack-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-slack-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-slack-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-slack-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-slack-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-slack-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-slack-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-slack-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-slack-tutorial/tutorial_general_203.png

