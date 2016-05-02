<properties
	pageTitle="Esercitazione: Integrazione di Azure Active Directory con Keylight | Microsoft Azure"
	description="Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Keylight."
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
	ms.date="04/18/2016"
	ms.author="jeedes"/>


# Esercitazione: Integrazione di Azure Active Directory con Keylight

Questa esercitazione descrive come integrare Keylight con Azure Active Directory (Azure AD).

L'integrazione di Keylight con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Keylight.
- È possibile abilitare gli utenti per l'accesso automatico a Keylight (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in una posizione centrale, il portale di Azure Active Directory.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Prerequisiti

Per configurare l'integrazione di Azure AD con Keylight, sono necessari gli elementi seguenti:

- Una sottoscrizione di Azure.
- Sottoscrizione di Keylight abilitata per l'accesso Single Sign-On


> [AZURE.NOTE] Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non è disponibile un ambiente di prova di Azure AD, è possibile ottenere una versione di prova di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).


## Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Keylight dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD


## Aggiunta di Keylight dalla raccolta
Per configurare l'integrazione di Keylight in Azure AD, è necessario aggiungere Keylight dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Keylight dalla raccolta, seguire questa procedura:**

1. Nel **portale di gestione di Azure** fare clic su **Active Directory** nel pannello di navigazione sinistro. 

	![Active Directory][1]

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

	![Applicazioni][2]

4. Fare clic su **Add** nella parte inferiore della pagina.

	![Applicazioni][3]

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

	![Applicazioni][4]

6. Nella casella di ricerca digitare **Keylight**.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_01.png)

7. Nel riquadro dei risultati selezionare **Keylight** e quindi fare clic su **Completa** per aggiungere l'applicazione.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_02.png)

##  Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Keylight con un utente test di nome "Britta Simon".

Per configurare e testare l'accesso Single Sign-On di Azure AD con Keylight, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Creazione di un utente test di Keylight](#creating-a-keylight-test-user)**: per avere una controparte di Britta Simon in Keylight collegata alla relativa rappresentazione in Azure AD.
5. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Keylight.


**Per configurare l'accesso Single Sign-On di Azure AD con Keylight, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Keylight** del portale di Azure fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

	![Configura accesso Single Sign-On][6]


2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Keylight**, selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.

	![Configura accesso Single Sign-On](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_03.png)

3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:
 
	![Configura accesso Single Sign-On](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_04.png)


    a. Nella casella di testo URL di accesso, digitare l'URL usato dagli utenti per accedere all'applicazione Keylight adottando il modello seguente: **"https://<company name>.keylightgrc.com/Login.aspx?saml=1"**.


4. Nella pagina **Configura accesso Single Sign-On in Keylight** seguire questa procedura:
 
	![Configura accesso Single Sign-On](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_05.png)

    a. Fare clic su **Scarica certificato** e quindi salvare il file nel computer.

    b. Fare clic su **Avanti**.


5. Per abilitare l'accesso Single Sign-On in Keylight, seguire questa procedura:
 
    a. Accedere al proprio account Keylight come amministratore.

    b. Nel menu in alto fare clic su **Person (Persona)** e selezionare **Keylight Setup (Configurazione Keylight)**.
       
	![Configura accesso Single Sign-On](./media/active-directory-saas-keylight-tutorial/401.png)

    c. Nella visualizzazione albero a sinistra fare clic su **SAML**.

	![Configura accesso Single Sign-On](./media/active-directory-saas-keylight-tutorial/402.png)

    d. Nella finestra di dialogo **SAML Settings (Impostazioni SAML)** fare clic su **Edit (Modifica)**.

	![Configura accesso Single Sign-On](./media/active-directory-saas-keylight-tutorial/404.png)
  

