<properties
	pageTitle="Esercitazione: Integrazione di Azure Active Directory con PostBeyond | Microsoft Azure"
	description="Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e PostBeyond."
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
	ms.date="08/09/2016"
	ms.author="jeedes"/>  


# Esercitazione: Integrazione di Azure Active Directory con PostBeyond

Questa esercitazione descrive come integrare PostBeyond con Azure Active Directory (Azure AD).

L'integrazione di PostBeyond con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a PostBeyond
- È possibile abilitare gli utenti per l'accesso automatico a PostBeyond (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Prerequisiti

Per configurare l'integrazione di Azure AD con PostBeyond, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di **PostBeyond** abilitata per l'accesso Single Sign-On


> [AZURE.NOTE] Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).


## Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di PostBeyond dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD


## Aggiunta di PostBeyond dalla raccolta
Per configurare l'integrazione di PostBeyond in Azure AD, è necessario aggiungerla dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere PostBeyond dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.

	![Active Directory][1]  

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

	![Applicazioni][2]  

4. Fare clic su **Add** nella parte inferiore della pagina.

	![Applicazioni][3]  

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

	![Applicazioni][4]  

6. Nella casella di ricerca digitare **PostBeyond**.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-postbeyond-tutorial/tutorial_postbeyond_01.png)  

7. Nel riquadro dei risultati selezionare **PostBeyond** e quindi fare clic su **Completa** per aggiungere l'applicazione.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-postbeyond-tutorial/tutorial_postbeyond_02.png)  

##  Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con PostBeyond con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di PostBeyond che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in PostBeyond. La relazione di collegamento viene stabilita assegnando al valore di **nome utente** in Azure AD lo stesso valore di **Username** in PostBeyond.

Per configurare e testare l'accesso Single Sign-On di Azure AD con PostBeyond, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Creazione di un utente test di PostBeyond](#creating-a-PostBeyond-test-user)**: per avere una controparte di Britta Simon in PostBeyond collegata alla relativa rappresentazione in Azure AD.
5. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### Configurazione dell'accesso Single Sign-On di Azure AD

Questa sezione descrive come abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure classico e configurare l'accesso Single Sign-On nell'applicazione PostBeyond.


**Per configurare l'accesso Single Sign-On di Azure AD con PostBeyond, seguire questa procedura:**

1. Nel menu in alto fare clic su **Avvio rapido**.

	![Configura accesso Single Sign-On][6]  

2. Nella pagina di integrazione dell'applicazione **PostBeyond** del portale di Azure classico, fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

	![Configura accesso Single Sign-On][7]

3. Nella pagina **Stabilire come si desidera che gli utenti accedano a PostBeyond**, selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.
 	
	![Configura accesso Single Sign-On](./media/active-directory-saas-postbeyond-tutorial/tutorial_postbeyond_06.png)  

4. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:

	![Configura accesso Single Sign-On](./media/active-directory-saas-postbeyond-tutorial/tutorial_postbeyond_07.png)


    a. Nella casella di testo URL accesso digitare un URL corrispondente al modello seguente: `https://app.postbeyond.com`.

	b. Fare clic su **Avanti**.

5. Nella pagina **Configura accesso Single Sign-On in PostBeyond** fare clic su **Download certificato** e quindi salvare il file nel computer. Copiare i valori di URL dell'autorità di certificazione, URL servizio Single Sign-On e URL servizio Single Sign-Out. È necessario condividere queste informazioni con il supporto di PostBeyond per ottenere la configurazione di SSO.

	![Configura accesso Single Sign-On](./media/active-directory-saas-postbeyond-tutorial/tutorial_postbeyond_08.png)

6. Per ottenere la configurazione di SSO per l'applicazione, contattare il team di supporto di PostBeyond all'indirizzo <sso@postbeyond.com>. Per ottenere assistenza dal canale appropriato per configurare l'accesso SSO, fornire le informazioni seguenti:

	- Certificato scaricato
	- **URL dell'autorità di certificazione**
	- **URL SSO SAML**
	- **URL servizio Single Sign-Out**

