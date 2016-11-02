<properties
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con @Task| Microsoft Azure"
    description="Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e @Task."
    services="active-directory"
    documentationCenter=""
    authors="jeevansd"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/01/2016"
    ms.author="jeedes"/>



# <a name="tutorial:-azure-active-directory-integration-with-@task"></a>Esercitazione: Integrazione di Azure Active Directory con @Task

Questa esercitazione descrive l'integrazione di @Task con Azure Active Directory (Azure AD).  
L'integrazione di @Task con Azure AD offre i vantaggi seguenti: 

- È possibile controllare in Azure AD chi può accedere a @Task
- È possibile abilitare gli utenti per l'accesso automatico ad @Task (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti 

Per configurare l'integrazione di Azure AD con @Task,, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di @Task abilitata per l'accesso Single Sign-On


> [AZURE.NOTE] Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non è disponibile un ambiente di prova di Azure AD, è possibile ottenere una versione di prova di un mese [qui](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è testare l'accesso Single Sign-On di Azure AD in un ambiente di test.  
Lo scenario descritto in questa esercitazione è costituito da tre blocchi principali:

1. Aggiunta di @Task dalla raccolta 
2. Configurazione e test dell'accesso Single Sign-On di Azure AD


## <a name="adding-@task-from-the-gallery"></a>Aggiunta di @Task dalla raccolta
Per configurare l'integrazione di @Task in Azure AD è necessario aggiungere @Task dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere @Task dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1] 

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applications][2] 

4. Fare clic su **Add** nella parte inferiore della pagina.

    ![Applicazioni][3] 

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Applicazioni][4] 

6. Nella casella di ricerca digitare **@Task**.

    ![Applicazioni][5] 

7. Nel riquadro dei risultati selezionare **@Task**, quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Applicazioni][30] 



##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD

Questa sezione descrive come configurare e testare l'accesso Single Sign-On di Azure AD con @Task in base a un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di @Task che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in @Task.   
La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** in @Task.
 
Per configurare e testare l'accesso Single Sign-On di Azure AD con @Task,, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Creazione di un utente @Tasktest ](#creating-a-halogen-software-test-user)** per avere una controparte di Britta Simon in @Taskthat collegata alla relativa rappresentazione in Azure AD.
5. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

Questa sezione descrive come abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure classico e configurare l'accesso Single Sign-On nell'applicazione @Task.

**Per configurare Single Sign-On di Azure AD con @Task,, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **@Task** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo** Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On][6] 

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a @Task** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.

    ![Single Sign-On di Microsoft Azure AD][7] 

3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:

    ![Configurare le impostazioni dell'app][8] 
 
     a. Nella casella di testo **URL di accesso** digitare l'URL utilizzato dagli utenti per accedere all'applicazione @Task, ad esempio:*https://<Tenant name>.attask-ondemand.com*.

     b. Fare clic su **Avanti**.

4. Nella pagina **Configura accesso Single Sign-On in @Task** fare clic su **Scarica metadati**, salvare il file di metadati in locale nel computer e quindi fare clic su **Avanti**.

    ![Cos'è Azure AD Connect][9] 



1. Accedere al sito aziendale di @Task come amministratore.

2. Andare a **Single Sign On Configuration**.


1. Nella finestra di dialogo **Single Sign-On** seguire questa procedura

    ![Configura accesso Single Sign-On][23]

    a. Per **Type** selezionare**SAML 2.0**.

    b. Selezionare **Service Provider ID**.

    c. Nel portale di Azure classico copiare il valore di **URL accesso remoto** e incollarlo nella casella di testo **Login Portal UR** (URL portale di accesso).

    d. Nel portale di Azure classico copiare il valore di **URL servizio Single Sign-Out** e incollarlo nella casella di testo **Sign-Out URL** (URL di disconnessione).

    e. Nel portale di Azure classico copiare il valore di **Modifica URL password** e incollarlo nella casella di testo **Change Password URL** (Modifica URL password).

    f. Fare clic su **Save**.

6. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e fare clic su **Avanti**. 

    ![Cos'è Azure AD Connect][10]

7. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  

    ![Cos'è Azure AD Connect][11]




### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di Azure classico.  

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_02.png) 

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_03.png) 
 
4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso. 

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_04.png) 

5. Nella pagina **Informazioni sull'utente** seguire questa procedura: 

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_05.png) 

    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.

    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina **Profilo utente** seguire questa procedura: 

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_06.png) 
 
    a. Nella casella di testo **Nome** digitare **Britta**.  

    b. Nella casella di testo **Cognome** digitare **Simon**.

    c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.

    d. Nell'elenco **Ruolo** selezionare **Utente**.
    e. Fare clic su **Avanti**.

7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_07.png) 
 
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_08.png) 
  
    a. Prendere nota del valore visualizzato in **Nuova password**.

    b. Fare clic su **Completa**.   

  
 
### <a name="creating-an-@task-test-user"></a>Creazione di un utente di test di @Task

L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon in @Task.


**Per creare un utente denominato Britta Simon in @Task,, seguire questa procedura:**

1. Accedere al sito aziendale di @Task come amministratore.

2. Nel menu in alto fare clic su **People**.

3. Fare clic su **New Person**. 

4. Nella finestra di dialogo New Person seguire questa procedura:

    ![Creare un utente di test di @Task][21] 

    a. Nella casella di testo **First Name** digitare "Britta".

    b. Nella casella di testo **Last Name** digitare "Simon".

    c. Nella casella di testo **Email Address** digitare l'indirizzo di posta elettronica di Britta Simon in Azure Active Directory.

    d. Fare clic su **Add Person**.




### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

L’obiettivo di questa sezione consiste nell’abilitare Britta Simon all’utilizzo dell’accesso Single Sign-On di Azure concedendole l’accesso a @Task.

![Assegna utente][200] 

**Per assegnare Britta Simon a @Task,, seguire questa procedura:**

1. Per aprire la visualizzazione applicazioni nel portale di Azure classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **@Task**.

    ![Assegna utente][202] 

1. Scegliere **Utenti**dal menu in alto.

    ![Assegna utente][203] 

1. Nell'elenco di utenti selezionare **Britta Simon**.

2. Fare clic su **Assegna**sulla barra degli strumenti in basso.

    ![Assegna utente][205]



### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.  
Quando si fa clic sul riquadro @Task nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione @Task.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-attask-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-attask-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-attask-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-attask-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_01.png
[30]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_02.png


[6]: ./media/active-directory-saas-attask-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_03.png
[8]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_04.png
[9]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_05.png
[10]: ./media/active-directory-saas-attask-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-attask-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-attask-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_08.png


[23]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_06.png

[200]: ./media/active-directory-saas-attask-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-attask-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_09.png
[203]: ./media/active-directory-saas-attask-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-attask-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-attask-tutorial/tutorial_general_205.png









<!--HONumber=Oct16_HO2-->


