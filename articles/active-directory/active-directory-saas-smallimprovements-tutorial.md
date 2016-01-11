<properties
	pageTitle="Esercitazione: Integrazione di Azure Active Directory con Small Improvements | Microsoft Azure"
	description="Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Small Improvements."
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


# Esercitazione: Integrazione di Azure Active Directory con Small Improvements

Questa esercitazione illustra come integrare Small Improvements con Azure Active Directory (Azure AD).<br>L'integrazione di Small Improvements con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Small Improvements
- È possibile abilitare gli utenti per l'accesso automatico a Small Improvements (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Prerequisiti

Per configurare l'integrazione di Azure AD con Small Improvements, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Small Improvements abilitata per l'accesso Single Sign-On


> [AZURE.NOTE]Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).


## Descrizione dello scenario
L'obiettivo di questa esercitazione è testare l'accesso Single Sign-On di Azure AD in un ambiente di test. <br> Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Small Improvements dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD


## Aggiunta di Small Improvements dalla raccolta
Per configurare l'integrazione di Small Improvements in Azure AD, è necessario aggiungere Small Improvements dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Small Improvements dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico**, fare clic su **Active Directory** nel riquadro di spostamento sinistro. <br><br> ![Active Directory][1]<br>

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.<br><br> ![Applicazioni][2]<br>
4. Fare clic su **Aggiungi** nella parte inferiore della pagina.<br><br> ![Applicazioni][3]<br>
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.<br><br> ![Applicazioni][4]<br>
6. Nella casella di ricerca digitare **Small Improvements**.<br><br> ![Creazione di un utente test di Azure AD](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_01.png)<br>
7. Nel riquadro dei risultati, selezionare **Small Improvements** e quindi fare clic su **Completa** per aggiungere l'applicazione. <br><br> ![Creazione di un utente test di Azure AD](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_02.png)<br>

##  Configurazione e test dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come configurare e testare l'accesso Single Sign-On di Azure AD con Small Improvements con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Small Improvements che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Small Improvements.<br> La relazione di collegamento viene stabilita assegnando al valore di **nome utente** in Azure AD lo stesso valore di **Username** in Small Improvements.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Small Improvements, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Creazione di un utente test di Small Improvements](#creating-a-small-improvements-test-user)**: per avere una controparte di Britta Simon in Small Improvements collegata alla relativa rappresentazione in Azure AD.
5. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### Configurazione dell'accesso Single Sign-On di Azure AD

Questa sezione descrive come abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure classico e configurare l'accesso Single Sign-On nell'applicazione Small Improvements.



**Per configurare l'accesso Single Sign-On di Azure AD con Small Improvements, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Small Improvements** del portale di Azure classico, fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**. <br><br> ![Configura accesso Single Sign-On][6] <br>

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Small Improvements**, selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**. <br><br> ![Configura accesso Single Sign-On](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_03.png) <br>

3. Nella pagina **Configurare le impostazioni dell'app**, seguire questa procedura: <br><br>![Configura accesso Single Sign-On](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_04.png) <br>

    > [AZURE.NOTE]Contattare il team di supporto di Small Improvements all'indirizzo [Support@small-improvements.com](mailto:support@small-improvements.com) per configurare un nome di dominio per l'account. Questa operazione è necessaria per il corretto funzionamento dell'accesso Single Sign-On.


    a. Nella casella di testo **URL di accesso**, digitare l'URL usato dagli utenti per accedere all'applicazione Small Improvements. b. Fare clic su **Avanti**.


4. Nella pagina **Configura accesso Single Sign-On in Small Improvements**, seguire questa procedura: <br><br>![Configura accesso Single Sign-On](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_05.png) <br>

    a. Fare clic su **Download certificato** e quindi salvare il file nel computer.

    b. Fare clic su **Avanti**.


1. In un'altra finestra del browser accedere al sito aziendale di Small Improvements come amministratore.

1. Dalla pagina dashboard principale, fare clic sul pulsante **Amministrazione** sulla sinistra. <br><br> ![Configura accesso Single Sign-On](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_06.png) <br>


1. Fare clic sul pulsante **Accesso Single Sign-On SAML** nella sezione **Integrazioni**. <br><br> ![Configura accesso Single Sign-On](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_07.png) <br>


