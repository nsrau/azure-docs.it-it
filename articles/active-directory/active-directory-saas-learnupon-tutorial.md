<properties
	pageTitle="Esercitazione: Integrazione di Azure Active Directory con Novatus | Microsoft Azure"
	description="Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e LearnUpon."
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
	ms.date="02/11/2016"
	ms.author="jeedes"/>


# Esercitazione: Integrazione di Azure Active Directory con LearnUpon

Questa esercitazione illustra come integrare LearnUpon con Azure Active Directory (Azure AD).<br>L'integrazione di LearnUpon con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a LearnUpon
- È possibile abilitare gli utenti per l'accesso automatico a LearnUpon (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in una posizione centrale, il portale di Azure Active Directory classico. 
- 

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Prerequisiti

Per configurare l'integrazione di Azure AD con LearnUpon, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di LearnUpon abilitata per l'accesso Single Sign-On


> [AZURE.NOTE] Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non è disponibile un ambiente di prova di Azure AD, è possibile ottenere una versione di prova di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).


## Descrizione dello scenario
L'obiettivo di questa esercitazione è testare l'accesso Single Sign-On di Azure AD in un ambiente di test. <br> Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di LearnUpon dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD


## Aggiunta di LearnUpon dalla raccolta
Per configurare l'integrazione di LearnUpon in Azure AD, è necessario aggiungere LearnUpon dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere LearnUpon dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro. <br><br>![Active Directory][1] <br>

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.<br><br> ![Applicazioni][2]<br>
4. Fare clic su **Aggiungi** nella parte inferiore della pagina.<br><br> ![Applicazioni][3]<br>
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.<br><br> ![Applicazioni][4]<br>
6. Nella casella di ricerca digitare **LearnUpon**.<br><br> ![Creazione di un utente test di Azure AD](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_01.png)<br>
7. Nel riquadro dei risultati selezionare **LearnUpon** e quindi fare clic su **Completa** per aggiungere l'applicazione. <br><br> ![Creazione di un utente test di Azure AD](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_02.png)<br>
##  Configurazione e test dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come configurare e testare l'accesso Single Sign-On di Azure AD con LearnUpon con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di LearnUpon che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in LearnUpon.<br> La relazione di collegamento viene stabilita assegnando al valore di **nome utente** in Azure AD lo stesso valore di **Username** in LearnUpon.

Per configurare e testare l'accesso Single Sign-On di Azure AD con LearnUpon, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Creazione di un utente test di LearnUpon](#creating-a-learnupon-test-user)**: per avere una controparte di Britta Simon in LearnUpon collegata alla relativa rappresentazione in Azure AD.
5. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### Configurazione dell'accesso Single Sign-On di Azure AD

Questa sezione descrive come abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure AD e configurare l'accesso Single Sign-On nell'applicazione LearnUpon.



**Per configurare Single Sign-On di Azure AD con LearnUpon, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **LearnUpon** del portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**. <br><br> ![Configura accesso Single Sign-On][6] <br>

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a LearnUpon** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**. <br><br> ![Configura accesso Single Sign-On](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_03.png) <br>

3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura: <br><br>![Configura accesso Single Sign-On](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_04.png) <br>


    a. Nella casella di testo URL di risposta immettere l'URL relativo al servizio consumer di asserzione usando il modello seguente:**"https://<nomeazienda>.learnupon.com/saml/consumer"**.


4. Nella pagina **Configura accesso Single Sign-On in LearnUpon** seguire questa procedura: <br><br>![Configura accesso Single Sign-On](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_05.png) <br>

    a. Fare clic su **Scarica certificato** e quindi salvare il file nel computer. Questo certificato e gli URL dei metadati (ID entità, URL di accesso SSO e URL di disconnessione SSO) saranno necessari per configurare l'accesso Single Sign-On nel sito LearnUpon.

    b. Fare clic su **Avanti**.


