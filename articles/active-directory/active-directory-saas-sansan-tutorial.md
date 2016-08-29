<properties
	pageTitle="Esercitazione: Integrazione di Azure Active Directory con SanSan | Microsoft Azure"
	description="Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SanSan."
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
	ms.date="07/20/2016"
	ms.author="jeedes"/>


# Esercitazione: integrazione di Azure Active Directory con SanSan

Questa esercitazione descrive come integrare SanSan con Azure Active Directory (Azure AD).

L'integrazione di SanSan con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a SanSan
- È possibile abilitare gli utenti per l'accesso automatico a SanSan (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Prerequisiti

Per configurare l'integrazione di Azure AD con SanSan, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Samanage abilitata per l'accesso Single Sign-On


> [AZURE.NOTE] Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).


## Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Microsoft Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di SanSan dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Microsoft Azure AD


## Aggiunta di SanSan dalla raccolta
Per configurare l'integrazione di SanSan in Azure AD, è necessario aggiungere SanSan dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere SanSan dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.

	![Active Directory][1]

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

	![Applicazioni][2]

4. Fare clic su **Add** nella parte inferiore della pagina.

	![Applicazioni][3]

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

	![Applicazioni][4]

6. Nella casella di ricerca digitare **SanSan**.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-sansan-tutorial/tutorial_sansan_01.png)

7. Nel riquadro dei risultati selezionare **SanSan**, quindi fare clic su **Completa** per aggiungere l'applicazione.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-sansan-tutorial/tutorial_sansan_06.png)

##  Configurazione e test dell'accesso Single Sign-On di Microsoft Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Microsoft Azure AD con SanSan in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di SanSan che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SanSan. La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente) in SanSan.

Per configurare e testare l'accesso Single Sign-On di Microsoft Azure AD con SanSan, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Microsoft Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Microsoft Azure AD con l'utente Britta Simon.
4. **[Creazione di un utente test di SanSan](#creating-an-sansan-test-user)**: per avere una controparte di Britta Simon in SanSan collegata alla relativa rappresentazione in Azure AD.
5. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Microsoft Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### Configurazione di Single Sign-On di Microsoft Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Microsoft Azure AD nel portale di Azure classico e viene configurato l'accesso Single Sign-On nell'applicazione SanSan.


**Per configurare Single Sign-On di Microsoft Azure AD con SanSan, seguire questa procedura:**


1. Nella pagina di integrazione dell'applicazione **SanSan** del portale di Azure classico, fare clic su Configura accesso Single Sign-On per aprire la finestra di dialogo Configura accesso Single Sign-On.

	![Configura accesso Single Sign-On](./media/active-directory-saas-sansan-tutorial/tutorial_general_05.png)

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a SanSan** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
 	
	![Configura accesso Single Sign-On](./media/active-directory-saas-sansan-tutorial/tutorial_sansan_03.png)

3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:

	![Configura accesso Single Sign-On](./media/active-directory-saas-sansan-tutorial/tutorial_sansan_04.png)

	a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente:

    | Environment | URL |
    | :--                     | :-- |
    | Web PC | `https://ap.sansan.com/v/saml2/<company name>/acs`|
    | App per dispositivi mobili nativa | `https://internal.api.sansan.com/saml2/<company name>/acs` |
    | Impostazioni del browser per dispositivi mobili | `https://ap.sansan.com/s/saml2/<company name>/acs` |


	b. Nella casella di testo **Identificatore** digitare l'URL usando il modello seguente:

    | Environment | URL |
    | :--                     | :-- |
    | Web PC | `https://ap.sansan.com/v/saml2/<company name>`|
    | App per dispositivi mobili nativa | `https://internal.api.sansan.com/saml2/<company name>` |
    | Impostazioni del browser per dispositivi mobili | `https://ap.sansan.com/s/saml2/<company name>` |


    c. Fare clic su **Next**.

4. Nella pagina **Configura accesso Single Sign-On in SanSan** seguire questa procedura:

	![Configura accesso Single Sign-On](./media/active-directory-saas-sansan-tutorial/tutorial_sansan_05.png)

    a. Fare clic su **Scarica certificato** e quindi salvare il file nel computer.

    b. Fare clic su **Avanti**.


5.  Al fine di configurare l'accesso SSO per l'applicazione, contattare il team di supporto di SanSan che fornirà l'aiuto richiesto. Specificare le informazioni seguenti:

	• **Certificato** scaricato

	• **ID provider di identità**

	• **URL SSO SAML**

	• **URL del servizio Single Sign-Out**

> [AZURE.NOTE] Le impostazioni del browser PC sono applicabili anche alle app per dispositivi mobili e al browser per dispositivi mobili, oltre che per Web PC.


6. Nel portale classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**.
	
	![Accesso Single Sign-On di Azure AD][10]

7. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.
  	
	![Accesso Single Sign-On di Azure AD][11]



### Creazione di un utente test di Azure AD

In questa sezione viene creato un utente test chiamato Britta Simon nel portale classico. Nell'elenco di utenti selezionare **Britta Simon**.

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
	
	![Creazione di un utente test di Azure AD](./media/active-directory-saas-sansan-tutorial/create_aaduser_09.png)

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per visualizzare l'elenco di utenti, fare clic su **Utenti** nel menu in alto.
	
	![Creazione di un utente test di Azure AD](./media/active-directory-saas-sansan-tutorial/create_aaduser_03.png)

4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-sansan-tutorial/create_aaduser_04.png)

5. Nella pagina della finestra di dialogo **Informazioni sull'utente** seguire questa procedura:
 
	![Creazione di un utente test di Azure AD](./media/active-directory-saas-sansan-tutorial/create_aaduser_05.png)

    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.

    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.

    c. Fare clic su **Next**.

6.  Nella pagina della finestra di dialogo **Profilo utente** seguire questa procedura:

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-sansan-tutorial/create_aaduser_06.png)

    a. Nella casella di testo **Nome** digitare **Britta**.

    b. Nella casella di testo **Cognome** digitare **Simon**.

    c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.

    d. Nell'elenco **Ruolo**, selezionare **Utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-sansan-tutorial/create_aaduser_07.png)

8. Nella pagina **Ottieni password temporanea** seguire questa procedura:

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-sansan-tutorial/create_aaduser_08.png)

    a. Prendere nota del valore visualizzato in **Nuova password**.

    b. Fare clic su **Completa**.



### Creazione di un utente test di SanSan

In questa sezione viene creato un utente chiamato Britta Simon in SanSan. L'applicazione SanSan richiede che venga eseguito il provisioning dell'utente all'interno dell'applicazione prima di eseguire l'accesso Single Sign-On.


> [AZURE.NOTE] Se si deve creare un utente manualmente o creare un batch di utenti, è necessario contattare il team di supporto SanSan.


### Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a SanSan.

![Assegna utente][200]

**Per assegnare Britta Simon ad SanSan, seguire questa procedura:**

1. Per aprire la visualizzazione delle applicazioni nel portale classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.

	![Assegna utente][201]

2. Nell'elenco delle applicazioni, selezionare **SanSan**.

	![Configura accesso Single Sign-On](./media/active-directory-saas-sansan-tutorial/tutorial_sansan_50.png)

1. Scegliere **Utenti** dal menu in alto.

	![Assegna utente][203]

1. Nell'elenco di utenti selezionare **Britta Simon**.

2. Fare clic su **Assegna** sulla barra degli strumenti in basso.

	![Assegna utente][205]


### Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Microsoft Azure AD usando il pannello di accesso. Quando si fa clic sul riquadro SanSan nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione SanSan.


## Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0817_2016-->