1. Nella pagina di configurazione dell’accesso Single Sign-On, seguire questa procedura: <br><br>![Configura accesso Single Sign-On](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_08.png) <br>

    a. Nella pagina **Configura accesso Single Sign-On in Small Improvements** del portale di Azure classico, copiare il valore di **URL accesso Single Sign-On SAML** e incollarlo nella casella di testo **HTTP Endpoint**.

    b. Aprire il certificato scaricato nel Blocco note, copiarne il contenuto e incollarlo nella casella di testo **Certificato x509**.

    c. Se si vuole che l'opzione di autenticazione al modulo di accesso e accesso Single Sign-On sia disponibile per gli utenti, selezionare l'opzione **Abilitare l'accesso anche tramite account di accesso e password**.
  
	d. Immettere il valore appropriato per il nome del pulsante di accesso Single Sign-On nella casella di testo **Richiesta SAML**

	e. Fare clic su **Salva**.


6. Nel portale di Azure classico, selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**. <br><br>![Accesso Single Sign-On di Azure AD][10]<br>

7. Nella pagina **Conferma Single Sign-On**, fare clic su **Completa**. <br><br>![Accesso Single Sign-On di Azure AD][11]




### Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di Azure classico.<br> Nell'elenco di utenti selezionare **Britta Simon**.<br><br>![Creare un utente di Azure AD][20]<br>

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico**, fare clic su **Active Directory** nel riquadro di spostamento sinistro. <br><br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_09.png) <br>

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per visualizzare l'elenco di utenti, fare clic su **Utenti** nel menu in alto. <br><br> ![Creazione di un utente test di Azure AD](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_03.png) <br>

4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** sulla barra degli strumenti in basso. <br><br> ![Creazione di un utente test di Azure AD](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_04.png) <br>

5. Nella pagina **Informazioni sull'utente**, seguire questa procedura: <br><br> ![Creazione di un utente test di Azure AD](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_05.png) <br>

    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.

    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina **Profilo utente**, seguire questa procedura: <br><br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_06.png) <br>

    a. Nella casella di testo **Nome** digitare **Britta**.

    b. Nella casella di testo **Cognome** digitare **Simon**.

    c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.

    d. Nell'elenco **Ruolo** selezionare **Utente**. e. Fare clic su **Avanti**.

7. Nella pagina **Ottieni password temporanea**, fare clic su **Crea**. <br><br> ![Creazione di un utente test di Azure AD](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_07.png) <br>

8. Nella pagina **Ottieni password temporanea**, seguire questa procedura: <br><br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_08.png) <br>

    a. Prendere nota del valore visualizzato in **Nuova password**.

    b. Fare clic su **Complete**.



### Creazione di un utente test di Small Improvements

Questa sezione descrive come creare un utente di nome Britta Simon in Small Improvements. È già stato abilitato in [Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on).


**Per creare un utente di nome Britta Simon in Small Improvements, seguire questa procedura:**

1. Accedere al sito aziendale di Small Improvements come amministratore.

1. Dalla home page passare al menu sulla sinistra e fare clic su **Amministrazione**.

1. Fare clic sul pulsante **Directory utenti** nella sezione Gestione utenti. <br><br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_10.png) <br>

1. Fare clic su **Aggiungi membro del team**. <br><br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_11.png) <br>

1. Digitare i valori **Nome**, **Cognome** e **Indirizzo di posta elettronica** di un utente valido di cui si vuole effettuare il provisioning nelle caselle di testo corrispondenti. <br><br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_12.png) <br>

1. È inoltre possibile scegliere di immettere il messaggio personale nella casella di testo **Invia messaggio di notifica**. Se non si vuole inviare la notifica, deselezionare questa casella di controllo.

1. Fare clic su **Crea utenti**.

### Assegnazione dell'utente test di Azure AD

Questa sezione descrive come abilitare Britta Simon per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Small Improvements. <br><br>![Assegna utente][200] <br>

**Per assegnare Britta Simon a Small Improvements, seguire questa procedura:**

1. Per aprire la visualizzazione applicazioni nel portale di Azure classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.<br><br>![Assegna utente][201] <br>

2. Nell'elenco di applicazioni, selezionare **Small Improvements**. <br><br>![Configura accesso Single Sign-On](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_50.png) <br>

1. Nel menu in alto, fare clic su **Utenti**. <br><br>![Assegna utente][203] <br>

1. Nell'elenco di utenti selezionare **Britta Simon**.

2. Sulla barra degli strumenti in basso, fare clic su **Assegna**. <br><br>![Assegna utente][205]



### Test dell'accesso Single Sign-On

Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.<br> Quando si fa clic sul riquadro Small Improvements nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Small Improvements.


## Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_1223_2015-->