7. Nel portale di Azure classico selezionare la conferma della configurazione e quindi fare clic su **Avanti**.
	
	![Accesso Single Sign-On di Azure AD][10]  

8. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.
  	
	![Accesso Single Sign-On di Azure AD][11]  

### Creazione di un utente test di Azure AD
In questa sezione viene creato un utente test chiamato Britta Simon nel portale classico.

![Creare un utente di Azure AD][20]  

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
	
	![Creazione di un utente test di Azure AD](./media/active-directory-saas-postbeyond-tutorial/create_aaduser_09.png)  

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per visualizzare l'elenco di utenti, fare clic su **Utenti** nel menu in alto.
	
	![Creazione di un utente test di Azure AD](./media/active-directory-saas-postbeyond-tutorial/create_aaduser_03.png)  

4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-postbeyond-tutorial/create_aaduser_04.png)  

5. Nella pagina della finestra di dialogo **Informazioni sull'utente** seguire questa procedura:
 
	![Creazione di un utente test di Azure AD](./media/active-directory-saas-postbeyond-tutorial/create_aaduser_05.png)

    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.

    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.

    c. Fare clic su **Next**.

6.  Nella pagina della finestra di dialogo **Profilo utente** seguire questa procedura:

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-postbeyond-tutorial/create_aaduser_06.png)  

    a. Nella casella di testo **Nome** digitare **Britta**.

    b. Nella casella di testo **Cognome** digitare **Simon**.

    c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.

    d. Nell'elenco **Ruolo**, selezionare **Utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-postbeyond-tutorial/create_aaduser_07.png)  

8. Nella pagina **Ottieni password temporanea** seguire questa procedura:

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-postbeyond-tutorial/create_aaduser_08.png)  

    a. Prendere nota del valore visualizzato in **Nuova password**.

    b. Fare clic su **Completa**.



### Creazione di un utente test PostBeyond

In questa sezione viene creato un utente chiamato Britta Simon in PostBeyond. Se non si sa come aggiungere Britta Simon in PostBeyond, rivolgersi al team di supporto di PostBeyond per aggiungere l'utente test e abilitare SSO. A tale scopo, scrivere a <sso@postbeyond.com>.

### Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a PostBeyond.

![Assegna utente][200]  

**Per assegnare Britta Simon a PostBeyond, seguire questa procedura:**

1. Per aprire la visualizzazione delle applicazioni nel portale classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.

	![Assegna utente][201]

2. Nell'elenco di applicazioni selezionare **PostBeyond**.

	![Configura accesso Single Sign-On](./media/active-directory-saas-postbeyond-tutorial/tutorial_postbeyond_09.png)  

1. Scegliere **Utenti** dal menu in alto.

	![Assegna utente][203]  

1. Nell'elenco Tutti gli utenti selezionare **Britta Simon**.

2. Fare clic su **Assegna** sulla barra degli strumenti in basso.

	![Assegna utente][205]  


### Test dell'accesso Single Sign-On

Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro PostBeyond nel pannello di accesso, verrà visualizzata la pagina di accesso di PostBeyond. Fare clic su **Sign in with Office 365** (Accedi con Office 365) e immettere le credenziali di Azure AD. Verrà eseguito l'accesso a PostBeyond.

## Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->  

[1]: ./media/active-directory-saas-postbeyond-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-postbeyond-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-postbeyond-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-postbeyond-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-postbeyond-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-postbeyond-tutorial/tutorial_general_06.png
[7]: ./media/active-directory-saas-postbeyond-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-postbeyond-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-postbeyond-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-postbeyond-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-postbeyond-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-postbeyond-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-postbeyond-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-postbeyond-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-postbeyond-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0817_2016-->