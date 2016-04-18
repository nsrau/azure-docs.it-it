<properties
	pageTitle="Esercitazione: Integrazione di Azure Active Directory con Novatus | Microsoft Azure"
	description="Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e HackerOne."
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
	ms.date="03/30/2016"
	ms.author="jeedes"/>


# Esercitazione: Integrazione di Azure Active Directory con HackerOne

Questa esercitazione descrive come integrare HackerOne con Azure Active Directory (Azure AD).

L'integrazione di HackerOne con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a HackerOne
- È possibile abilitare gli utenti per l'accesso automatico a HackerOne (Single Sign-On) con l'account Azure AD.
- È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Prerequisiti

Per configurare l'integrazione di Azure AD con HackerOne, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di HackerOne abilitata per l'accesso Single Sign-On


> [AZURE.NOTE] Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non è disponibile un ambiente di prova di Azure AD, è possibile ottenere una versione di prova di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).


## Descrizione dello scenario
In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. <br> Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di HackerOne dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD


## Aggiunta di HackerOne dalla raccolta
Per integrare HackerOne in Azure AD, è necessario aggiungere HackerOne dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere HackerOne dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro. <br><br>![Active Directory][1] <br>

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.<br><br> ![Applicazioni][2]<br>
4. Fare clic su **Aggiungi** nella parte inferiore della pagina.<br><br> ![Applicazioni][3]<br>
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.<br><br> ![Applicazioni][4]<br>
6. Nella casella di ricerca digitare **HackerOne**.<br><br> ![Creazione di un utente test di Azure AD](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_01.png)<br>
7. Nel riquadro dei risultati selezionare **HackerOne** e quindi fare clic su **Completa** per aggiungere l'applicazione.<br><br>


##  Configurazione e test dell'accesso Single Sign-On di Azure AD
Successivamente, vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD con HackerOne con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di HackerOne che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in HackerOne.<br> La relazione di collegamento viene stabilita assegnando al valore di **nome utente** in Azure AD lo stesso valore di **Username** in HackerOne.

Per configurare e testare l'accesso Single Sign-On di Azure AD con HackerOne, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di HackerOne](#creating-a-hackerone-test-user)**: per avere una controparte di Britta Simon in HackerOne collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### Configurazione dell'accesso Single Sign-On di Azure AD

Successivamente, viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure classico e viene configurato l'accesso Single Sign-On nell'applicazione HackerOne.

**Per configurare l'accesso Single Sign-On di Azure AD con HackerOne, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **HackerOne** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**. <br><br> ![Configura accesso Single Sign-On][6] <br>

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a HackerOne** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**. <br><br> ![Configura accesso Single Sign-On](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_03.png) <br>

3. Nella pagina **Configurare le impostazioni dell'app** effettuare i passaggi seguenti e quindi fare clic su **Avanti**: <br><br>![Configura accesso Single Sign-On](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_04.png) <br>


    a. Nella casella di testo URL di accesso digitare l'URL usato dagli utenti per accedere all'applicazione HackerOne adottando il modello seguente: **"https://hackerone.com/nomesocietà/authentication"**. Quando si fa riferimento a un nome generico, **nomesocietà** deve essere sostituito da un nome effettivo.<br>

	b. Nella casella di testo IDENTIFICATORE digitare l'URL del tenant. Contattare il team di supporto di HackerOne all'indirizzo support@hackerone.com per ottenere l'URL del tenant.

	c. Fare clic su **Avanti**


4. Nella pagina **Configura accesso Single Sign-On in HackerOne** effettuare i passaggi seguenti e quindi fare clic su **Avanti**: <br><br>![Configura accesso Single Sign-On](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_05.png) <br>

    a. Fare clic su **Scarica certificato** e quindi salvare il file nel computer.

    b. Fare clic su **Avanti**.


