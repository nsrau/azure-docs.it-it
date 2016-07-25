<properties
	pageTitle="Esercitazione: Integrazione di Azure Active Directory con Optimizely | Microsoft Azure"
	description="Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Optimizely."
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
	ms.date="06/25/2016"
	ms.author="jeedes"/>


# Esercitazione: Integrazione di Azure Active Directory con Optimizely

Questa esercitazione descrive come integrare Optimizely con Azure Active Directory (Azure AD).

L'integrazione di Optimizely con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Optimizely
- È possibile abilitare gli utenti per l'accesso automatico a Optimizely (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Prerequisiti

Per configurare l'integrazione di Azure AD con Optimizely, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di **Optimizely** abilitata per l'accesso Single Sign-On


> [AZURE.NOTE] Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non è disponibile un ambiente di prova di Azure AD, è possibile ottenere una versione di prova di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).


## Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Optimizely dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD


## Aggiunta di Optimizely dalla raccolta
Per configurare l'integrazione di Optimizely in Azure AD, è necessario aggiungere Optimizely dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Optimizely dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.

	![Active Directory][1]

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

	![Applicazioni][2]

4. Fare clic su **Add** nella parte inferiore della pagina.

	![Applicazioni][3]

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

	![Applicazioni][4]

6. Nella casella di ricerca digitare **Optimizely**.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_01.png)

7. Nel riquadro dei risultati selezionare **Optimizely** e quindi fare clic su **Completa** per aggiungere l'applicazione.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_02.png)

##  Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Optimizely con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Optimizely che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Optimizely. La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** in Optimizely.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Optimizely, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Creazione di un utente test di Optimizely](#creating-an-optimizely-test-user)**: per avere una controparte di Britta Simon in Optimizely collegata alla relativa rappresentazione in Azure AD.
5. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### Configurazione dell'accesso Single Sign-On di Azure AD

Questa sezione descrive come abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure classico e configurare l'accesso Single Sign-On nell'applicazione Optimizely.

L'applicazione Optimizely si aspetta che le asserzioni SAML contengano un attributo "email". Il valore di "email" deve essere un indirizzo di posta elettronica riconosciuto da Optimizely che può essere autenticato da Azure AD. Configurare l'attestazione "email" per questa applicazione. È possibile gestire i valori di questi attributi dalla scheda **"Attributes"** (Attributi) dell'applicazione. La schermata seguente illustra un esempio relativo a questa operazione.


![Configura accesso Single Sign-On](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_03.png)


**Per configurare l'accesso Single Sign-On di Azure AD con Optimizely, seguire questa procedura:**

1. Nel menu visualizzato nella parte superiore della pagina di integrazione dell'applicazione **Optimizely** del portale di Azure classico fare clic su **Attributi**.
     
    ![Configura accesso Single Sign-On][5]

2. Nella finestra di dialogo relativa agli attributi del token SAML, aggiungere l'attributo "email".

	a. Fare clic su **aggiungi attributo utente** per aprire la finestra di dialogo **Aggiungi attributo utente**.
	
	![Configura accesso Single Sign-On](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_05.png)

	b. Nella casella di testo **Nome attributo** digitare il nome dell'attributo "email".

	c. Dall'elenco **Valore attributo** selezionare il valore dell'attributo "userprincipalname" o un valore che contenga un indirizzo di posta elettronica riconosciuto da Azure AD e Optimizely.

	d. Fare clic su **Complete**.
3. Nel menu in alto fare clic su **Avvio rapido**.

	![Configura accesso Single Sign-On][6]
4. Nella pagina di integrazione dell'applicazione **Optimizely** del portale classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

	![Configura accesso Single Sign-On][7]

5. Nella pagina **How would you like users to sign on to Optimizely** (Stabilire come si desidera che gli utenti accedano a Optimizely) selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
 	
	![Configura accesso Single Sign-On](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_06.png)

6. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:

	![Configura accesso Single Sign-On](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_07.png)


    a. Nella casella di testo **URL di accesso** digitare `https://app.optimizely.net/contoso`.

	b. Nella casella di testo **Identificatore** digitare `urn:auth0:optimizely:contoso`.

	c. Fare clic su **Avanti**.


	> [AZURE.NOTE] I valori di **URL di accesso** e **Identificatore** sono solo segnaposti dei valori effettivi. È possibile trovare istruzioni per l'acquisizione dei valori effettivi da Optimizely più avanti in questa esercitazione.

