<properties
	pageTitle="Esercitazione: Integrazione di Azure Active Directory con The Funding Portal | Microsoft Azure"
	description="Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e The Funding Portal."
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
	ms.date="09/02/2016"
	ms.author="jeedes"/> 


# Esercitazione: Integrazione di Azure Active Directory con The Funding Portal

Questa esercitazione descrive come integrare The Funding Portal con Azure Active Directory (Azure AD).

L'integrazione di The Funding Portal con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a The Funding Portal
- È possibile abilitare gli utenti per l'accesso automatico a The Funding Portal (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Prerequisiti

Per configurare l'integrazione di Azure AD con The Funding Portal, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di **The Funding Portal** abilitata per l'accesso Single Sign-On


> [AZURE.NOTE] Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).


## Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di The Funding Portal dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD


## Aggiunta di The Funding Portal dalla raccolta
Per configurare l'integrazione di The Funding Portal in Azure AD, è necessario aggiungere The Funding Portal dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere The Funding Portal dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.

	![Active Directory][1] 

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

	![Applicazioni][2] 

4. Fare clic su **Add** nella parte inferiore della pagina.

	![Applicazioni][3] 

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

	![Applicazioni][4] 

6. Nella casella di ricerca digitare **The Funding Portal**.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-thefundingportal-tutorial/tutorial_thefundingportal_01.png) 

7. Nel riquadro dei risultati selezionare **The Funding Portal** e quindi fare clic su **Completa** per aggiungere l'applicazione.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-thefundingportal-tutorial/tutorial_thefundingportal_02.png) 

##  Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con The Funding Portal con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di The Funding Portal che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in The Funding Portal. La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente) in The Funding Portal.

Per configurare e testare l'accesso Single Sign-On di Azure AD con The Funding Portal, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Creazione di un utente test di The Funding Portal](#creating-a-the-funding-portal-test-user)**: per avere una controparte di Britta Simon in The Funding Portal collegata alla relativa rappresentazione in Azure AD.
5. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### Configurazione dell'accesso Single Sign-On di Azure AD

Questa sezione descrive come abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure classico e configurare l'accesso Single Sign-On nell'applicazione The Funding Portal.

L'applicazione The Funding Portal prevede che le asserzioni SAML contengano un attributo denominato "externalId1". Il valore di "externalId1" deve essere un ID studente riconosciuto. Configurare l'attestazione "externalId1" per questa applicazione. È possibile gestire i valori di questi attributi dalla scheda **"Attributes"** (Attributi) dell'applicazione. La schermata seguente illustra un esempio relativo a questa operazione.

![Configura accesso Single Sign-On](./media/active-directory-saas-thefundingportal-tutorial/tutorial_thefundingportal_03.png) 

**Per configurare l'accesso Single Sign-On di Azure AD con The Funding Portal, seguire questa procedura:**

1. Nel menu visualizzato nella parte superiore della pagina di integrazione dell'applicazione **The Funding Portal** nel portale di Azure classico fare clic su **Attributi**.
     
    ![Configura accesso Single Sign-On][5] 

2. Nella finestra di dialogo relativa agli attributi del token SAML aggiungere l'attributo "externalId1".

	a. Fare clic su **aggiungi attributo utente** per aprire la finestra di dialogo **Aggiungi attributo utente**.
	
	![Configura accesso Single Sign-On](./media/active-directory-saas-thefundingportal-tutorial/tutorial_thefundingportal_05.png) 

	b. Nella casella di testo **Nome attributo** digitare il nome dell'attributo "externalId1".

	c. Nell'elenco **Valore attributo** selezionare l'attributo utente da usare per l'implementazione. Se il valore dell'ID studente è stato archiviato nell'attributo ExtensionAttribute1, ad esempio, selezionare user.extensionattribute1.

	d. Fare clic su **Complete**. Fare quindi clic su **Applica modifiche**.

1. Nel menu in alto fare clic su **Avvio rapido**.

	![Configura accesso Single Sign-On][6] 

2. Nella pagina di integrazione dell'applicazione **The Funding Portal** nel portale classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

	![Configura accesso Single Sign-On][7] 

