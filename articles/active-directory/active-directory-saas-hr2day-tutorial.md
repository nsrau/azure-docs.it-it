<properties
	pageTitle="Esercitazione: Integrazione di Azure Active Directory con HR2day by Merces | Microsoft Azure"
	description="Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e HR2day by Merces."
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
	ms.date="06/06/2016"
	ms.author="jeedes"/>


# Esercitazione: Integrazione di Azure Active Directory con HR2day by Merces

L'obiettivo di questa esercitazione è descrivere l'integrazione di HR2day by Merces con Azure Active Directory (Azure AD). L'integrazione di HR2day by Merces con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a HR2day by Merces.
- È possibile abilitare gli utenti per l'accesso automatico a HR2day by Merces (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Prerequisiti

Per configurare l'integrazione di Azure AD con HR2day by Merces, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di HR2day by Merces abilitata per l'accesso Single Sign-On


> [AZURE.NOTE] Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non è disponibile un ambiente di prova di Azure AD, è possibile ottenere una versione di prova di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).


## Descrizione dello scenario
L'obiettivo di questa esercitazione è testare l'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di HR2day by Merces dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD


## Aggiunta di HR2day by Merces dalla raccolta
Per configurare l'integrazione di HR2day by Merces in Azure AD, è necessario aggiungere HR2day by Merces dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere HR2day by Merces dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro. 

	![Active Directory][1]

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
 
	![Applicazioni][2]

4. Fare clic su **Add** nella parte inferiore della pagina.

	![Applicazioni][3]

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

	![Applicazioni][4]

6. Nella casella di ricerca, digitare **HR2day by Merces**.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_01.png)

7. Nel riquadro dei risultati selezionare **HR2day by Merces** e quindi fare clic su **Completa** per aggiungere l'applicazione.


##  Configurazione e test dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come configurare e testare l'accesso Single Sign-On di Azure AD con HR2day by Merces con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di HR2day by Merces che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in HR2day by Merces. La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** (nome utente) in HR2day by Merces.

Per configurare e testare l'accesso Single Sign-On di Azure AD con HR2day by Merces, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Creazione di un utente test di HR2day by Merces](#creating-a-hr2day-by-merces-test-user)**: per avere una controparte di Britta Simon in HR2day by Merces collegata alla relativa rappresentazione in Azure AD.
5. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### Configurazione dell'accesso Single Sign-On di Azure AD

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a HR2day by Merces tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.

L'applicazione HR2day by Merces prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. La schermata seguente illustra un esempio relativo a questa operazione.

![Configura accesso Single Sign-On](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_00.png)

Prima di poter configurare l'asserzione SAML, è necessario contattare il team di supporto HR2day tramite [servicedesk@merces.nl](mailto:servicedesk@merces.nl) e richiede il valore di attributo dell'identificatore univoco per il tenant. Questo valore è necessario per completare i passaggi nella sezione successiva.


**Per configurare Single Sign-On di Azure AD con HR2day by Merces, seguire questa procedura:**

1. Nel menu visualizzato nella parte superiore della pagina di integrazione dell'applicazione **HR2day by Merces** del portale di Azure classico fare clic su **Attributi** per aprire la finestra di dialogo **Attributi token SAML**. 

	![Configura accesso Single Sign-On](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_06.png)

2. Per aggiungere i mapping di attributi obbligatori, seguire questa procedura:

	![Configura accesso Single Sign-On](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_07.png)


	a. Fare clic su **Aggiungi attributo utente**.

	b. Nella casella di testo **Nome attributo**, digitare **"ATTR\_LOGINCLAIM"**.

	c. Nell'elenco **Valore attributo** selezionare **Join()**.

	d. Nell'elenco **Stringa1**, selezionare **User.mail**.

	e. Nella casella di testo **Stringa2** digitare l'**identificatore univoco** fornito dal team HR2day.

	f. Nella casella di testo **Separatore** digitare **@**.

	g. Fare clic su **Complete**.

  
3. Fare clic su **Applica modifiche**.


