<properties
	pageTitle="Esercitazione: Integrazione di Azure Active Directory con eTouches | Microsoft Azure"
	description="Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e eTouches."
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
	ms.date="08/03/2016"
	ms.author="jeedes"/>


# Esercitazione: Integrazione di Azure Active Directory con eTouches

Questa esercitazione descrive come integrare eTouches con Azure Active Directory (Azure AD).

L'integrazione di eTouches con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a eTouches.
- È possibile abilitare gli utenti per l'accesso automatico a eTouches (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Prerequisiti

Per configurare l'integrazione di Azure AD con eTouches, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di eTouches abilitata per l'accesso Single Sign-On


> [AZURE.NOTE] Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).


## Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di eTouches dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD


## Aggiunta di eTouches dalla raccolta
Per configurare l'integrazione di eTouches in Azure AD, è necessario aggiungere eTouches dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere eTouches dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.

	![Active Directory][1]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

	![Applicazioni][2]

4. Fare clic su **Add** nella parte inferiore della pagina.

	![Applicazioni][3]

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

	![Applicazioni][4]

6. Nella casella di ricerca digitare **eTouches**.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_01.png)

7. Nel riquadro dei risultati selezionare **eTouches** e quindi fare clic su **Completa** per aggiungere l'applicazione.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_02.png)

##  Configurazione e test dell'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con eTouches usando un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente controparte di eTouches che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in eTouches.

La relazione di collegamento viene stabilita assegnando al valore di **nome utente** in Azure AD lo stesso valore di **Username** (Nome utente) in eTouches.

Per configurare e testare l'accesso Single Sign-On di Azure AD con eTouches, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di eTouches](#creating-a-predictix-price-reporting-test-user)**: per avere una controparte di Britta Simon in eTouches collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale classico e viene configurato l'accesso Single Sign-On nell'applicazione eTouches.

L'applicazione eTouches prevede che le asserzioni SAML abbiano un formato specifico. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla scheda **Attribute (Attributo)** dell'applicazione. La schermata seguente illustra un esempio relativo a questa operazione.

![Configura accesso Single Sign-On](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_07.png)

**Per configurare Single Sign-On di Azure AD con eTouches, seguire questa procedura:**


1. Nel menu visualizzato nella parte superiore della pagina di integrazione dell'applicazione **eTouches** del portale di Azure classico fare clic su **Attributi**.

	![Configura accesso Single Sign-On](./media/active-directory-saas-etouches-tutorial/tutorial_general_80.png)


2. Nella finestra di dialogo **Attributi token SAML**, per ciascuna riga della tabella seguire questa procedura:

	| Nome attributo | Valore attributo |
	| --- | --- |    
	| Email | user.mail |

	a. Fare clic su **aggiungi attributo utente** per aprire la finestra di dialogo **Aggiungi attributo utente**.

	![Configura accesso Single Sign-On](./media/active-directory-saas-etouches-tutorial/tutorial_general_81.png)


	b. Nella casella di testo **Nome attributo** digitare il nome dell'attributo indicato per quella riga.

    c. Nell'elenco **Valore attributo** selezionare il valore dell'attributo indicato per quella riga.

    d. Fare clic su **Complete**.
	

3. Nella pagina di integrazione dell'applicazione **eTouches** del portale di Azure classico, fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
	 
	![Configura accesso Single Sign-On][6]

4. Nella pagina **Stabilire come si desidera che gli utenti accedano a eTouches**, selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.

	![Configura accesso Single Sign-On](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_03.png)

5. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:

	![Configura accesso Single Sign-On](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_04.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usato dagli utenti per accedere all'applicazione eTouches adottando il modello seguente: **https://www.eiseverywhere.com/saml/accounts/?sso&accountid=\<IDaccount>**.
	
	b. Fare clic su **Avanti**
 
6. Nella pagina **Configura accesso Single Sign-On in eTouches** seguire questa procedura:

	![Configura accesso Single Sign-On](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_05.png)

    a. Fare clic su **Scarica metadati** e quindi salvare il file nel computer.

    b. Fare clic su **Avanti**.


7. Per ottenere l'accesso Single Sign-On configurato per l'applicazione, seguire questa procedura nell'applicazione eTouches:

	a. Accedere all'applicazione **eTouches** usando i diritti di amministratore.
	
	b. Passare alla pagina di configurazione per **SAML**.

	c. Nella sezione **General Settings** (Impostazioni generali) incollare i contenuti dei metadati della federazione di Azure AD nella casella di testo.

	d. Fare clic sul pulsante **Save & Stay** (Salva e rimani).

	e. Fare clic sul pulsante **Update Metadata** (Aggiorna metadati) nella sezione relativa ai metadati SAML.

	f. Verrà visualizzata la pagina e verrà effettuato l'accesso Single Sign-On. Quando l'accesso Single Sign-On è operativo, è possibile configurare il nome utente.

	g. Nel campo **Username** (Nome utente) selezionare **emailaddress**, come mostrato nell'immagine seguente.

	h. Copiare il valore di **SSO URL/ACS** (URL SSO/ACS) e inserirlo nella casella di testo URL di accesso nella procedura di configurazione guidata dell'applicazione di Azure AD.

	![Configura accesso Single Sign-On](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_06.png)

8. Nel portale di Azure classico selezionare la conferma della configurazione e quindi fare clic su **Avanti**.
	
	![Accesso Single Sign-On di Azure AD][10]

9. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.
	
 
	![Accesso Single Sign-On di Azure AD][11]


### Creazione di un utente test di Azure AD
In questa sezione viene creato un utente test chiamato Britta Simon nel portale classico.


![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-etouches-tutorial/create_aaduser_09.png)

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per visualizzare l'elenco di utenti, fare clic su **Utenti** nel menu in alto.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-etouches-tutorial/create_aaduser_03.png)

4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** sulla barra degli strumenti in basso.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-etouches-tutorial/create_aaduser_04.png)

5. Nella pagina **Informazioni sull'utente** seguire questa procedura: ![Creazione di un utente test di Azure AD](./media/active-directory-saas-etouches-tutorial/create_aaduser_05.png)

    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.

    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina **Profilo utente** seguire questa procedura: ![Creazione di un utente test di Azure AD](./media/active-directory-saas-etouches-tutorial/create_aaduser_06.png)

    a. Nella casella di testo **Nome** digitare **Britta**.

    b. Nella casella di testo **Cognome** digitare **Simon**.

    c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.

    d. Nell'elenco **Ruolo**, selezionare **Utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-etouches-tutorial/create_aaduser_07.png)

8. Nella pagina **Ottieni password temporanea** seguire questa procedura:

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-etouches-tutorial/create_aaduser_08.png)

    a. Prendere nota del valore visualizzato in **Nuova password**.

    b. Fare clic su **Complete**.



### Creazione di un utente test eTouches

In questa sezione viene creato un utente chiamato Britta Simon in eTouches. Collaborare con il team di supporto di eTouches per aggiungere gli utenti alla piattaforma eTouches.


### Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a eTouches.

![Assegna utente][200]

**Per assegnare Britta Simon a eTouches, seguire questa procedura:**

1. Per aprire la visualizzazione delle applicazioni nel portale classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.

	![Assegna utente][201]

2. Nell'elenco delle applicazioni, selezionare **eTouches**.

	![Configura accesso Single Sign-On](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_50.png)

3. Scegliere **Utenti** dal menu in alto.

	![Assegna utente][203]

4. Nell'elenco di utenti selezionare **Britta Simon**.

5. Fare clic su **Assegna** sulla barra degli strumenti in basso.

	![Assegna utente][205]


### Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro eTouches nel pannello di accesso, si accederà automaticamente all'applicazione eTouches.


## Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0803_2016-->