5. Nella pagina della finestra di dialogo **Edit SAML Settings (Modifica impostazioni SAML)** seguire questa procedura:

	![Configura accesso Single Sign-On](./media/active-directory-saas-keylight-tutorial/405.png)

    a. Impostare **SAML authentication (Autenticazione SAML)** su **Active (Attiva)**.


    b. Nel portale di Azure AD classico copiare il valore di **URL SSO SAML** e incollarlo nella casella di testo **Identity Provider Login URL (URL di accesso al provider di identità)**.

    c. Nel portale di Azure AD classico copiare il valore di **URL servizio Single Sign-Out** e incollarlo nella casella di testo **Identity Provider Logout URL (URL di disconnessione dal provider di identità)**.

    d. Fare clic su **Choose File (Scegli file)** per selezionare il certificato Keylight scaricato e quindi fare clic su **Open (Apri)** per caricare il certificato.


    e. Impostare **SAML User Id location (Posizione ID utente SAML)** su **NameIdentifier element of the subject statement (Elemento NameIdentifier dell'istruzione Subject)**.
   
    f. Immettere il **Keylight Service Provider (Provider di servizi Keylight) adottando il modello seguente: **https://&lt;Company Name&gt;.keylightgrc.com**.

    g. Impostare **Auto-provision users (Utenti che eseguono il provisioning automatico)** su **Active (Attivo)**.

    h. Impostare **Auto-provision account type (Tipo di account di provisioning automatico)** su **Full User (Utente completo)**.

    i. Per **Auto-provision security role (Ruolo di sicurezza del provisioning automatico)** selezionare **Standard User with SAML (Utente standard con SAML)**.
   
    j. Per **Auto-provision security config (Configurazione di sicurezza del provisioning automatico)** selezionare **Standard User Configuration (Configurazione utente standard)**.
   
    k. Nella casella di testo Email attribute (Attributo di posta elettronica) digitare **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    l. Nella casella di testo **First name attribute (Attributo nome)** digitare **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.

    m. Nella casella di testo **Last name attribute (Attributo cognome)** digitare **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.

    n. Fare clic su **Save**.
   
  
   
  
6. Nel portale di Azure selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**.

	![Accesso Single Sign-On di Azure AD][10]

7. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.

	![Accesso Single Sign-On di Azure AD][11]




### Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di Azure.

Nell'elenco di utenti selezionare **Britta Simon**.

![Creare un utente di Azure AD][20]



**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic su **Active Directory** nel riquadro di spostamento sinistro.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_09.png)


2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per visualizzare l'elenco di utenti, fare clic su **Utenti** nel menu in alto.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_03.png)


4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_04.png)

5. Nella pagina della finestra di dialogo **Informazioni sull'utente** seguire questa procedura:

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_05.png)

    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.

    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina della finestra di dialogo **Profilo utente** seguire questa procedura:

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_06.png)

    a. Nella casella di testo **Nome** digitare **Britta**.

    b. Nella casella di testo **Cognome** digitare **Simon**.

    c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.

    d. Nell'elenco **Ruolo**, selezionare **Utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina della finestra di dialogo **Ottieni password temporanea** fare clic su **crea**.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_07.png)

8. Nella pagina della finestra di dialogo **Ottieni password temporanea** seguire questa procedura:

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_08.png)

    a. Prendere nota del valore visualizzato in **Nuova password**.

    b. Fare clic su **Complete**.



### Creazione di un utente test di Keylight

In questa sezione viene creato un utente chiamato Britta Simon in Keylight. Keylight supporta il provisioning just-in-time, abilitato per impostazione predefinita.

Non è necessario alcun intervento dell'utente in questa sezione. Quando si accede a Keylight se l'utente non esiste ancora, viene creato un nuovo utente.

> [AZURE.NOTE] Per creare un utente manualmente, è necessario contattare il team di supporto di Keylight.


### Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Keylight.

![Assegna utente][200]

**Per assegnare Britta Simon a Keylight, seguire questa procedura:**

1. Per aprire la visualizzazione applicazioni nel portale di Azure, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.

	![Assegna utente][201]

2. Nell'elenco di applicazioni selezionare **Keylight**.

	![Configura accesso Single Sign-On](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_50.png)

1. Scegliere **Utenti** dal menu in alto.

	![Assegna utente][203]

1. Nell'elenco di utenti selezionare **Britta Simon**.

2. Fare clic su **Assegna** nella barra degli strumenti in basso.

	![Assegna utente][205]



### Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Keylight nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Keylight.


## Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0420_2016-->