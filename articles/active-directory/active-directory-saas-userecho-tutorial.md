<properties
	pageTitle="Esercitazione: Integrazione di Azure Active Directory con UserEcho | Microsoft Azure"
	description="Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e UserEcho."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/05/2015"
	ms.author="markusvi"/>


# Esercitazione: Integrazione di Azure Active Directory con UserEcho

Questa esercitazione illustra come integrare UserEcho con Azure Active Directory (Azure AD).<br>L'integrazione di UserEcho con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a UserEcho. 
- È possibile abilitare gli utenti per l'accesso automatico a UserEcho (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in una posizione centrale, il portale di Azure Active Directory.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Prerequisiti 

Per configurare l'integrazione di Azure AD con UserEcho, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di UserEcho abilitata per l'accesso Single Sign-On


> [AZURE.NOTE]Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non si ha un ambiente di prova di Azure AD, fare clic [qui](https://azure.microsoft.com/pricing/free-trial/) per ottenere una versione di valutazione valida un mese. 

 
## Descrizione dello scenario
L'obiettivo di questa esercitazione è testare l'accesso Single Sign-On di Azure AD in un ambiente di test. <br> Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di UserEcho dalla raccolta 
2. Configurazione e test dell'accesso Single Sign-On di Azure AD


## Aggiunta di UserEcho dalla raccolta
Per configurare l'integrazione di UserEcho in Azure AD, è necessario aggiungere UserEcho dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere UserEcho dalla raccolta, seguire questa procedura:**

1. Nel **portale di gestione di Azure** fare clic su **Active Directory** nel riquadro di spostamento sinistro. <br><br> ![Active Directory][1]<br>

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.<br><br> ![Applicazioni][2]<br>
4. Fare clic su **Aggiungi** nella parte inferiore della pagina.<br><br> ![Applicazioni][3]<br>
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.<br><br> ![Applicazioni][4]<br>
6. Nella casella di ricerca digitare **UserEcho**.<br><br> ![Creazione di un utente test di Azure AD](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_01.png)<br>
7. Nel riquadro dei risultati selezionare **UserEcho** e quindi fare clic su **Completa** per aggiungere l'applicazione. <br><br>

##  Configurazione e test dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come configurare e testare l'accesso Single Sign-On di Azure AD con UserEcho con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di UserEcho che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in UserEcho.<br> La relazione di collegamento viene stabilita assegnando al valore di **nome utente** in Azure AD lo stesso valore di **Username** in UserEcho.
 
Per configurare e testare l'accesso Single Sign-On di Azure AD con UserEcho, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Creazione di un utente test di UserEcho](#creating-a-userecho-test-user)**: per avere una controparte di Britta Simon in UserEcho collegata alla relativa rappresentazione in Azure AD.
5. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### Configurazione dell'accesso Single Sign-On di Azure AD

Questa sezione descrive come abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure AD e configurare l'accesso Single Sign-On nell'applicazione UserEcho.




**Per configurare l'accesso Single Sign-On di Azure AD con UserEcho, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **UserEcho** del portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**. <br><br> ![Configura accesso Single Sign-On][6] <br>

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a UserEcho** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**. <br><br> ![Configura accesso Single Sign-On](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_03.png) <br>

3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura: <br><br>![Configura accesso Single Sign-On](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_04.png) <br>

    a. Nella casella di testo **URL di accesso** digitare l'URL usato dagli utenti per accedere all'applicazione UserEcho, ad esempio **https://fabrikam.UserEcho.com/*).

    b. Fare clic su **Avanti**.
 
 
4. Nella pagina **Configura accesso Single Sign-On in UserEcho** seguire questa procedura: <br><br>![Configura accesso Single Sign-On](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_05.png) <br>

    a. Fare clic su **Scarica certificato** e quindi salvare il file nel computer.

    b. Fare clic su **Avanti**.


1. In un'altra finestra del browser accedere al sito aziendale di UserEcho come amministratore.

1. Sulla barra degli strumenti in alto fare clic sul nome utente per espandere il menu e quindi fare clic su **Setup**. <br><br>![Configura accesso Single Sign-On](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_06.png) <br>

1. Fare clic su **Integrations**. <br><br>![Configura accesso Single Sign-On](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_07.png) <br>


1. Fare clic su **Website** e quindi su **Single sign-on (SAML2)**. <br><br>![Configura accesso Single Sign-On](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_08.png) <br>


1. Nella pagina **Single sign-on (SAML)** seguire questa procedura: <br><br>![Configura accesso Single Sign-On](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_09.png) <br>

    a. In **SAML-enabled** selezionare **Yes**.

    b. Nella pagina **Configura accesso Single Sign-On in UserEcho** del portale di Azure copiare il valore di **URL servizio Single Sign-On**, aprire il certificato scaricato nel Blocco note, copiarne il contenuto e incollarlo nella casella di testo **SAML SSO URL**.

    c. Nella pagina **Configura accesso Single Sign-On in UserEcho** del portale di Azure copiare il valore di **URL disconnessione remota**, aprire il certificato scaricato nel Blocco note, copiarne il contenuto e incollarlo nella casella di testo **Remote logout URL**.

    d. Aprire il certificato scaricato nel Blocco note, copiarne il contenuto e incollarlo nella casella di testo **X.509 Certificate**.

    e. Fare clic su **Salva**.


6. Nel portale di Azure AD selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**. <br><br>![Accesso Single Sign-On di Azure AD][10]<br>

7. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**. <br><br>![Accesso Single Sign-On di Azure AD][11]




### Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di Azure.<br> Nell'elenco di utenti selezionare **Britta Simon**.<br><br>![Creare un utente di Azure AD][20]<br>

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di gestione di Azure** fare clic su **Active Directory** nel riquadro di spostamento sinistro. <br><br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_09.png) <br> 

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per visualizzare l'elenco di utenti, nel menu in alto fare clic su **Utenti**. <br><br> ![Creazione di un utente test di Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_03.png) <br>
 