5. Per configurare l'accesso Single Sign-On per l'applicazione, è necessario accedere al tenant di HackerOne con l'utente amministratore.
	
	a. Passare al dashboard e fare clic su **Settings** nell'angolo superiore destro della pagina.<br><br>![Configura accesso Single Sign-On](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_001.png) <br>

	b. Quindi passare a "**Authentication**"e fare clic sul pulsante "**Add SAML settings**".<br><br>![Configura accesso Single Sign-On](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_003.png) <br>

	c. Compilare il modulo **SAML Settings**.<br><br>![Configura accesso Single Sign-On](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_004.png) <br><br> c1. Immettere un dominio registrato nella casella di testo **Email Domain**. Per registrare il dominio in HackerOne contattare support@hackerone.com.<br><br> c2. Copiare l'URL del servizio Single Sign-On di Azure AD in **Single Sign On URL** in HackerOne.<br><br>![Configura accesso Single Sign-On](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_006.png) <br> c3. Convertire il certificato scaricato in file Base64 e aprirlo in Blocco note, copiare e incollare nella casella di testo **X509 Certificate** di HackerOne.<br>

	d. Fare clic sul pulsante **Save**.<br><br> e. Fare clic sul pulsante **Run test** e verificare il funzionamento del test. Se il test viene eseguito correttamente, richiedere la verifica delle proprie impostazioni SAML al team di supporto di HackerOne all'indirizzo support@hackerone.com che le approverà.<br><br>![Configura accesso Single Sign-On](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_005.png) <br><br>


6. Nel portale di Azure classico, selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**. <br><br>![Accesso Single Sign-On di Azure AD][10]<br>

7. Nella pagina **Conferma Single Sign-On** fare clic su **Completa**. <br>![Accesso Single Sign-On di Azure AD][11]




### Creazione di un utente test di Azure AD
Successivamente, viene creato un utente test chiamato Britta Simon nel portale classico.<br> Nell'elenco di utenti selezionare **Britta Simon**.<br><br>![Creare un utente di Azure AD][20]<br>

**Per creare un utente test di SECURE DELIVER in Azure AD, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro. <br><br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_09.png) <br>

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per visualizzare l'elenco di utenti, fare clic su **Utenti** nel menu in alto. <br><br> ![Creazione di un utente test di Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_03.png) <br>

4. Per aprire la finestra di dialogo **Aggiungi utente**, nella barra degli strumenti in basso fare clic su **Aggiungi utente**. <br><br> ![Creazione di un utente test di Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_04.png) <br>

5. Nella pagina della finestra di dialogo **Informazioni sull'utente** seguire questa procedura: <br><br> ![Creazione di un utente test di Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_05.png) <br>

    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.

    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina della finestra di dialogo **Profilo utente** seguire questa procedura: <br><br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_06.png) <br>

    a. Nella casella di testo **Nome** digitare **Britta**.

    b. Nella casella di testo **Cognome** digitare **Simon**.

    c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.

    d. Nell'elenco **Ruolo**, selezionare **Utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina della finestra di dialogo **Ottieni password temporanea**, fare clic su **crea**. <br><br> ![Creazione di un utente test di Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_07.png) <br>

8. Nella pagina **Ottieni password temporanea** seguire questa procedura: <br><br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_08.png) <br>

    a. Prendere nota del valore visualizzato in **Nuova password**.

    b. Fare clic su **Complete**.


### Creazione di un utente test di HackerOne

Successivamente, viene creato un utente chiamato Britta Simon in HackerOne. HackerOne supporta il provisioning JIT (Just-In-Time) che è abilitato per impostazione predefinita.

Non è necessario alcun intervento dell'utente in questa sezione. Quando si accede a HackerOne, viene creato un nuovo utente se non esiste ancora. [Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on).

> [AZURE.NOTE] Per creare un utente manualmente, è necessario contattare il team di supporto di HackerOne.




### Assegnazione dell'utente test di Azure AD

Successivamente, Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a HackerOne. <br><br>![Assegna utente][200] <br>

**Per assegnare Britta Simon a HackerOne, seguire questa procedura:**

1. Per aprire la visualizzazione applicazioni nel portale di Azure classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto. <br><br>![Assegna utente][201] <br>

2. Nell'elenco di applicazioni selezionare **HackerOne**. <br><br>![Configura accesso Single Sign-On](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_50.png) <br>

1. Nel menu in alto, fare clic su **Utenti**. <br><br>![Assegna utente][203] <br>

1. Nell'elenco di utenti selezionare **Britta Simon**.

2. Fare clic su **Assegna** sulla barra degli strumenti in basso. <br><br>![Assegna utente][205]



### Test dell'accesso Single Sign-On

Viene infine eseguito il test della configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.<br> Quando si fa clic sul riquadro HackerOne nel pannello di accesso, verrà eseguito automaticamente l'accesso all'applicazione HackerOne.


## Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0406_2016-->