5. Aprire un altro accesso dell'istanza del browser nell'istanza di LearnUpon con l'utente amministratore impostato su **SAML SSO** sul lato LearnUpon. Dopo aver eseguito l'accesso a LearnUpon, verrà visualizzata una schermata simile alla seguente. <br><br>![Configura accesso Single Sign-On](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_06.png) <br>

	a. Fare clic sulla scheda **settings** per aprire la finestra delle impostazioni.<br> b. Fare clic su **Single Sign On - SAML**<br> c. Fare clic su **General Settings** per configurare le impostazioni SAML. <br><br>![Configura accesso Single Sign-On](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_07.png) <br> d. Compilare il modulo **General Settings** come indicato di seguito: <br><br>![Configura accesso Single Sign-On](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_08.png) <br> d1. Selezionare la casella di controllo **Enabled** per abilitare SAML nel portale<br> d2. Scegliere **Version 2.0**<br> d3. Scegliere **Skip conditions - No**<br> d4. **SAML Token POST param name** è il nome del parametro post di richiesta all'URL del consumer SAML indicato sopra che contiene l'asserzione SAML da verificare e autenticare, ad esempio **SAMLResponse** <br> d5. **Name Identifier Format** indica la posizione dell'identificatore degli utenti (indirizzo di posta elettronica) nell'asserzione SAML, ad esempio **urn:oasis:names:tc:SAML:1.1:nameid- format:emailAddress**.<br> d6. **Identify Provider Location** è il percorso a cui verranno indirizzati gli utenti quando fanno clic sull'icona caricata dalla schermata di accesso del portale.<br> d7. Copiare l'**URL servizio Single Sign-Out** della schermata di configurazione di Azure in **Sign out URL**. <br>![Configura accesso Single Sign-On](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_09.png) <br> d8. Fare clic sul collegamento **Manage finger prints** in Certificate Finger Print per caricare le impronte digitali del certificato. <br>![Configura accesso Single Sign-On](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_10.png) <br> d9. Fare clic sul pulsante Save e. Fare clic su **User Settings** per configurare le impostazioni utente di SAML. <br><br>![Configura accesso Single Sign-On](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_11.png) <br> e1. **First Name Identifier Format** indica il punto in cui si trovano i nomi degli utenti nell'asserzione SAML, ad esempio **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ givenname**. e2. **Last Name Identifier Format** indica il punto in cui si trovano i cognomi degli utenti nell'asserzione SAML, ad esempio **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ surname**


6. Nel portale di Azure AD selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**. <br><br>![Accesso Single Sign-On di Azure AD][10]<br>

7. Nella pagina **Conferma Single Sign-On** fare clic su **Completa**. <br>![Accesso Single Sign-On di Azure AD][11]




### Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di Azure classico.<br> Nell'elenco di utenti selezionare **Britta Simon**.<br><br>![Creare un utente di Azure AD][20]<br>

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro. <br><br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_09.png) <br>

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per visualizzare l'elenco di utenti, fare clic su **Utenti** nel menu in alto. <br><br> ![Creazione di un utente test di Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_03.png) <br>

4. Per aprire la finestra di dialogo **Aggiungi utente**, nella barra degli strumenti in basso fare clic su **Aggiungi utente**. <br><br> ![Creazione di un utente test di Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_04.png) <br>

5. Nella pagina della finestra di dialogo **Informazioni sull'utente** seguire questa procedura: <br><br> ![Creazione di un utente test di Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_05.png) <br>

    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.

    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina della finestra di dialogo **Profilo utente** seguire questa procedura: <br><br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_06.png) <br>

    a. Nella casella di testo **Nome** digitare **Britta**.

    b. Nella casella di testo **Cognome** digitare **Simon**.

    c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.

    d. Nell'elenco **Ruolo**, selezionare **Utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina della finestra di dialogo **Ottieni password temporanea**, fare clic su **crea**. <br><br> ![Creazione di un utente test di Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_07.png) <br>

8. Nella pagina **Ottieni password temporanea** seguire questa procedura: <br><br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_08.png) <br>

    a. Prendere nota del valore visualizzato in **Nuova password**.

    b. Fare clic su **Complete**.



### Creazione di un utente test LearnUpon

Questa sezione descrive come creare un utente chiamato Britta Simon in LearnUpon. LearnUpon supporta il provisioning just-in-time, che è abilitato per impostazione predefinita.

Non è necessario alcun intervento dell'utente in questa sezione. Durante un tentativo di accesso a LearnUpon verrà creato un nuovo utente, se questo non esiste già. [Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on).

> [AZURE.NOTE] Per creare un utente manualmente, è necessario contattare il team di supporto di LearnUpon.


### Assegnazione dell'utente test di Azure AD

Questa sezione descrive come abilitare Britta Simon per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a LearnUpon. <br><br>![Assegna utente][200] <br>

**Per assegnare Britta Simon a LearnUpon, seguire questa procedura:**

1. Per aprire la visualizzazione applicazioni nel portale di Azure classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto. <br><br>![Assegna utente][201] <br>

2. Nell'elenco di applicazioni selezionare **LearnUpon**. <br><br>![Configura accesso Single Sign-On](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_50.png) <br>

1. Nel menu in alto, fare clic su **Utenti**. <br><br>![Assegna utente][203] <br>

1. Nell'elenco di utenti selezionare **Britta Simon**.

2. Fare clic su **Assegna** sulla barra degli strumenti in basso. <br><br>![Assegna utente][205]



### Test dell'accesso Single Sign-On

Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.<br> Quando si fa clic sul riquadro LearnUpon nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione LearnUpon.


## Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0218_2016-->