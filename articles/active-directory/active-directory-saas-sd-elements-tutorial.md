<properties
	pageTitle="Esercitazione: Integrazione di Azure Active Directory con SD Elements| Microsoft Azure"
	description="Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SD Elements."
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
	ms.date="09/01/2016"
	ms.author="jeedes"/>


# Esercitazione: Integrazione di Azure Active Directory con SD Elements

L’obiettivo di questa esercitazione è descrivere l’integrazione di SD Elements con Azure Active Directory (Azure AD). L'integrazione di SD Elements con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a SD Elements
- È possibile abilitare gli utenti per l'accesso automatico a SD Elements (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account da una posizione centrale: Azure Active Directory


Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Prerequisiti

Per configurare l'integrazione di Azure AD con SD Elements, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di SD Elements abilitata per l'accesso Single Sign-On


> [AZURE.NOTE] Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non è disponibile un ambiente di prova di Azure AD, è possibile ottenere una versione di prova di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).


## Descrizione dello scenario
L'obiettivo di questa esercitazione è testare l'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di SD Elements dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD


## Aggiunta di SD Elements dalla raccolta
Per configurare l'integrazione di SD Elements in Azure AD, è necessario aggiungere SD Elements dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere SD Elements dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.

	![Active Directory][1]

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

	![Applicazioni][2]

4. Fare clic su **Add** nella parte inferiore della pagina.

	![Applicazioni][3]

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

	![Applicazioni][4]

6. Nella casella di ricerca digitare **SD Elements**.
 
	![Creazione di un utente test di Azure AD](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_01.png)

7. Nel riquadro dei risultati selezionare **SD Elements** quindi fare clic su **Completa** per aggiungere l’applicazione.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_02.png)


##  Configurazione e test dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come configurare e testare l'accesso Single Sign-On di Azure AD con SD Elements in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di SD Elements che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SD Elements. La relazione di collegamento viene stabilita assegnando al valore di **nome utente** in Azure AD lo stesso valore di **Username** in SD Elements.

Per configurare e testare l'accesso Single Sign-On di Azure AD con SD Elements, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Creazione di un utente test di SD Elements](#creating-a-sd-elements-test-user)**: per avere una controparte di Britta Simon in SD Elements collegata alla relativa rappresentazione in Azure AD.
5. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### Configurazione dell'accesso Single Sign-On di Azure AD

Questa sezione descrive come abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure classico e configurare l'accesso Single Sign-On nell'applicazione SD Elements.

L'applicazione SD Elements prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli **attributi del token SAML**. La schermata seguente mostra un esempio relativo a questa operazione:

![Configura accesso Single Sign-On](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_14.png)



**Per configurare Single Sign-On di Azure AD con SD Elements, seguire questa procedura:**

1. Nel portale di Azure classico, nella pagina di integrazione dell’applicazione **SD Elements** fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

	![Configura accesso Single Sign-On][6]

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a SD Elements** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.

	![Configura accesso Single Sign-On](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_03.png)

3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:
    
	![Configura accesso Single Sign-On](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_04.png)


    a. Nella casella di testo **Autorità di certificazione** digitare l'URL dell'autorità emittente del tenant usando il modello seguente: *https://\<nome del tenant>.sdelements.com/sso/saml2/metadata*
   
    b. Nella casella di testo **URL di risposta** digitare l'URL di risposta del tenant usando il modello seguente: *https://\<nome del tenant>.sdelements.com/sso/saml2/acs/*

    > [AZURE.NOTE] Se sono necessari l'URL dell'autorità di certificazione e l'URL di risposta effettivi per il tenant, contattare il [team di supporto di SD Elements](mailto:support@sdelements.com).
      
    c. Fare clic su **Next**.


4. Nella pagina **Configura accesso Single Sign-On in SD Elements** seguire questa procedura:
   
	![Configura accesso Single Sign-On](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_05.png)

    a. Fare clic su **Scarica certificato** e quindi salvare il file nel computer.

    b. Fare clic su **Avanti**.


1. Per avere l'accesso Single Sign-On abilitato, contattare il [team di supporto di SD Elements](mailto:support@sdelements.com) e fornire il file del certificato scaricato.


5. In una finestra del browser diversa accedere al tenant SD Elements come amministratore.

6. Nel menu in alto fare clic su System e quindi su Single Sign-On.

	![Configura accesso Single Sign-On](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_09.png)