4. Per aprire la finestra di dialogo **Aggiungi utente**, nella barra degli strumenti in basso fare clic su **Aggiungi utente**. <br><br> ![Creazione di un utente test di Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_04.png) <br>

5. Nella pagina **Informazioni sull'utente** seguire questa procedura: <br><br> ![Creazione di un utente test di Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_05.png) <br>

    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.

    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina **Profilo utente** seguire questa procedura: <br><br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_06.png) <br>
 
    a. Nella casella di testo **Nome** digitare **Britta**.

    b. Nella casella di testo **Cognome** digitare **Simon**.

    c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.

    d. Nell'elenco **Ruolo** selezionare **Utente**. e. Fare clic su **Avanti**.

7. Nella pagina **Ottieni password temporanea** fare clic su **crea**. <br><br> ![Creazione di un utente test di Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_07.png) <br>
 
8. Nella pagina **Ottieni password temporanea** seguire questa procedura: <br><br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_08.png) <br>
  
    a. Prendere nota del valore visualizzato in **Nuova password**.

    b. Fare clic su **Completa**.

  
 
### Creazione di un utente test per UserEcho

Questa sezione descrive come creare un utente chiamato Britta Simon in UserEcho.

**Per creare un utente denominato Britta Simon in UserEcho, seguire questa procedura:**

1. Accedere al sito aziendale di UserEcho come amministratore.

1. Sulla barra degli strumenti in alto fare clic sul nome utente per espandere il menu e quindi fare clic su **Setup**. <br><br>![Configura accesso Single Sign-On](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_06.png) <br>

1. Fare clic su **Users** per espandere la sezione **Users**. <br><br>![Configura accesso Single Sign-On](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_10.png) <br>

1. Fare clic su **Users**. <br><br>![Configura accesso Single Sign-On](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_11.png) <br>

1. Fare clic su **Invite a new user**. <br><br>![Configura accesso Single Sign-On](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_12.png) <br>


1. Nella finestra di dialogo **Invite a new user** seguire questa procedura: <br><br>![Configura accesso Single Sign-On](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_13.png) <br>

    a. Nella casella di testo **Name** digitare **Britta Simon**.

    b. Nella casella di testo **Email** digitare l'indirizzo di posta elettronica di Britta Simon nel portale di Azure.

    c. Fare clic su **Invite**.

A Britta viene inviato un invito che le permette di iniziare a usare UserEcho.



### Assegnazione dell'utente test di Azure AD

Questa sezione descrive come abilitare Britta Simon per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a UserEcho. <br><br>![Assegna utente][200] <br>

**Per assegnare Britta Simon a UserEcho, seguire questa procedura:**

1. Per aprire la visualizzazione applicazioni nel portale di Azure, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto. <br><br>![Assegna utente][201] <br>

2. Nell'elenco di applicazioni selezionare **UserEcho**. <br><br>![Configura accesso Single Sign-On](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_50.png) <br>

1. Nel menu in alto fare clic su **Utenti**. <br><br>![Assegna utente][203] <br>

1. Nell'elenco di utenti selezionare **Britta Simon**.

2. Fare clic su **Assegna** sulla barra degli strumenti in basso. <br><br>![Assegna utente][205]



### Test dell'accesso Single Sign-On

Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.<br> Quando si fa clic sul riquadro UserEcho nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione UserEcho.


## Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_205.png

<!---HONumber=Nov15_HO2-->