3. Nella pagina **Stabilire come si desidera che gli utenti accedano a The Funding Portal** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
 	
	![Configura accesso Single Sign-On](./media/active-directory-saas-thefundingportal-tutorial/tutorial_thefundingportal_06.png) 

4. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:

	![Configura accesso Single Sign-On](./media/active-directory-saas-thefundingportal-tutorial/tutorial_thefundingportal_07.png)


    a. Nella casella di testo URL accesso digitare un URL corrispondente al modello seguente: `https://<subdomain>.regenteducation.net/`.

	b. Fare clic su **Next**.

5. Nella pagina **Configura accesso Single Sign-On in The Funding Portal** fare clic su **Scarica metadati** e quindi salvare il file nel computer.

	![Configura accesso Single Sign-On](./media/active-directory-saas-thefundingportal-tutorial/tutorial_thefundingportal_08.png) 

6. Al fine di configurare l'accesso SSO per l'applicazione, contattare il supporto di The Funding Portal. Il canale appropriato fornirà l'aiuto richiesto per configurare l'accesso SSO. Si noti che è necessario inviare un messaggio di posta elettronica all'indirizzo info@regenteducation.com e allegare il file di metadati scaricato.

7. Nel portale classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**.
	
	![Accesso Single Sign-On di Azure AD][10] 

8. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.
  	
	![Accesso Single Sign-On di Azure AD][11] 

### Creazione di un utente test di Azure AD
In questa sezione viene creato un utente test chiamato Britta Simon nel portale classico.

![Creare un utente di Azure AD][20] 

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
	
	![Creazione di un utente test di Azure AD](./media/active-directory-saas-thefundingportal-tutorial/create_aaduser_09.png) 

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per visualizzare l'elenco di utenti, fare clic su **Utenti** nel menu in alto.
	
	![Creazione di un utente test di Azure AD](./media/active-directory-saas-thefundingportal-tutorial/create_aaduser_03.png) 

4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-thefundingportal-tutorial/create_aaduser_04.png) 

5. Nella pagina della finestra di dialogo **Informazioni sull'utente** seguire questa procedura:
 
	![Creazione di un utente test di Azure AD](./media/active-directory-saas-thefundingportal-tutorial/create_aaduser_05.png)

    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.

    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.

    c. Fare clic su **Next**.

6.  Nella pagina della finestra di dialogo **Profilo utente** seguire questa procedura:

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-thefundingportal-tutorial/create_aaduser_06.png) 

    a. Nella casella di testo **Nome** digitare **Britta**.

    b. Nella casella di testo **Cognome** digitare **Simon**.

    c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.

    d. Nell'elenco **Ruolo**, selezionare **Utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-thefundingportal-tutorial/create_aaduser_07.png) 

8. Nella pagina **Ottieni password temporanea** seguire questa procedura:

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-thefundingportal-tutorial/create_aaduser_08.png) 

    a. Prendere nota del valore visualizzato in **Nuova password**.

    b. Fare clic su **Completa**.



### Creazione di un utente test di The Funding Portal

In questa sezione viene creato un utente chiamato Britta Simon in The Funding Portal. Se non si sa come aggiungere Britta Simon in The Funding Portal, collaborare con il team di supporto di The Funding Portal per aggiungere l'utente test e abilitare l'accesso SSO. A tale scopo, scrivere a info@regenteducation.com.

### Assegnazione dell'utente test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a The Funding Portal.

![Assegna utente][200] 

**Per assegnare Britta Simon a The Funding Portal, seguire questa procedura:**

1. Per aprire la visualizzazione delle applicazioni nel portale classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.

	![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **The Funding Portal**.

	![Configura accesso Single Sign-On](./media/active-directory-saas-thefundingportal-tutorial/tutorial_thefundingportal_09.png) 

1. Scegliere **Utenti** dal menu in alto.

	![Assegna utente][203] 

1. Nell'elenco Tutti gli utenti selezionare **Britta Simon**.

2. Fare clic su **Assegna** sulla barra degli strumenti in basso.

	![Assegna utente][205] 


### Test dell'accesso Single Sign-On

Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro The Funding Portal nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione The Funding Portal.

## Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references--> 

[1]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_06.png
[7]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0921_2016-->