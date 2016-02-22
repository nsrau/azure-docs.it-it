<properties
	pageTitle="Esercitazione: integrazione di Azure Active Directory con Moxtra | Microsoft Azure"
	description="Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Moxtra."
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="prasannas"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/10/2016"
	ms.author="jeedes"/>


# Esercitazione: integrazione di Azure Active Directory con Moxtra

Questa esercitazione illustra come integrare Moxtra con Azure Active Directory (Azure AD).<br>L'integrazione di Moxtra con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Moxtra 
- È possibile abilitare gli utenti per l'accesso automatico a Moxtra (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Prerequisiti 

Per configurare l'integrazione di Azure AD con Moxtra, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione Moxtra abilitata per l'accesso Single Sign-On


> [AZURE.NOTE] Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non è disponibile un ambiente di prova di Azure AD, è possibile ottenere una versione di prova di un mese [qui](https://azure.microsoft.com/pricing/free-trial/). 

 
## Descrizione dello scenario
L'obiettivo di questa esercitazione è testare l'accesso Single Sign-On di Azure AD in un ambiente di test. <br> Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Moxtra dalla raccolta 
2. Configurazione e test dell'accesso Single Sign-On di Azure AD


## Aggiunta di Moxtra dalla raccolta
Per configurare l'integrazione di Moxtra in Azure AD, è necessario aggiungere Moxtra dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Moxtra dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro. <br><br>![Active Directory][1] <br>

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.<br><br> ![Applicazioni][2]<br>
4. Fare clic su **Aggiungi** nella parte inferiore della pagina.<br><br> ![Applicazioni][3]<br>
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.<br><br> ![Applicazioni][4]<br>
6. Nella casella di ricerca, digitare **Moxtra**.<br><br> ![Creazione di un utente test di Azure AD](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_01.png)<br>
7. Nel riquadro dei risultati, selezionare **Moxtra** e quindi fare clic su **Completa** per aggiungere l'applicazione.<br><br> ![Creazione di un utente test di Azure AD](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_02.png)<br>

##  Configurazione e test dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come configurare e testare l'accesso Single Sign-On di Azure AD con Moxtra con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Moxtra che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Moxtra.<br> La relazione di collegamento viene stabilita assegnando al valore di **nome utente** in Azure AD lo stesso valore di **Username** in Moxtra.
 
Per configurare e testare l'accesso Single Sign-On di Azure AD con Moxtra, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Creazione di un utente test di Moxtra](#creating-a-moxtra-test-user)** - per avere una controparte di Britta Simon in Moxtra collegata alla relativa rappresentazione in Azure AD.
5. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### Configurazione dell'accesso Single Sign-On di Azure AD

Questa sezione descrive come abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure classico e configurare l'accesso Single Sign-On nell'applicazione Moxtra.

L'applicazione Moxtra prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. La schermata seguente mostra un esempio di questa operazione. <br><br> ![Configura accesso Single Sign-On](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_09.png) <br>



**Per configurare l'accesso Single Sign-On di Azure AD con Moxtra, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Moxtra** del portale di Azure classico, fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**. <br><br> ![Configura accesso Single Sign-On][6] <br>

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Moxtra** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**. <br><br> ![Configura accesso Single Sign-On](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_03.png) <br>

3. Nella pagina della finestra di dialogo **Configurare le impostazioni dell'app** seguire questa procedura: <br><br>![Configura accesso Single Sign-On](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_04.png) <br>

    a. Nella casella di testo **URL Sign On**, digitare il seguente URL: ****https://www.moxtra.com/service/#login**.

    b. Fare clic su **Avanti**.
 
 
4. Nella pagina **Configura accesso Single Sign-On in Moxtra** seguire questa procedura: <br><br>![Configura accesso Single Sign-On](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_05.png) <br>

    a. Fare clic su **Scarica certificato** e quindi salvare il file nel computer.

    b. Fare clic su **Avanti**.


1. In un'altra finestra del browser, accedere al sito aziendale di Moxtra come amministratore.

1. Nella barra degli strumenti a sinistra, fare clic su**Admin Console > SAML Single Sign-On** e quindi su **Nuovo**.<br><br>![Configura accesso Single Sign-On](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_06.png) <br>


1. Nella pagina **SAML**, eseguire i passaggi seguenti:<br><br>![Configura accesso Single Sign-On](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_08.png) <br>

    a. Nella casella di testo **Nome**, digitare un nome per la configurazione (ad esempio *SAML*).

    b. Nella pagina della finestra di dialogo **Configura accesso Single Sign-On in Moxtra** del portale di Azure classico copiare il valore di **ID entità** e incollarlo nella casella di testo **IdP Entity ID**.

    c. Nella pagina della finestra di dialogo **Configura accesso Single Sign-On in Moxtra** del portale di Azure classico, copiare il valore di **URL accesso remoto** e incollarlo nella casella di testo **Login URL**.

    d. Nella casella di testo **AuthnContextClassRef** digitare **urn:oasis:names:tc:SAML:2.0:ac:classes:Password**.

    e. Nella pagina della finestra di dialogo **Configura accesso Single Sign-On in Moxtra** nel portale di Azure classico, copiare il valore di **Formato identificatore nome** e quindi incollarlo nella casella di testo **NameID Format**.

    f. Aprire il certificato scaricato nel Blocco note, copiarne il contenuto e incollarlo nella casella di testo **Certificato**.

    g. Nella casella di testo dominio email SAML, digitare il dominio email SAML.
    > [AZURE.NOTE] Per verificare il dominio, fare clic su “**i**” di seguito.


    h. Fare clic su **Aggiorna**.


6. Nel portale di Azure classico, selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**. <br><br>![Accesso Single Sign-On di Azure AD][10]<br>

7. Nella pagina **Conferma Single Sign-On** fare clic su **Completa**. <br><br>![Accesso Single Sign-On di Azure AD][11]

1. Per aggiungere il mapping degli attributi personalizzato alla configurazione degli attributi token SAML, nel menu in alto fare clic su **Attributi** per aprire la finestra di dialogo **Attributi del token SAML**.<br><br>![Configura accesso Single Sign-On](./media/active-directory-saas-moxtra-tutorial/tutorial_general_80.png) <br>



1. Per ogni riga di dati nella tabella precedente, seguire questa procedura:

    | Nome attributo | Valore attributo |
    | ---            | ---             |
    | firstname | givenname |
    | lastname | surname |
    | idpid | *<valore di **ID entità** dalla pagina **Configura accesso Single Sign-On in Moxtra** nel portale di Azure classico>* |

 
    a. Fare clic su aggiungi attributo utente <br><br>![Configura accesso Single Sign-On](./media/active-directory-saas-moxtra-tutorial/tutorial_general_81.png) <br>

    b. Nella pagina **Aggiungi attributo utente**, digitare il nome e il valore dell’attributo mostrati per quella riga nella tabella.<br><br>![Configura accesso Single Sign-On](./media/active-directory-saas-moxtra-tutorial/tutorial_general_82.png) <br>

    c. Fare clic su **Complete**.



1. Fare clic su **Applica modifiche**. <br><br>![Configura accesso Single Sign-On](./media/active-directory-saas-moxtra-tutorial/tutorial_general_84.png) <br>








### Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di Azure classico.<br> Nell'elenco di utenti selezionare **Britta Simon**.<br><br>![Creare un utente di Azure AD][20]<br>

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro. <br><br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_09.png) <br> 

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per visualizzare l'elenco di utenti, fare clic su **Utenti** nel menu in alto. <br><br> ![Creazione di un utente test di Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_03.png) <br>
 
