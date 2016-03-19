<properties
	pageTitle="Esercitazione: Integrazione di Azure Active Directory con @Task | Microsoft Azure"
	description="Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e @Task."
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/18/2015"
	ms.author="jeedes"/>


# Esercitazione: Integrazione di Azure Active Directory con @Task

L'obiettivo di questa esercitazione è illustrare come integrare @Task con Azure Active Directory (Azure AD).<br>L'integrazione di @Task con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a @Task
- È possibile abilitare gli utenti per l'accesso automatico a @Task (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in una posizione centrale, il portale di Azure Active Directory.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Prerequisiti 

Per configurare l'integrazione di Azure AD con @Task, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di @Task abilitata per l'accesso Single Sign-On


> [AZURE.NOTE]Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/). 

 
## Descrizione dello scenario
L'obiettivo di questa esercitazione è testare l'accesso Single Sign-On di Azure AD in un ambiente di test. <br> Lo scenario descritto in questa esercitazione prevede i tre blocchi predefiniti seguenti:

1. Aggiunta di @Task dalla raccolta 
2. Configurazione e test dell'accesso Single Sign-On di Azure AD


## Aggiunta di @Task dalla raccolta
Per configurare l'integrazione di @Task in Azure AD, è necessario aggiungere @Task dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere @Task dalla raccolta, seguire questa procedura:**

1. Nel **portale di gestione di Azure** fare clic su **Active Directory** nel pannello di navigazione sinistro. <br><br> ![Active Directory][1] <br>

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applicazioni** nel menu superiore. <br><br> ![Applicazioni][2] <br>

4. Fare clic su **Aggiungi** nella parte inferiore della pagina. <br><br> ![Applicazioni][3] <br>

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungi un'applicazione dalla raccolta**. <br><br> ![Applicazioni][4] <br>

6. Nella casella di ricerca digitare **@Task**.<br><br>![Applicazioni][5] <br>

7. Nel riquadro dei risultati selezionare **@Task** e quindi fare clic su **Completa** per aggiungere l'applicazione. <br><br>![Applicazioni][30] <br>



##  Configurazione e test dell'accesso Single Sign-On di Azure AD

Questa sezione descrive come configurare e testare l'accesso Single Sign-On di Azure AD con @Task in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di @Task che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in @Task.<br> La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** in @Task.
 
Per configurare e testare l'accesso Single Sign-On di Azure AD con @Task, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Creazione di un utente test di @Task](#creating-a-halogen-software-test-user)**: per avere una controparte di Britta Simon in @Task collegata alla relativa rappresentazione in Azure AD.
5. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene descritto come abilitare Single Sign-On di Azure AD nel portale di Azure AD e configurare l'accesso Single Sign-On nell'applicazione @Task.<br>

**Per configurare Single Sign-On di Azure AD con @Task, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **@Task** del portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**. <br><br> ![Configura accesso Single Sign-On][6] <br>

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a @Task** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**. <br><br> ![Accesso Single Sign-On di Azure AD][7] <br>

