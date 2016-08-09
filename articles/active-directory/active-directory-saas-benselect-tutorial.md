<properties
	pageTitle="Esercitazione: Integrazione di Azure Active Directory con BenSelect | Microsoft Azure"
	description="Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e BenSelect."
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
	ms.date="07/14/2016"
	ms.author="jeedes"/>


# Esercitazione: Integrazione di Azure Active Directory con BenSelect

Questa esercitazione descrive come integrare BenSelect con Azure Active Directory (Azure AD).

L'integrazione di BenSelect con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a BenSelect
- È possibile abilitare gli utenti per l'accesso automatico a BenSelect (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Prerequisiti

Per configurare l'integrazione di Azure AD con BenSelect, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di BenSelect abilitata per l'accesso Single Sign-On.


> [AZURE.NOTE] Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non è disponibile un ambiente di prova di Azure AD, è possibile ottenere una versione di prova di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).


## Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di BenSelect dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD


## Aggiunta di BenSelect dalla raccolta
Per configurare l'integrazione di BenSelect in Azure AD, è necessario aggiungere BenSelect dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere BenSelect dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.

	![Active Directory][1]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

	![Applicazioni][2]

4. Fare clic su **Add** nella parte inferiore della pagina.

	![Applicazioni][3]

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

	![Applicazioni][4]

6. Nella casella di ricerca digitare **BenSelect**.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-benselect-tutorial/tutorial_benselect_01.png)

7. Nel riquadro dei risultati selezionare **BenSelect** e quindi fare clic su **Completa** per aggiungere l'applicazione.

	![Selezione dell'app nella raccolta](./media/active-directory-saas-benselect-tutorial/tutorial_benselect_001.png)

##  Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con BenSelect in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di BenSelect che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in BenSelect.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente) in BenSelect.

Per configurare e testare l'accesso Single Sign-On di Azure AD con BenSelect, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di BenSelect](#creating-a-benselect-test-user)**: per avere una controparte di Britta Simon in BenSelect collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### Configurazione dell'accesso Single Sign-On di Azure AD

Questa sezione descrive come abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure classico e configurare l'accesso Single Sign-On nell'applicazione BenSelect.

L'applicazione BenSelect prevede che le asserzioni SAML abbiano un formato specifico. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla scheda degli **attributi** dell'applicazione. La schermata seguente illustra un esempio relativo a questa operazione.

![Configura accesso Single Sign-On](./media/active-directory-saas-benselect-tutorial/tutorial_benselect_06.png)

**Per configurare l'accesso Single Sign-On di Azure AD con BenSelect, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **BenSelect** del portale di Azure classico fare clic su **Attributi** nel menu in alto.

	 ![Configura accesso Single Sign-On](./media/active-directory-saas-benselect-tutorial/tutorial_benselect_07.png)

2. Nella finestra di dialogo **Attributi token SAML**, per ciascuna riga della tabella seguire questa procedura:

	| Nome attributo | Valore attributo |
	| --- | --- |    
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier | extractmailprefix([userprincipalname]) |

	a. Fare clic su **aggiungi attributo utente** per aprile la finestra di dialogo **Aggiungi attributo a utente**.

	![Configura accesso Single Sign-On](./media/active-directory-saas-benselect-tutorial/tutorial_benselect_08.png)

	b. Nella casella di testo **Nome attributo**, digitare il nome dell'attributo indicato per quella riga.

	c. Nell'elenco **Valore attributo** digitare ExtractMailPrefix().

	d. Nell'elenco **Posta** digitare User.userprincipalname.
	
	e. Fare clic su **Completa**.

3. Nel menu in alto fare clic su **Avvio rapido**.

	![Configura accesso Single Sign-On](./media/active-directory-saas-benselect-tutorial/tutorial_benselect_09.png)

4. Nella pagina **Stabilire come si desidera che gli utenti accedano a BenSelect** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.

	![Configura accesso Single Sign-On](./media/active-directory-saas-benselect-tutorial/tutorial_benselect_03.png)

5. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:

	![Configura accesso Single Sign-On](./media/active-directory-saas-benselect-tutorial/tutorial_benselect_04.png)

    a. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://www.benselect.com/enroll/login.aspx?Path={<tenant name>}`
	
	b. Fare clic su **Avanti**.
 
6. Nella pagina **Configura accesso Single Sign-On in BenSelect** seguire questa procedura:

	![Configura accesso Single Sign-On](./media/active-directory-saas-benselect-tutorial/tutorial_benselect_05.png)

    a. Fare clic su **Scarica certificato** e quindi salvare il file nel computer.

    b. Fare clic su **Avanti**.


7. Per ottenere SSO configurato per l'applicazione, contattare il team di supporto di BenSelect all'indirizzo [support@selerix.com](mailto:support@selerix.com) e fornire gli elementi seguenti:

	- Certificato scaricato
	- URL SSO SAML
	- URL di disconnessione
	- Autorità di certificazione

	> [AZURE.NOTE] È necessario specificare che questa integrazione richiede l'algoritmo SHA256 perché sia possibile impostare SSO sul server corretto, ad esempio app2101 e così via. L'algoritmo SHA1 non è supportato.

8. Nel portale di Azure classico selezionare la conferma della configurazione e fare clic su **Avanti**.
	
	![Accesso Single Sign-On di Azure AD][10]

9. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.
 
	![Accesso Single Sign-On di Azure AD][11]


### Creazione di un utente test di Azure AD
In questa sezione viene creato un utente test chiamato Britta Simon nel portale classico.


![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-benselect-tutorial/create_aaduser_09.png)

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per visualizzare l'elenco di utenti, selezionare **Utenti** dal menu in alto.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-benselect-tutorial/create_aaduser_03.png)

4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** sulla barra degli strumenti in basso.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-benselect-tutorial/create_aaduser_04.png)

