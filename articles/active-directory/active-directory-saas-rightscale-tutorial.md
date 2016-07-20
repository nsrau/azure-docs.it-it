<properties
	pageTitle="Esercitazione: Integrazione di Azure Active Directory con RightScale | Microsoft Azure"
	description="Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e RightScale."
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
	ms.date="06/29/2016"
	ms.author="jeedes"/>


# Esercitazione: Integrazione di Azure Active Directory con RightScale

Questa esercitazione descrive l'integrazione di RightScale con Azure Active Directory (Azure AD). L'integrazione di RightScale con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a RightScale
- È possibile abilitare gli utenti per l'accesso automatico a RightScale (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Prerequisiti

Per configurare l'integrazione di Azure AD con RightScale, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di RightScale abilitata per l'accesso Single Sign-On


> [AZURE.NOTE] Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).


## Descrizione dello scenario
L'obiettivo di questa esercitazione è testare l'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di RightScale dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD


## Aggiunta di RightScale dalla raccolta
Per configurare l'integrazione di RightScale in Azure AD, è necessario aggiungere RightScale dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere RightScale dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.

	![Active Directory][1]

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

	![Applicazioni][2]

4. Fare clic su **Add** nella parte inferiore della pagina.

	![Applicazioni][3]

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
 
	![Applicazioni][4]

6. Nella casella di ricerca digitare **RightScale**.
 
	![Creazione di un utente test di Azure AD](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_01.png)

7. Nel riquadro dei risultati selezionare **RightScale** e quindi fare clic su **Completa** per aggiungere l'applicazione.

	![Selezione dell'app nella raccolta](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_02.png)

##  Configurazione e test dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come configurare e testare l'accesso Single Sign-On di Azure AD con RightScale in usando un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di RightScale che corrisponde a un utente di Azure AD. In altre parole deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in RightScale.


Per configurare e testare l'accesso Single Sign-On di Azure AD con RightScale, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Creazione di un utente di test di RightScale](#creating-a-rightscale-test-user)**: per avere una controparte di Britta Simon in RightScale collegata alla relativa rappresentazione in Azure AD.
5. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### Configurazione dell'accesso Single Sign-On di Azure AD

Questa sezione descrive come abilitare l'accesso Single Sign-On di Azure AD nel portale classico e configurare l'accesso Single Sign-On nell'applicazione RightScale.



**Per configurare l'accesso Single Sign-On di Azure AD con RightScale, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **RightScale** del portale classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

	![Configura accesso Single Sign-On][6]

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a RightScale** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.

	![Configura accesso Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_03.png)

3. Nella pagina della finestra di dialogo **Configurare le impostazioni dell'app**, se si desidera configurare l'applicazione in **modalità iniziata da IDP**, seguire la procedura seguente e fare clic su **Avanti**:

	![Configura accesso Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_04.png)


    a. Nella casella di testo URL di risposta digitare l'URL nel formato seguente: `https://login.rightscale.com/login/saml2/consume`

	b. Fare clic su **Avanti**

4. Se si desidera configurare l'applicazione in **modalità avviata da SP** nella pagina **Configurare le impostazioni dell'app**, fare clic su **Mostra opzioni avanzate (facoltativo)**, immettere l'**URL di accesso** e fare clic su **Avanti**.
 
	![Configura accesso Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_06.png)

	a. Nella casella di testo URL di accesso digitare l'URL usato dagli utenti per accedere all'applicazione RightScale nel formato seguente: `https://login.rightscale.com/`

	b. Fare clic su **Avanti**

5. Nella pagina **Configura accesso Single Sign-On in RightScale** seguire la procedura seguente e fare clic su **Avanti**:

	![Configura accesso Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_05.png)

    a. Fare clic su **Scarica certificato** e quindi salvare il file del certificato con codifica Base 64 nel computer.

    b. Fare clic su **Avanti**.


5. Per configurare l'accesso SSO per l'applicazione, è necessario accedere al tenant di RightScale come amministratore.

	a. Nel menu in alto fare clic sulla scheda **Impostazioni** e selezionare **Single Sign-On**.

	![Configura accesso Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_001.png)

	b. Fare clic sul pulsante "**nuovo**" per aggiungere i **provider di identità SAML**.

	![Configura accesso Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_002.png)

	c. Nella casella **Nome visualizzato** digitare il nome dell'azienda.
	
	![Configura accesso Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_003.png)

	d. Selezionare **Allow RightScale-initiated SSO using a discovery hint** (Concenti accesso SSO avviato da RightScale mediante un hint di individuazione) e digitare il **nome di dominio** nella casella di testo sottostante.

	![Configura accesso Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_004.png)

	e. Copiare l'URL SSO SAML da Azure AD in **SAML SSO Endpoint** (Endpoint SSO SAML) in RightScale.

	![Configura accesso Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_005.png)
	
	![Configura accesso Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_006.png)

	f. Copiare l'ID entità da Azure AD a **SAML EntityID** (ID entità SAML) in RightScale.
	
	![Configura accesso Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_007.png)
	
	![Configura accesso Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_008.png)

	g. Fare clic sul pulsante **Sfoglia** per caricare il certificato scaricato al passaggio 4.
	
	![Configura accesso Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_009.png)

	h. Fare clic su **Save**.
	


6. Nel portale classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**.

	![Accesso Single Sign-On di Azure AD][10]

7. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.
  
	![Accesso Single Sign-On di Azure AD][11]




### Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente di test chiamato Britta Simon nel portale classico.

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-rightscale-tutorial/create_aaduser_09.png)

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per visualizzare l'elenco di utenti, fare clic su **Utenti** nel menu in alto.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-rightscale-tutorial/create_aaduser_03.png)