1. Nel menu in alto fare clic su **Avvio rapido** per aprire la finestra di dialogo **Avvio rapido**.

	![Configura accesso Single Sign-On](./media/active-directory-saas-hr2day-tutorial/tutorial_general_08.png)



1. Fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

	![Configura accesso Single Sign-On][6]

2. Nella pagina **How would you like users to sign on to HR2day by Merces** (Stabilire come si desidera che gli utenti accedano a HR2day by Merces) selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.

	![Configura accesso Single Sign-On](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_03.png)

3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:

	![Configura accesso Single Sign-On](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_04.png)


    a. Nella casella di testo URL di accesso digitare l'URL usato dagli utenti per accedere all'applicazione HR2day by Merces usando il modello seguente: **"https://<nome tenant>.force.com/<nome istanza>"**.

    b. Fare clic su **Avanti**.

4. Nella pagina **Configure single sign-on at HR2day by Merces** (Configura accesso Single Sign-On in HR2day by Merces) seguire questa procedura:

	![Configura accesso Single Sign-On](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_05.png)

    a. Fare clic su **Scarica certificato** e quindi salvare il file nel computer.

    b. Fare clic su **Avanti**.


5. Per ottenere l'accesso Single Sign-On configurato per l'applicazione, contattare il team di supporto di HR2day by Merces all'indirizzo [servicedesk@merces.nl](emailTo:servicedesk@merces.nl) allegando al messaggio di posta elettronica il file del certificato scaricato. Inoltre fornire URL SSO SAML, URL di disconnessione e URL dell’autorità di certificazione in modo da configurarli per l'integrazione SSO.


> [AZURE.NOTE] Indicare al team Merces che questa integrazione richiede che l'ID entità sia impostato con questo modello **https://hr2day.force.com/INSTANCENAME**



6. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**.

	![Accesso Single Sign-On di Azure AD][10]

7. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.

	![Accesso Single Sign-On di Azure AD][11]



### Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di Azure classico.

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_09.png)

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per visualizzare l'elenco di utenti, fare clic su **Utenti** nel menu in alto.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_03.png)

4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** sulla barra degli strumenti in basso.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_04.png)

5. Nella pagina **Informazioni sull'utente** seguire questa procedura:

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_05.png)

    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.

    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina **Profilo utente** seguire questa procedura:

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_06.png)

    a. Nella casella di testo **Nome** digitare **Britta**.

    b. Nella casella di testo **Cognome** digitare **Simon**.

    c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.

    d. Nell'elenco **Ruolo**, selezionare **Utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_07.png)

8. Nella pagina **Ottieni password temporanea** seguire questa procedura:

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_08.png)

    a. Prendere nota del valore visualizzato in **Nuova password**.

    b. Fare clic su **Complete**.



### Creazione di un utente test HR2day Merces

L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon in HR2day by Merces. Collaborare con il team di supporto di HR2day by Merces per aggiungere utenti all'account HR2day.


> [AZURE.NOTE] Per creare un utente manualmente, è necessario contattare il team di supporto di HR2day by Merces.


### Assegnazione dell'utente test di Azure AD

Questa sezione descrive come abilitare Britta Simon per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a HR2day by Merces.

![Assegna utente][200]

**Per assegnare Britta Simon a HR2day by Merces, seguire questa procedura:**

1. Per aprire la visualizzazione applicazioni nel portale di Azure classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.

	![Assegna utente][201]

2. Nell'elenco delle applicazioni, selezionare **HR2day by Merces**.

	![Configura accesso Single Sign-On](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_50.png)

1. Scegliere **Utenti** dal menu in alto.

	![Assegna utente][203]

1. Nell'elenco di utenti selezionare **Britta Simon**.

2. Fare clic su **Assegna** sulla barra degli strumenti in basso.

	![Assegna utente][205]



### Test dell'accesso Single Sign-On

Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso. Quando si fa clic sul riquadro HR2day by Merces nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione HR2day by Merces.


## Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0608_2016-->