7. Nella finestra di dialogo **Impostazioni Single Sign-On** seguire questa procedura:

	![Configura accesso Single Sign-On](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_10.png)

    a. Come **SSO Type** selezionare **SAML**.

    b. Nella finestra di dialogo **Configura accesso Single Sign-On in SD Elements** del portale di Azure classico, copiare il valore di **URL autorità di certificazione** e incollarlo nella casella di testo **Identity Provider Entity ID**.

    c. Nella finestra di dialogo **Configura accesso Single Sign-On in SD Elements** del portale di Azure classico, copiare il valore di **URL servizio Single Sign-On** e incollarlo nella casella di testo **Identity Provider Single Sign-On Service**.

    d. Fare clic su **Save**.

6. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e fare clic su **Avanti**.

	![Accesso Single Sign-On di Azure AD][10]

7. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.

	![Accesso Single Sign-On di Azure AD][11]

1. Nel menu in alto fare clic su **Attributi** per aprire la finestra di dialogo **Attributi token SAML**.
    
	![Configura accesso Single Sign-On][21]


2. Per ogni riga di dati nella tabella precedente, seguire questa procedura:

    | Nome attributo | Valore attributo |
    | ---            | ---             |
    | email | user.mail |
    | firstname | user.givenname |
    | lastname | user.surname |


    a. Fare clic su **Aggiungi attributo utente**.
    
	![Configura accesso Single Sign-On][23]

    b. Nella casella di testo **Nome attributo** digitare il **nome attributo** e per **Valore attributo** selezionare il valore dell’attributo indicato per la riga.
    
	![Configura accesso Single Sign-On][22]

    c. Fare clic su **Aggiungi attributo utente**.
    
	![Configura accesso Single Sign-On][23]

1. Fare clic su **Applica modifiche**.
    
	![Configura accesso Single Sign-On][24]

### Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di Azure classico.

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_09.png)

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per visualizzare l'elenco di utenti, fare clic su **Utenti** nel menu in alto.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_03.png)

4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_04.png)

5. Nella pagina della finestra di dialogo **Informazioni sull'utente** seguire questa procedura:

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_05.png)

    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.

    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.

    c. Fare clic su **Next**.

6.  Nella pagina della finestra di dialogo **Profilo utente** seguire questa procedura:

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_06.png)

    a. Nella casella di testo **Nome** digitare **Britta**.

    b. Nella casella di testo **Cognome** digitare **Simon**.

    c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.

    d. Nell'elenco **Ruolo**, selezionare **Utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_07.png)

8. Nella pagina **Ottieni password temporanea** seguire questa procedura:

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_08.png)

    a. Prendere nota del valore visualizzato in **Nuova password**.

    b. Fare clic su **Complete**.



### Creazione di un utente test di SD Elements

Questa sezione descrive come creare un utente denominato Britta Simon in SD Elements. Nel caso di SD Elements la creazione degli utenti è un'attività manuale.

**Per creare un utente denominato Britta Simon in SD Elements, seguire questa procedura:**

1.	In una finestra del Web browser accedere al sito aziendale di SD Elements come amministratore.

2.	Nel menu in alto fare clic su User Management e quindi su Users.
 
	![Creazione di un utente test di SD Elements](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_11.png)

3.	Fare clic su Add new user.
 
    ![Creazione di un utente test di SD Elements](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_12.png)

4.	Nella finestra di dialogo Add New User seguire questa procedura:

    ![Creazione di un utente test di SD Elements](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_13.png)

    a. Nella casella di testo **Email** digitare l'indirizzo di posta elettronica di Britta in Azure AD.

    b. Nella casella di testo **First Name** digitare **Britta**.

    c. Nella casella di testo **Last Name** digitare **Simon**.

    d. Come **Role** selezionare **User**.

    e. Fare clic su **Create User**.




### Assegnazione dell'utente test di Azure AD

Questa sezione descrive come abilitare Britta Simon per l'utilizzo dell'accesso Single Sign-On di Azure concedendole l'accesso a SD Elements.

![Assegna utente][200]

**Per assegnare Britta Simon a SD Elements, seguire questa procedura:**

1. Per aprire la visualizzazione applicazioni nel portale di Azure classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.

	![Assegna utente][201]

2. Nell’elenco di applicazioni selezionare **SD Elements**.

	![Configura accesso Single Sign-On](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_50.png)

1. Scegliere **Utenti** dal menu in alto.

	![Assegna utente][203]

1. Nell'elenco **Utenti** selezionare **Britta Simon**.

2. Fare clic su **Assegna** sulla barra degli strumenti in basso.

	![Assegna utente][205]



### Test dell'accesso Single Sign-On

Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso. Facendo clic sul riquadro SD Elements nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione SD Elements.


## Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_100.png

[21]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_80.png
[22]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_82.png
[23]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_81.png
[24]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_83.png


[200]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0907_2016-->