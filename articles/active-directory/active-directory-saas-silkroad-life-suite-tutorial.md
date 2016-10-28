<properties
	pageTitle="Esercitazione: Integrazione di Azure Active Directory con SilkRoad Life Suite | Microsoft Azure"
	description="Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SilkRoad Life Suite."
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
	ms.date="09/19/2016"
	ms.author="jeedes"/> 


# Esercitazione: Integrazione di Azure Active Directory con SilkRoad Life Suite

Questa esercitazione descrive l'integrazione di SilkRoad Life Suite con Azure Active Directory (Azure AD). L'integrazione di SilkRoad Life Suite con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a SilkRoad Life Suite.
- È possibile abilitare gli utenti per l'accesso automatico a SilkRoad Life Suite (Single Sign-On) con i propri account Azure AD.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Prerequisiti 

Per configurare l'integrazione di Azure AD con SilkRoad Life Suite, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di SilkRoad Life Suite abilitata per l'accesso Single Sign-On.


> [AZURE.NOTE] Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non è disponibile un ambiente di prova di Azure AD, è possibile ottenere una versione di prova di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

 
## Descrizione dello scenario
L'obiettivo di questa esercitazione è testare l'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di SilkRoad Life Suite dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD


## Aggiunta di SilkRoad Life Suite dalla raccolta
Per configurare l'integrazione di SilkRoad Life Suite in Azure AD, è necessario aggiungere SilkRoad Life Suite dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere SilkRoad Life Suite dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.

	![Active Directory][1] 

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

	![Applicazioni][2] 

4. Fare clic su **Add** nella parte inferiore della pagina.

	![Applicazioni][3] 

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

	![Applicazioni][4]

6. Nella casella di ricerca digitare **SilkRoad Life Suite**.

	![Applicazioni][5]

7. Nel riquadro dei risultati selezionare **SilkRoad Life Suite** e quindi fare clic su **Completa** per aggiungere l'applicazione.

	![Applicazioni][50]


##  Configurazione e test dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come configurare e testare l'accesso Single Sign-On di Azure AD con SilkRoad Life Suite in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di SilkRoad Life Suite che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SilkRoad Life Suite. La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **nome utente** in SilkRoad Life Suite.
 
Per configurare e testare l'accesso Single Sign-On di Azure AD con SilkRoad Life Suite, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Creazione di un utente test di SilkRoad Life Suite](#creating-a-silkroad-life-suite-test-user)**: per avere una controparte di Britta Simon in SilkRoad Life Suite collegata alla relativa rappresentazione in Azure AD.
5. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### Configurazione dell'accesso Single Sign-On di Azure AD

Questa sezione descrive come abilitare Single Sign-On di Azure AD nel portale di Azure classico e configurare l'accesso Single Sign-On nell'applicazione SilkRoad Life Suite.

**Per configurare Single Sign-On di Azure AD con SilkRoad Life Suite, seguire questa procedura:**

5. Accedere al sito aziendale di SilkRoad come amministratore.


    > [AZURE.NOTE] Per ottenere l'accesso all'applicazione SilkRoad Life Suite Authentication per configurare la federazione con Microsoft Azure AD, contattare il supporto SilkRoad o il rappresentante dei servizi SilkRoad.


6. Passare a **Service Provider** (Provider di servizi) e quindi fare clic su **Federation Details** (Dettagli federazione).

	![Accesso Single Sign-On di Azure AD][10]


1. Fare clic su **Download Federation Metadata** (Scarica metadati federazione) e quindi salvare il file di metadati nel computer.

	![Accesso Single Sign-On di Azure AD][11]

3. Nella pagina di integrazione dell'applicazione **SilkRoad Life Suite** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

	![Configura accesso Single Sign-On][6]

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a SilkRoad Life Suite** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.

	![Accesso Single Sign-On di Azure AD][7]

3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:

	![Accesso Single Sign-On di Azure AD][8]
 
    a. Nella casella di testo **URL di accesso** digitare l'URL usato dagli utenti per accedere al sito SilkRoad Life Suite, ad esempio *https://defcompanytest-test-redcarpet.silkroad-eng.com/Authentication/*.

    b. Aprire il file dei metadati **Silkroad**scaricato.

    c. Trovare il tag **AssertionConsumerService** e quindi copiare l'attributo **Location**.

	![Accesso Single Sign-On di Azure AD][21]
   
    d. Incollare il valore nella casella di testo**URL di risposta**.
 
    e. Fare clic su **Avanti**.
 
4. Nella pagina **Configura accesso Single Sign-On in SilkRoad Life Suite** seguire questa procedura:

	![Accesso Single Sign-On di Azure AD][9]

    a. Fare clic su Download certificato e quindi salvare il file nel computer.

    b. Fare clic su **Next**.