5. Nella pagina **Informazioni sull'utente** seguire questa procedura: ![Creazione di un utente test di Azure AD](./media/active-directory-saas-benselect-tutorial/create_aaduser_05.png)

    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.

    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina **Profilo utente** seguire questa procedura: ![Creazione di un utente test di Azure AD](./media/active-directory-saas-benselect-tutorial/create_aaduser_06.png)

    a. Nella casella di testo **Nome** digitare **Britta**.

    b. Nella casella di testo **Cognome** digitare **Simon**.

    c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.

    d. Nell'elenco **Ruolo**, selezionare **Utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-benselect-tutorial/create_aaduser_07.png)

8. Nella pagina **Ottieni password temporanea** seguire questa procedura:

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-benselect-tutorial/create_aaduser_08.png)

    a. Prendere nota del valore visualizzato in **Nuova password**.

    b. Fare clic su **Complete**.



### Creazione di un utente test di BenSelect

Questa sezione descrive come creare un utente chiamato Britta Simon in BenSelect. Collaborare con il team di supporto di BenSelect per aggiungere utenti all'account BenSelect.

> [AZURE.NOTE] Per creare un utente manualmente, è necessario contattare il team di supporto di BenSelect all'indirizzo <mailto:support@selerix.com>.


### Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a BenSelect.

![Assegna utente][200]

**Per assegnare Britta Simon a BenSelect, seguire questa procedura:**

1. Per aprire la visualizzazione delle applicazioni nel portale classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.

	![Assegna utente][201]

2. Nell'elenco delle applicazioni selezionare **BenSelect**.

	![Configura accesso Single Sign-On](./media/active-directory-saas-benselect-tutorial/tutorial_benselect_50.png)

3. Scegliere **Utenti** dal menu in alto.

	![Assegna utente][203]

4. Nell'elenco di utenti selezionare **Britta Simon**.

5. Fare clic su **Assegna** sulla barra degli strumenti in basso.

	![Assegna utente][205]


### Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro BenSelect nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione BenSelect.


## Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0720_2016-->
