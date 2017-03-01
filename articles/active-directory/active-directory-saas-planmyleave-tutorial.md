---
title: 'Esercitazione: Integrazione di Azure Active Directory con PlanMyLeave | Documentazione Microsoft'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e PlanMyLeave.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: b0d31cbe-7ae2-488b-9cf3-4927391fa744
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 83334b1b02df214e51c86a862636c9392cd19474
ms.openlocfilehash: ba418a641b339a0d94a3c7b2596d37fbd88a30c5
ms.lasthandoff: 02/15/2017


---
# <a name="tutorial-azure-active-directory-integration-with-planmyleave"></a>Esercitazione: Integrazione di Azure Active Directory con PlanMyLeave

Questa esercitazione descrive come integrare PlanMyLeave con Azure Active Directory (Azure AD).

L'integrazione di PlanMyLeave con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a PlanMyLeave
- È possibile abilitare gli utenti per l'accesso automatico a PlanMyLeave (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account da una posizione centrale: il portale di gestione di Azure

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con PlanMyLeave, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di PlanMyLeave abilitata per l'accesso Single Sign-On


> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non è disponibile un ambiente di prova di Azure AD, è possibile ottenere una versione di prova di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di PlanMyLeave dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD


## <a name="adding-planmyleave-from-the-gallery"></a>Aggiunta di PlanMyLeave dalla raccolta
Per configurare l'integrazione di PlanMyLeave in Azure AD, è necessario aggiungere PlanMyLeave dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere PlanMyLeave dalla raccolta, seguire questa procedura:**

1. Nel **[portale di gestione di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Aggiungi** nella parte superiore della finestra di dialogo.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **PlanMyLeave**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_001.png)

5. Nel pannello dei risultati selezionare **PlanMyLeave** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con PlanMyLeave in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente di PlanMyLeave che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in PlanMyLeave.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente) in PlanMyLeave.

Per configurare e testare l'accesso Single Sign-On di Azure AD con PlanMyLeave, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di PlanMyLeave](#creating-a-planmyleave-test-user)**: per avere una controparte di Britta Simon in PlanMyLeave collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di gestione di Azure e viene configurato l'accesso Single Sign-On nell'applicazione PlanMyLeave.

**Per configurare l'accesso Single Sign-On di Azure AD con PlanMyLeave, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **PlanMyLeave** del portale di gestione di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** in **Modalità** selezionare **Accesso basato su SAML** per abilitare Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_01.png)

3. Nella sezione **URL e dominio PlanMyLeave** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_02.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<company-name>.planmyleave.com/Login.aspx`
    
    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<company-name>.planmyleave.com`

    > [!NOTE] 
    > Si noti che questi non sono i valori reali. È necessario aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto di PlanMyLeave](mailto:support@planmyleave.com).

4. Nella sezione **Certificato di firma SAML** fare clic su **Crea nuovo certificato**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_03.png)     

5. Nella finestra di dialogo **Crea nuovo certificato** fare clic sull'icona del calendario e selezionare una **data di scadenza**. Fare quindi clic sul pulsante **Salva**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-planmyleave-tutorial/tutorial_general_300.png)

6. Nella sezione **Certificato di firma SAML** selezionare **Rendi attivo il certificato nuovo** e fare clic sul pulsante **Salva**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_04.png)

7. Nella finestra popup **Certificato di rollover** fare clic su **OK**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-planmyleave-tutorial/tutorial_general_400.png)

8. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_05.png) 

9. Nella sezione **Configurazione di PlanMyLeave** fare clic su **Configura PlanMyLeave** per aprire la finestra **Configura accesso**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_06.png) 

    ![Configura accesso Single Sign-On](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_07.png)

10. In un'altra finestra del browser Web accedere al tenant di PlanMyLeave come amministratore.

11. Passare a **System Setup**. Quindi nella sezione **Gestione sicurezza** fare clic su **Company SAML settings** (Impostazioni SAML azienda).

    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_002.png) 

12. Nella sezione **SAML Settings** (Impostazioni SAML) fare clic sull'icona dell'editor.

    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_003.png)

13. Nella sezione **Update SAML Settings** (Aggiorna impostazioni SAML) eseguire i seguenti passaggi:

    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_004.png)

    a.  Nella casella di testo **Login URL** (URL di accesso) inserire il valore di **SAML Single Sign-On Service URL** (URL servizio Single Sign-On SAML) dalla finestra di configurazione dell'applicazione di Azure AD.

    b.  Aprire il certificato scaricato nel Blocco note, copiarne solo il contenuto fra ---Begin Certificate--- ed ---End certificate---- negli Appunti e incollarlo nella casella di testo **Certificato**.

    c. Impostare "**Is Enable**" su "**Yes**".

    d. Fare clic su **Save**.



### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di gestione di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di gestione di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-planmyleave-tutorial/create_aaduser_01.png) 

2. Andare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-planmyleave-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-planmyleave-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** eseguire la procedura seguente:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-planmyleave-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Create**(Crea). 



### <a name="creating-a-planmyleave-test-user"></a>Creazione di un utente test di PlanMyLeave

Questa sezione descrive come creare un utente chiamato Britta Simon in PlanMyLeave. PlanMyLeave supporta il provisioning just-in-time, che è abilitato per impostazione predefinita.

Non è necessario alcun intervento dell'utente in questa sezione. Durante un tentativo di accesso a PlanMyLeave verrà creato un nuovo utente se questo non è già esistente.

> [!NOTE]
> Per creare un utente manualmente, è necessario contattare il [team di supporto di PlanMyLeave](mailto:support@planmyleave.com).



### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure, concedendole l'accesso a PlanMyLeave.

![Assegna utente][200] 

**Per assegnare Britta Simon a PlanMyLeave, seguire questa procedura:**

1. Nel portale di gestione di Azure aprire la visualizzazione applicazioni, passare alla visualizzazione directory e andare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **PlanMyLeave**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_50.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    


### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro PlanMyLeave nel pannello di accesso, si accederà automaticamente all'applicazione PlanMyLeave.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_203.png