1. Nell'applicazione **SilkRoad**, fare clic su **Authentication Sources** (Origini autenticazione).

	![Accesso Single Sign-On di Azure AD][12]



1. Fare clic su **Add Authentication Source** (Aggiungi origine autenticazione).

	![Accesso Single Sign-On di Azure AD][13]



1. Nella sezione **Add Authentication Source** (Aggiungi origine autenticazione) seguire questa procedura:

	![Accesso Single Sign-On di Azure AD][14]

    a. In **Option 2 - Metadata File** fare clic su **Browse** per caricare il file di metadati scaricato.

    b. Fare clic su **Create Identity Provider using File Data**.



1. Nella sezione **Authentication Sources** (Origini autenticazione) fare clic su **Edit** (Modifica).

	![Accesso Single Sign-On di Azure AD][15]


1. Nella finestra di dialogo **Edit Authentication Source** (Modifica origine autenticazione) seguire questa procedura:

	![Accesso Single Sign-On di Azure AD][16]

    a. In **Enabled** selezionare **Yes**.

    b. Nella casella di testo **IdP Description** digitare una descrizione per la configurazione, ad esempio *Azure AD SSO*.

    c. Nella casella di testo **IdP Name** digitare un nome specifico per la configurazione, ad esempio *Azure SP*.

    d. Fare clic su **Save**.


6. Disabilitare tutte le altre origini di autenticazione.

	![Accesso Single Sign-On di Azure AD][17]

7. Nella pagina **Conferma Single Sign-On** del portale di Azure classico fare clic su **Avanti**.

	![Accesso Single Sign-On di Azure AD][18]

1. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.

	![Accesso Single Sign-On di Azure AD][19] 


### Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di Azure classico.

![Creare un utente di Azure AD][20] 

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_09.png) 

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per visualizzare l'elenco di utenti, fare clic su **Utenti** nel menu in alto.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_03.png) 
 
4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_04.png) 

5. Nella pagina della finestra di dialogo **Informazioni sull'utente** seguire questa procedura:

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_05.png)

    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.

    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.

    c. Fare clic su **Next**.

6.  Nella pagina della finestra di dialogo **Profilo utente** seguire questa procedura:

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_06.png) 
 
    a. Nella casella di testo **Nome** digitare **Britta**.

    b. Nella casella di testo **Cognome** digitare **Simon**.

    c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.

    d. Nell'elenco **Ruolo** selezionare **Utente**. e. Fare clic su **Avanti**.

7. Nella pagina della finestra di dialogo **Ottieni password temporanea** fare clic su **crea**.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_07.png) 
 
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_08.png) 
  
    a. Prendere nota del valore visualizzato in **Nuova password**.

    b. Fare clic su **Complete**.

  
 
### Creazione di un utente di test di SilkRoad Life Suite

Questa sezione descrive come creare un utente chiamato Britta Simon in SilkRoad Life Suite. Britta deve avere un ID SSO, a volte definito *AuthParam*, che corrisponde al valore **emailaddress** in Azure AD.

**Per creare un utente test denominato Britta Simon in SilkRoad Life Suite, seguire questa procedura:**

1. Chiedere al team di supporto di SilkRoad Life Suite di creare un utente con un valore per l'attributo **SSO ID** equivalente all’**indirizzo email**di Britta Simon in Azure AD.



### Assegnazione dell'utente test di Azure AD

Questa sezione descrive come abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a SilkRoad Life Suite.

![Assegna utente][200]

**Per assegnare Britta Simon a SilkRoad Life Suite, seguire questa procedura:**

1. Per aprire la visualizzazione applicazioni nel portale di Azure classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.

	![Assegna utente][201]

2. Nell'elenco delle applicazioni selezionare **SilkRoad Life Suite**.

	![Assegna utente][202]

1. Scegliere **Utenti** dal menu in alto.

	![Assegna utente][203] 

1. Nell'elenco di utenti selezionare **Britta Simon**.

2. Fare clic su **Assegna** sulla barra degli strumenti in basso.

	![Assegna utente][205] 



### Test dell'accesso Single Sign-On

Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso. Quando si fa clic sul riquadro SilkRoad Life Suite nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione SilkRoad Life Suite.


## Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)


<!--Image references--> 

[1]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_01.png
[50]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_02.png

[6]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_03.png
[8]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_04.png
[9]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_05.png
[10]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_06.png
[11]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_07.png
[12]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_08.png
[13]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_09.png
[14]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_10.png
[15]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_11.png
[16]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_12.png
[17]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_13.png
[18]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_06.png
[19]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_07.png


[20]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_15.png


[200]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_14.png
[203]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0921_2016-->