7. Nella pagina **Configure single sign-on at Optimizely** (Configura accesso Single Sign-On in Optimizely) seguire questa procedura:

	![Configura accesso Single Sign-On](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_08.png)

    a. Fare clic su **Scarica certificato** e quindi salvare il file nel computer.

    b. Copiare il valore di **URL servizio Single Sign-On**.

8. Per ottenere l'accesso Single Sign-On configurato per l'applicazione, contattare l'account manager di Optimizely e fornire le informazioni seguenti:

    - Certificato scaricato
    - URL servizio Single Sign-On
 
    In risposta al messaggio di posta elettronica, Optimizely fornisce l'URL di accesso (SSO avviato da SP) e i valori di Identificatore (ID entità del provider di servizi).

9. Tornare alla finestra di dialogo **Configurare le impostazioni dell'app** e seguire questa procedura:

	![Configura accesso Single Sign-On](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_07.png)

	a. Nella casella di testo **URL di accesso** digitare l'**URL SSO avviato da SP** fornito da Optimizely.

	b. Nella casella di testo **Identificatore** digitare l'**ID entità del provider di servizi** fornito da Optimizely.

    c. Fare clic su **Avanti**.

10. Nella pagina **Configure single sign-on at Optimizely** (Configura accesso Single Sign-On in Optimizely) seguire questa procedura:
	
	![Accesso Single Sign-On di Azure AD][10]

    a. Selezionare la conferma della configurazione dell'accesso Single Sign-On.

    b. Fare clic su **Avanti**.

11. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.
  	
	![Accesso Single Sign-On di Azure AD][11]

12. In una finestra del browser diversa accedere all'applicazione Optimizely.
13. Fare clic sul nome del proprio account in alto a destra e quindi su **Account Settings** (Impostazioni account).

	![Accesso Single Sign-On di Azure AD](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_09.png)

14. Nella scheda Account, selezionare la casella **Enable SSO** (Abilita SSO) nella sezione **Overview** (Panoramica) di Single Sign On.

	![Accesso Single Sign-On di Azure AD](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_10.png)

### Creazione di un utente test di Azure AD
In questa sezione viene creato un utente test chiamato Britta Simon nel portale classico. Nell'elenco di utenti selezionare **Britta Simon**.

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
	
	![Creazione di un utente test di Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_09.png)

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per visualizzare l'elenco di utenti, selezionare **Utenti** dal menu in alto.
	
	![Creazione di un utente test di Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_03.png)

4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_04.png)

5. Nella pagina **Informazioni sull'utente** seguire questa procedura:
 
	![Creazione di un utente test di Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_05.png)

    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.

    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina **Profilo utente** seguire questa procedura:

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_06.png)

    a. Nella casella di testo **Nome** digitare **Britta**.

    b. Nella casella di testo **Cognome** digitare **Simon**.

    c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.

    d. Nell'elenco **Ruolo**, selezionare **Utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_07.png)

8. Nella pagina **Ottieni password temporanea** seguire questa procedura:

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_08.png)

    a. Prendere nota del valore visualizzato in **Nuova password**.

    b. Fare clic su **Complete**.



### Creazione di un utente test di Optimizely

In questa sezione viene creato un utente di nome Britta Simon in Optimizely.

1. Nella home page selezionare la scheda **Collaborators** (Collaboratori)
2. Fare clic su **New Collaborator** (Nuovo collaboratore) per aggiungere un nuovo collaboratore al progetto.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_10.png)

3.  Immettere l'indirizzo di posta elettronica e assegnare un ruolo ai nuovi collaboratori. Fare clic su **Invita**.


	![Creazione di un utente test di Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_11.png)

4. L'utente riceve un invito tramite posta elettronica. L'utente dovrà eseguire l'accesso a Optimizely usando l'indirizzo di posta elettronica.


### Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Optimizely.

![Assegna utente][200]

**Per assegnare Britta Simon a Optimizely, seguire questa procedura:**

1. Per aprire la visualizzazione applicazioni nel portale classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.

	![Assegna utente][201]

2. Nell'elenco di applicazioni selezionare **Optimizely**.

	![Configura accesso Single Sign-On](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_50.png)

1. Scegliere **Utenti** dal menu in alto.

	![Assegna utente][203]

1. Nell'elenco Tutti gli utenti selezionare **Britta Simon**.

2. Fare clic su **Assegna** nella barra degli strumenti in basso.

	![Assegna utente][205]


### Test dell'accesso Single Sign-On

Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Optimizely nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Optimizely.

## Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_06.png
[7]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0713_2016-->