<properties
	pageTitle="Esercitazione: Integrazione di Azure Active Directory con AppBlade | Microsoft Azure"
	description="Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e AppBlade."
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
	ms.date="12/14/2015"
	ms.author="jeedes"/>


# Esercitazione: Integrazione di Azure Active Directory con AppBlade

L'obiettivo di questa esercitazione è illustrare come integrare AppBlade con Azure Active Directory (Azure AD).<br>L'integrazione di AppBlade con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere ad AppBlade
- È possibile abilitare gli utenti per l'accesso automatico ad AppBlade (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Prerequisiti

Per configurare l'integrazione di Azure AD con AppBlade, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di AppBlade abilitata per l'accesso Single Sign-On


> [AZURE.NOTE]Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).


## Descrizione dello scenario
L'obiettivo di questa esercitazione è testare l'accesso Single Sign-On di Azure AD in un ambiente di test. <br> Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di AppBlade dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD


## Aggiunta di AppBlade dalla raccolta
Per configurare l'integrazione di AppBlade in Azure AD, è necessario aggiungere AppBlade dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere AppBlade dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro. <br><br>![Active Directory][1] <br>

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.<br><br> ![Applicazioni][2]<br>
4. Fare clic su **Aggiungi** nella parte inferiore della pagina.<br><br> ![Applicazioni][3]<br>
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.<br><br> ![Applicazioni][4]<br>
6. Nella casella di ricerca digitare **AppBlade**.<br><br> ![Creazione di un utente test di Azure AD](./media/active-directory-saas-appblade-tutorial/tutorial_appblade_01.png)<br>
7. Nel riquadro dei risultati selezionare **AppBlade** e quindi fare clic su **Completa** per aggiungere l'applicazione. <br><br>

##  Configurazione e test dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come configurare e testare l'accesso Single Sign-On di Azure AD con AppBlade in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di AppBlade che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in AppBlade.<br> La relazione di collegamento viene stabilita assegnando al valore di **nome utente** in Azure AD lo stesso valore di **Username** in AppBlade.

Per configurare e testare l'accesso Single Sign-On di Azure AD con AppBlade, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Creazione di un utente test di AppBlade](#creating-a-appblade-test-user)**: per avere una controparte di Britta Simon in AppBlade collegata alla relativa rappresentazione in Azure AD.
5. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### Configurazione dell'accesso Single Sign-On di Azure AD

Questa sezione descrive come abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure classico e configurare l'accesso Single Sign-On nell'applicazione AppBlade.



**Per configurare Single Sign-On di Azure AD con AppBlade, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **AppBlade** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**. <br><br> ![Configura accesso Single Sign-On][6] <br>

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a AppBlade** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**. <br><br> ![Configura accesso Single Sign-On](./media/active-directory-saas-appblade-tutorial/tutorial_appblade_03.png) <br>

3. Nella pagina della finestra di dialogo **Configurare le impostazioni dell'app** seguire questa procedura: <br><br>![Configura accesso Single Sign-On](./media/active-directory-saas-appblade-tutorial/tutorial_appblade_04.png) <br>


    a. Nella casella di testo URL di accesso, digitare l'URL usato dagli utenti per accedere all'applicazione AppBlade adottando il modello seguente: **“https://companyname.appblade.com/saml/tenantid”**.

    b. Fare clic su **Avanti**.


4. Nella pagina **Configura accesso Single Sign-On in AppBlade** seguire questa procedura: <br><br>![Configura accesso Single Sign-On](./media/active-directory-saas-appblade-tutorial/tutorial_appblade_05.png) <br>

    a. Fare clic su **Scarica metadati** e quindi salvare il file nel computer.

    b. Fare clic su **Next**.


5. Per ottenere l'accesso Single Sign-On configurato per l'applicazione, contattare il team di supporto AppBlade all'indirizzo ****support@appblade.com** allegando al messaggio di posta elettronica il file di metadati scaricato. Richiedere inoltre al team di configurare il valore per **SSO Issuer URL** come ****https://appblade.com/saml**. Questa impostazione è necessaria per il corretto funzionamento dell'accesso Single Sign-On.


6. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**. <br><br>![Accesso Single Sign-On di Azure AD][10]<br>

7. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**. <br><br>![Accesso Single Sign-On di Azure AD][11]




### Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di Azure classico.<br> Nell'elenco di utenti selezionare **Britta Simon**.<br><br>![Creare un utente di Azure AD][20]<br>

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro. <br><br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-appblade-tutorial/create_aaduser_09.png) <br>

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per visualizzare l'elenco di utenti, nel menu in alto fare clic su **Utenti**. <br><br> ![Creazione di un utente test di Azure AD](./media/active-directory-saas-appblade-tutorial/create_aaduser_03.png) <br>

4. Per aprire la finestra di dialogo **Aggiungi utente**, nella barra degli strumenti in basso fare clic su **Aggiungi utente**. <br><br> ![Creazione di un utente test di Azure AD](./media/active-directory-saas-appblade-tutorial/create_aaduser_04.png) <br>

5. Nella pagina della finestra di dialogo **Informazioni sull'utente** seguire questa procedura: <br><br> ![Creazione di un utente test di Azure AD](./media/active-directory-saas-appblade-tutorial/create_aaduser_05.png) <br>

    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.

    b. Nella **casella di testo** Nome utente digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina della finestra di dialogo **Profilo utente** seguire questa procedura: <br><br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-appblade-tutorial/create_aaduser_06.png) <br>

    a. Nella casella di testo **Nome** digitare **Britta**.

    b. Nella casella di testo **Cognome** digitare **Simon**.

    c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.

    d. Nell'elenco **Ruolo** selezionare **Utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina della finestra di dialogo **Ottieni password temporanea** fare clic su **crea**. <br><br> ![Creazione di un utente test di Azure AD](./media/active-directory-saas-appblade-tutorial/create_aaduser_07.png) <br>

8. Nella pagina della finestra di dialogo **Ottieni password temporanea** seguire questa procedura: <br><br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-appblade-tutorial/create_aaduser_08.png) <br>

    a. Prendere nota del valore in **Nuova password**.

    b. Fare clic su **Operazione completata**.



### Creazione di un utente di test AppBlade

Questa sezione descrive come creare un utente chiamato Britta Simon in AppBlade. AppBlade supporta il provisioning just-in-time, che è abilitato per impostazione predefinita. **Assicurarsi che il nome di dominio sia configurato con AppBlade per il provisioning utente. Successivamente, funzionerà sono il provisioning utente just-in-time.**

Se l'utente ha un indirizzo di posta elettronica che termina con il dominio configurato da AppBlade per l'account, l'utente verrà aggiunto automaticamente all'account come membro con il livello di autorizzazione specificato, ovvero "Basic" (utente di base che può solo installare applicazioni), "Team Member" (utente che può caricare nuove versioni dell'app e gestire progetti) o "Administrator" (privilegi completi di amministratore per l'account). In genere si sceglie il livello Basic e quindi si alzano di livello manualmente gli utenti tramite un accesso amministrativo. AppBlade deve configurare in anticipo un accesso amministrativo basato su posta elettronica o deve alzare di livello un utente per conto del cliente dopo l'accesso.

Non è necessario alcun intervento dell'utente in questa sezione. Durante un tentativo di accesso ad AppBlade verrà creato un nuovo utente, se questo non esiste già. [Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on).

> [AZURE.NOTE]Per creare un utente manualmente, è necessario contattare il team di supporto di AppBlade.


### Assegnazione dell'utente test di Azure AD

Questa sezione descrive come abilitare Britta Simon per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a AppBlade. <br><br>![Assegna utente][200] <br>

**Per assegnare Britta Simon ad AppBlade, seguire questa procedura:**

1. Per aprire la visualizzazione applicazioni nel portale di Azure classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.<br><br>![Assegna utente][201] <br>

2. Nell'elenco di applicazioni selezionare **AppBlade**. <br><br>![Configura accesso Single Sign-On](./media/active-directory-saas-appblade-tutorial/tutorial_appblade_50.png) <br>

1. Nel menu in alto fare clic su **Utenti**. <br><br>![Assegna utente][203] <br>

1. Nell'elenco di utenti selezionare **Britta Simon**.

2. Fare clic su **Assegna** sulla barra degli strumenti in basso. <br><br>![Assegna utente][205]



### Test dell'accesso Single Sign-On

Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.<br> Quando si fa clic sul riquadro AppBlade nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione AppBlade.


## Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_1217_2015-->