3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura: <br><br>![Configurare le impostazioni dell'app][8] <br>
 
     a. Nella casella di testo **URL di accesso** digitare l'URL usato dagli utenti per accedere all'applicazione @Task, ad esempio:*https://<Tenant name>.attask-ondemand.com*.

     b. Fare clic su **Avanti**.

4. Nella pagina **Configura accesso Single Sign-On in @Task** fare clic su **Scarica metadati**, salvare il file di metadati in locale nel computer e quindi fare clic su **Avanti**. <br><br>![Cos'è Azure AD Connect][9] <br>



1. Accedere al sito aziendale di @Task come amministratore.

2. Andare a **Single Sign On Configuration**.


1. Nella finestra di dialogo **Single Sign-On** seguire questa procedura <br><br>![Configura accesso Single Sign-On][23]<br>

    a. Come **Type** selezionare **SAML 2.0**.

    b. Selezionare **Service Provider ID**.

    c. Nel portale di Azure copiare il valore di **Remote Login URL** e quindi incollarlo nella casella di testo **URL del portale di accesso**.

    d. Nel portale di Azure copiare il valore di **Single Sign-Out Service URL** e incollarlo nella casella di testo **URL di disconnessione**.

    e. Nel portale di Azure copiare il valore di **Change Password URL** e incollarlo nella casella di testo **Modifica URL password**.

    e. Fare clic su **Salva**.

6. Nel portale di Azure AD selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**. <br><br>![Cos'è Azure AD Connect][10]<br>

7. Nella pagina **Conferma Single Sign-On** fare clic su **Completa**. <br><br>![Cos'è Azure AD Connect][11]




### Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test nel portale di Azure denominato Britta Simon.<br> Nell'elenco di utenti selezionare **Britta Simon**.<br><br>![Creare un utente di Azure AD][20]<br>

**Per creare un utente test in Azure AD, seguire questa procedura:**

1. Nel **portale di gestione di Azure** fare clic su **Active Directory** nel pannello di navigazione sinistro.<br> ![Creazione di un utente test di Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_02.png) 

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per visualizzare l'elenco di utenti, nel menu in alto fare clic su **Utenti**.<br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_03.png)
 
4. Per aprire la finestra di dialogo **Aggiungi utente**, nella barra degli strumenti in basso fare clic su **Aggiungi utente**. <br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_04.png)

5. Nella pagina **Informazioni sull'utente** seguire questa procedura: <br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_05.png)

    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.

    b. Nella **casella di testo** Nome utente digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina **Profilo utente** seguire questa procedura: <br><br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_06.png) <br>
 
    a. Nella casella di testo **Nome** digitare **Britta**.

    b. Nella casella di testo **Cognome** digitare **Simon**.

    c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.

    d. Nell'elenco **Ruolo** selezionare **Utente**. e. Fare clic su **Avanti**.

7. Nella pagina **Ottieni password temporanea** fare clic su **Crea**. <br><br> ![Creazione di un utente test di Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_07.png) <br>
 
8. Nella pagina **Ottieni password temporanea** seguire questa procedura: <br><br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_08.png) <br>
  
    a. Prendere nota del valore in **Nuova password**.

    b. Fare clic su **Complete**.

  
 
### Creazione di un utente test di @Task

L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon in @Task.


**Per creare un utente denominato Britta Simon in @Task, seguire questa procedura:**

1. Accedere al sito aziendale di @Task come amministratore.

2. Nel menu in alto fare clic su **People**.

3. Fare clic su **New Person**.

4. Nella finestra di dialogo New Person seguire questa procedura: <br><br>![Creare un utente test di @Task][21] <br>

    a. Nella casella di testo **First Name** digitare "Britta".

    b. Nella casella di testo **Last Name** digitare "Simon".

    c. Nella casella di testo **Email Address** digitare l'indirizzo di posta elettronica di Britta Simon in Azure Active Directory.

    d. Fare clic su **Add Person**.




### Assegnazione dell'utente test di Azure AD

L'obiettivo di questa sezione consiste nell'abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a @Task. <br><br>![Assegna utente][200] <br>

**Per assegnare Britta Simon a @Task, seguire questa procedura:**

1. Per aprire la visualizzazione applicazioni nel portale di Azure, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto. <br><br>![Assegna utente][201] <br>

2. Nell'elenco delle applicazioni selezionare **@Task**. <br><br>![Assegna utente][202] <br>

1. Nel menu in alto fare clic su **Utenti**. <br><br>![Assegna utente][203] <br>

1. Nell'elenco di utenti selezionare **Britta Simon**.

2. Sulla barra degli strumenti in basso fare clic su **Assegna**. <br><br>![Assegna utente][205]



### Test dell'accesso Single Sign-On

In questa sezione viene descritto come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.<br> Quando si fa clic sul riquadro @Task nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione @Task.


## Risorse aggiuntive

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

<!---HONumber=AcomDC_1223_2015-->