<properties
	pageTitle="Esercitazione: Integrazione di Azure Active Directory con People | Microsoft Azure"
	description="Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e People."
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
	ms.date="07/11/2016"
	ms.author="jeedes"/>


# Esercitazione: Integrazione di Azure Active Directory con People

Questa esercitazione descrive l'integrazione di People con Azure Active Directory (Azure AD).

L'integrazione di People con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a People
- È possibile abilitare gli utenti per l'accesso automatico a People (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Prerequisiti

Per configurare l'integrazione di Azure AD con People, sono necessari gli elementi seguenti:

- Una sottoscrizione di Azure.
- Sottoscrizione di People abilitata per l'accesso Single Sign-On


> [AZURE.NOTE] Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non è disponibile un ambiente di prova di Azure AD, è possibile ottenere una versione di prova di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).


## Descrizione dello scenario
L'obiettivo di questa esercitazione è testare l'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di People dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD


## Aggiunta di People dalla raccolta
Per configurare l'integrazione di People in Azure AD, è necessario aggiungere People dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere People dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
 
	![Active Directory][1]

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

	![Applicazioni][2]

4. Fare clic su **Add** nella parte inferiore della pagina.

	![Applicazioni][3]

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

	![Applicazioni][4]

6. Nella casella di ricerca digitare **People**.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-people-tutorial/tutorial_people_01.png)

7. Nel riquadro dei risultati selezionare **People** e quindi fare clic su **Completa** per aggiungere l'applicazione.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-people-tutorial/tutorial_people_02.png)

##  Configurazione e test dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come configurare e testare l'accesso Single Sign-On di Azure AD con People in base a un utente test di nome "Britta Simon".

Per configurare e testare l'accesso Single Sign-On di Azure AD con People, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di People](#creating-a-people-test-user)**: per avere una controparte di Britta Simon in People collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### Configurazione dell'accesso Single Sign-On di Azure AD

Questa sezione descrive come abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure classico e configurare l'accesso Single Sign-On nell'applicazione People.



**Per configurare l'accesso Single Sign-On di Azure AD con People, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **People** del portale di Azure classico, fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

	[Configura accesso Single Sign-On][6]

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a People** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.
 
	![Configura accesso Single Sign-On](./media/active-directory-saas-people-tutorial/tutorial_people_03.png)

3. Nella pagina della finestra di dialogo **Configurare le impostazioni dell'app** seguire questa procedura e quindi fare clic su **Avanti**:
 
	![Configura accesso Single Sign-On](./media/active-directory-saas-people-tutorial/tutorial_people_04.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usato dagli utenti per accedere all'applicazione People adottando il modello seguente: **"https://<company name>.peoplehr.com/"**.

    b. Se non si conosce l'URL del tenant, contattare il team di supporto di People all'indirizzo [customerservices@peoplehr.com](mailto:customerservices@peoplehr.com) per richiederlo.

	c. Nella casella di testo **Identificatore** digitare l'URL del tenant.

    d. Nella casella di testo **URL di risposta** digitare l'URL nel modello seguente: "**https://itgs.peoplehr.net/Pages/Saml/ConsumeAzureAD.aspx**".

	e. Fare clic su **Avanti**


4. Nella pagina **Configura accesso Single Sign-On in People** seguire questa procedura e quindi fare clic su **Avanti**:

	![Configura accesso Single Sign-On](./media/active-directory-saas-people-tutorial/tutorial_people_05.png)

    a. Fare clic su **Scarica metadati** e quindi salvare il file nel computer.

    b. Fare clic su **Avanti**.


5. Per configurare l'accesso SSO sull'applicazione, è necessario accedere al tenant di People come amministratore.
	
	a. Nella barra di spostamento sul lato sinistro fare clic su **Settings (Impostazioni)**.
    
	![Configura accesso Single Sign-On](./media/active-directory-saas-people-tutorial/tutorial_people_001.png)

	b. Fare clic su **"Company" (Azienda)**.
    
	![Configura accesso Single Sign-On](./media/active-directory-saas-people-tutorial/tutorial_people_002.png)

	c. In **"Upload 'Single Sign On' SAML meta-data file" (Carica il file dei metadati SAML "Single Sign-On")** fare clic su **Browse (Sfoglia)** per caricare il file di metadati scaricato.

	![Configura accesso Single Sign-On](./media/active-directory-saas-people-tutorial/tutorial_people_003.png)




6. Nel portale di Azure classico, selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**.
 
	![Accesso Single Sign-On di Azure AD][10]

7. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.

	![Accesso Single Sign-On di Azure AD][11]




### Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di Azure classico. Nell'elenco di utenti selezionare **Britta Simon**.

![Creare un utente di Azure AD][20]


**Per creare un utente test People in Azure AD, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-people-tutorial/create_aaduser_09.png)

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per visualizzare l'elenco di utenti, fare clic su **Utenti** nel menu in alto.
 
	![Creazione di un utente test di Azure AD](./media/active-directory-saas-people-tutorial/create_aaduser_03.png)

4. Per aprire la finestra di dialogo **Aggiungi utente**, nella barra degli strumenti in basso fare clic su **Aggiungi utente**.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-people-tutorial/create_aaduser_04.png)

5. Nella pagina della finestra di dialogo **Informazioni sull'utente** seguire questa procedura:
 
	![Creazione di un utente test di Azure AD](./media/active-directory-saas-people-tutorial/create_aaduser_05.png)

    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.

    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina della finestra di dialogo **Profilo utente** seguire questa procedura:

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-people-tutorial/create_aaduser_06.png)

    a. Nella casella di testo **Nome** digitare **Britta**.

    b. Nella casella di testo **Cognome** digitare **Simon**.

    c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.

    d. Nell'elenco **Ruolo**, selezionare **Utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina della finestra di dialogo **Ottieni password temporanea**, fare clic su **crea**.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-people-tutorial/create_aaduser_07.png)

8. Nella pagina della finestra di dialogo **Ottieni password temporanea** seguire questa procedura:

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-people-tutorial/create_aaduser_08.png)

    a. Prendere nota del valore visualizzato in **Nuova password**.

    b. Fare clic su **Complete**.



### Creazione di un utente test di People

Questa sezione descrive come creare un utente chiamato Britta Simon in People. People non supporta il provisioning just-in-time quindi è necessario contattare il team di supporto di People per creare manualmente un utente.




### Assegnazione dell'utente test di Azure AD

L'obiettivo di questa sezione consiste nell'abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a People.

![Assegna utente][200]

**Per assegnare Britta Simon a People, seguire questa procedura:**

1. Per aprire la visualizzazione applicazioni nel portale di Azure classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.

	![Assegna utente][201]

2. Nell'elenco delle applicazioni, selezionare **People**.

	![Configura accesso Single Sign-On](./media/active-directory-saas-people-tutorial/tutorial_people_50.png)

1. Scegliere **Utenti** dal menu in alto.

	![Assegna utente][203]

1. Nell'elenco di utenti selezionare **Britta Simon**.

2. Fare clic su **Assegna** nella barra degli strumenti in basso.

	![Assegna utente][205]



### Test dell'accesso Single Sign-On

Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso. Quando si fa clic sul riquadro People nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione People.


## Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-people-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-people-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-people-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-people-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-people-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-people-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-people-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-people-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-people-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-people-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-people-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-people-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-people-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0713_2016-->