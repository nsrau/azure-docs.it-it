<properties
	pageTitle="Esercitazione: Integrazione di Azure Active Directory con Intralinks | Microsoft Azure"
	description="Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Intralinks."
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


# Esercitazione: Integrazione di Azure Active Directory con Intralinks

Questa esercitazione descrive come integrare Intralinks con Azure Active Directory (Azure AD).

L'integrazione di Intralinks con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Intralinks
- È possibile abilitare gli utenti per l'accesso automatico a Intralinks (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Prerequisiti

Per configurare l'integrazione di Azure AD con Intralinks, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Intralinks abilitata per l'accesso Single Sign-On.


> [AZURE.NOTE] Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non è disponibile un ambiente di prova di Azure AD, è possibile ottenere una versione di prova di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).


## Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Intralinks dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD


## Aggiunta di Intralinks dalla raccolta
Per configurare l'integrazione di Intralinks in Azure AD, è necessario aggiungere Intralinks dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Intralinks dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.

	![Active Directory][1]

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

	![Applicazioni][2]

4. Fare clic su **Add** nella parte inferiore della pagina.

	![Applicazioni][3]

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

	![Applicazioni][4]

6. Nella casella di ricerca digitare **Intralinks**.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_01.png)

7. Nel riquadro dei risultati selezionare **Intralinks** e quindi fare clic su **Completa** per aggiungere l'applicazione.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_02.png)


##  Configurazione e test dell'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Intralinks con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Intralinks che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Intralinks.

La relazione di collegamento viene stabilita assegnando al valore del **nome utente** in Azure AD lo stesso valore di **Username** in Intralinks.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Intralinks, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Creazione di un utente test di Intralinks](#creating-an-intralinks-test-user)**: per avere una controparte di Britta Simon in Intralinks collegata alla relativa rappresentazione in Azure AD.
5. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure classico e viene configurato l'accesso Single Sign-On nell'applicazione Intralinks.


**Per configurare l'accesso Single Sign-On di Azure AD con Intralinks, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Intralinks** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
	 
	![Configura accesso Single Sign-On][6]

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Intralinks** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.

	![Configura accesso Single Sign-On](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_03.png)

3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:

	![Configura accesso Single Sign-On](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_04.png)

    a. Nella casella di testo **URL accesso** digitare l'URL usato dagli utenti per accedere all'applicazione Intralinks usando il modello seguente: **https://\<nome società>.Intralinks.com/?PartnerIdpId=https://sts.windows.net/<ID tenant Azure AD>/**.

    b. Fare clic su **Avanti**.
	
4. Nella pagina **Configura accesso Single Sign-On in Intralinks** seguire questa procedura:

	![Configura accesso Single Sign-On](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_05.png)

    a. Fare clic su **Scarica metadati** e quindi salvare il file nel computer.

    b. Fare clic su **Avanti**.


5. Per ottenere SSO configurato per l'applicazione, contattare il team di supporto di Intralinks e inviare per posta elettronica il file di metadati scaricato.


6. Nel portale classico selezionare la conferma della configurazione dell'accesso Single Sign-On e fare clic su **Avanti**.
	
	![Accesso Single Sign-On di Azure AD][10]

7. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.
 
	![Accesso Single Sign-On di Azure AD][11]


### Creazione di un utente test di Azure AD
In questa sezione viene creato un utente test chiamato Britta Simon nel portale classico.

Nell'elenco di utenti selezionare **Britta Simon**.

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_09.png)

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per visualizzare l'elenco di utenti, fare clic su **Utenti** nel menu in alto.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_03.png)

4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_04.png)

5. Nella pagina della finestra di dialogo **Informazioni sull'utente** seguire questa procedura: ![Creazione di un utente test di Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_05.png)

    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.

    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina della finestra di dialogo **Profilo utente** seguire questa procedura: ![Creazione di un utente test di Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_06.png)

    a. Nella casella di testo **Nome** digitare **Britta**.

    b. Nella casella di testo **Cognome** digitare **Simon**.

    c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.

    d. Nell'elenco **Ruolo**, selezionare **Utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_07.png)

8. Nella pagina **Ottieni password temporanea** seguire questa procedura:

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_08.png)

    a. Prendere nota del valore visualizzato in **Nuova password**.

    b. Fare clic su **Complete**.



### Creazione di un utente test di Intralinks

In questa sezione viene creato un utente chiamato Britta Simon in Intralinks. Collaborare con il team di supporto di Intralinks per aggiungere gli utenti nella piattaforma Intralinks.


### Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Intralinks.

![Assegna utente][200]

**Per assegnare Britta Simon a Intralinks, seguire questa procedura:**

1. Per aprire la visualizzazione delle applicazioni nel portale classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.

	![Assegna utente][201]

2. Nell'elenco di applicazioni selezionare **Intralinks**.

	![Configura accesso Single Sign-On](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_50.png)

3. Scegliere **Utenti** dal menu in alto.

	![Assegna utente][203]

4. Nell'elenco di utenti selezionare **Britta Simon**.

5. Fare clic su **Assegna** sulla barra degli strumenti in basso.

	![Assegna utente][205]

### Aggiunta di un'applicazione Intralinks VIA o Elite
Intralinks usa la stessa piattaforma di identità Single Sign On per tutte le altre applicazioni Intralinks esclusa applicazione Deal Nexus. Se si prevede di usare qualsiasi altra applicazione Intralinks, è necessario configurare prima di tutto l'accesso Single Sign On per un'applicazione di Intralinks primaria usando la procedura descritta sopra.

Si potrà quindi usare la procedura seguente per aggiungere un'altra applicazione Intralinks nel tenant che può sfruttare l'applicazione primaria per Single Sign-On.

> [AZURE.NOTE] Questa funzionalità è disponibile solo per i clienti degli SKU di Azure AD Premium e non per i clienti di clienti SKU Basic o Gratuito.

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.

	![Active Directory][1]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

	![Applicazioni][2]

4. Fare clic su **Add** nella parte inferiore della pagina.

	![Applicazioni][3]

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

	![Applicazioni][4]

6. Dalla scheda a sinistra fare clic sulla scheda **Personalizzata**

	![Aggiunta di un'applicazione Intralinks VIA o Elite](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_51.png)

7. Assegnare un nome appropriato all'applicazione, ad esempio **Intralinks Elite** e fare clic su pulsante Fine.

8. Fare clic sul pulsante **Configura accesso Single Sign-On**.

9. Selezionare l'opzione **Accesso Single Sign-On esistente**.

	![Aggiunta di un'applicazione Intralinks VIA o Elite](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_52.png)

10. Ottenere l'URL SSO avviato dal provider di servizi dal team Intralinks per l'altra applicazione Intralinks e immetterlo come illustrato di seguito.

	![Aggiunta di un'applicazione Intralinks VIA o Elite](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_53.png)

	a. Nella casella di testo URL accesso digitare l'URL usato dagli utenti per accedere all'applicazione Intralinks usando il modello seguente: **https://\<NomeSocietà>.Intralinks.com/?PartnerIdpId=https://sts.windows.net/<IDTenantAzureAD>/**.


11. Fare clic su **Avanti**.

12. Assegnare l'applicazione a un utente o a gruppi, come illustrato nella sezione **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)**


### Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Intralinks nel pannello di accesso, si accede automaticamente all'applicazione Intralinks.


## Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0518_2016-->