4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** sulla barra degli strumenti in basso.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-rightscale-tutorial/create_aaduser_04.png)

5. Nella pagina **Informazioni sull'utente** seguire questa procedura:

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-rightscale-tutorial/create_aaduser_05.png)

    a. In **Tipo di utente** selezionare **Nuovo utente nell'organizzazione**.

    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina della finestra di dialogo **Profilo utente** seguire questa procedura:

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-rightscale-tutorial/create_aaduser_06.png)

    a. Nella casella di testo **Nome** digitare **Britta**.

    b. Nella casella di testo **Cognome** digitare **Simon**.

    c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.

    d. Nell'elenco **Ruolo**, selezionare **Utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina della finestra di dialogo **Ottieni password temporanea** fare clic su **crea**.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-rightscale-tutorial/create_aaduser_07.png)

8. Nella pagina della finestra di dialogo **Ottieni password temporanea** seguire questa procedura:

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-rightscale-tutorial/create_aaduser_08.png)

    a. Prendere nota del valore visualizzato in **Nuova password**.

    b. Fare clic su **Complete**.



### Creazione di un utente di test di RightScale

In questa sezione viene creato un utente chiamato Britta Simon in RightScale. Rivolgersi al team di supporto di RightScale mediante support@rightscale.com per aggiungere gli utenti nella piattaforma RightScale.


### Assegnazione dell'utente test di Azure AD

Questa sezione descrive come abilitare Britta Simon per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a RightScale.

![Assegna utente][200]

**Per assegnare Britta Simon a RightScale, seguire questa procedura:**

1. Per aprire la visualizzazione delle applicazioni nel portale classico, nella visualizzazione Directory fare clic su **Applicazioni** nel menu in alto.

	![Assegna utente][201]

2. Nell'elenco di applicazioni selezionare **RightScale**.

	![Configura accesso Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_50.png)

1. Scegliere **Utenti** dal menu in alto.

	![Assegna utente][203]

1. Nell'elenco di utenti selezionare **Britta Simon**.

2. Fare clic su **Assegna** sulla barra degli strumenti in basso.

	![Assegna utente][205]



### Test dell'accesso Single Sign-On

Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso. Quando si fa clic sul riquadro RightScale nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione RightScale.
	

## Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0706_2016-->