4. Per aprire la finestra di dialogo **Aggiungi utente**, nella barra degli strumenti in basso fare clic su **Aggiungi utente**. <br><br> ![Creazione di un utente test di Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_04.png) <br>

5. Nella pagina della finestra di dialogo **Informazioni sull'utente** seguire questa procedura: <br><br> ![Creazione di un utente test di Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_05.png) <br>

    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.

    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina della finestra di dialogo **Profilo utente** seguire questa procedura: <br><br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_06.png) <br>
 
    a. Nella casella di testo **Nome** digitare **Britta**.

    b. Nella casella di testo **Cognome** digitare **Simon**.

    c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.

    d. Nell'elenco **Ruolo** selezionare **Utente**. e. Fare clic su **Avanti**.

7. Nella pagina della finestra di dialogo **Ottieni password temporanea** fare clic su **crea**. <br><br> ![Creazione di un utente test di Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_07.png) <br>
 
8. Nella pagina **Ottieni password temporanea** seguire questa procedura: <br><br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_08.png) <br>
  
    a. Prendere nota del valore visualizzato in **Nuova password**.

    b. Fare clic su **Complete**.

  
 
### Creare un utente test Moxtra

Questa sezione descrive come creare un utente chiamato Britta Simon in Moxtra.

**Per creare un utente denominato Britta Simon in Moxtra, seguire questa procedura:**

1. Accedere al sito aziendale di Moxtra come amministratore.

1. Nella barra degli strumenti a sinistra, fare clic su**Admin Console > Gestione utente** e quindi su **Aggiungi utente**.<br><br>![Configura accesso Single Sign-On](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_10.png) <br>



1. Nella finestra di dialogo **Aggiungi utente** seguire questa procedura:

    a. Nella casella di testo **Nome** digitare **Britta**.

    b. Nella casella di testo **Cognome** digitare **Simon**.

    c. Nella casella di testo **Posta elettronica** digitare l'indirizzo di posta elettronica di Britta Simon nel portale di Azure classico.

    d. Nella casella di testo **Divisione**, digitare **Dev**.

    e. Nella casella di testo **Department**, digitare **IT**.

    f. Selezionare **Amministratore**.

    g. Fare clic su **Aggiungi**.





### Assegnazione dell'utente test di Azure AD

Questa sezione descrive come abilitare Britta Simon per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Moxtra. <br><br>![Assegna utente][200] <br>

**Per assegnare Britta Simon a Moxtra, seguire questa procedura:**

1. Per aprire la visualizzazione applicazioni nel portale di Azure classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto. <br><br>![Assegna utente][201] <br>

2. Nell'elenco di applicazioni, selezionare **Moxtra**. <br><br>![Configura accesso Single Sign-On](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_50.png) <br>

1. Nel menu in alto fare, clic su **Utenti**. <br><br>![Assegna utente][203] <br>

1. Nell'elenco di utenti selezionare **Britta Simon**.

2. Fare clic su **Assegna** sulla barra degli strumenti in basso. <br><br>![Assegna utente][205]



### Test dell'accesso Single Sign-On

Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.<br> Quando si fa clic sul riquadro Moxtra nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Moxtra.


## Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0211_2016-->