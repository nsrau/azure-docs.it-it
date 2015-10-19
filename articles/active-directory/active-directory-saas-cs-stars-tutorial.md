<properties
	pageTitle="Esercitazione: Integrazione di Azure Active Directory con CS Stars | Microsoft Azure"
	description="Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e CS Stars."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/06/2015"
	ms.author="markusvi"/>


# Esercitazione: Integrazione di Azure Active Directory con CS Stars

L'obiettivo di questa esercitazione è illustrare come integrare CS Stars con Azure Active Directory (Azure AD).<br>L'integrazione di CS Stars con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a CS Stars. 
- È possibile abilitare gli utenti per l'accesso automatico a CS Stars (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in una posizione centrale, il portale di Azure Active Directory.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Prerequisiti 

Per configurare l'integrazione di Azure AD con CS Stars, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di CS Stars abilitata per l'accesso Single Sign-On.


> [AZURE.NOTE]Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/). 

 
## Descrizione dello scenario
L'obiettivo di questa esercitazione è testare l'accesso Single Sign-On di Azure AD in un ambiente di test. <br> Lo scenario descritto in questa esercitazione prevede i tre blocchi predefiniti seguenti:

1. Aggiunta di CS Stars dalla raccolta 
2. Configurazione e test dell'accesso Single Sign-On di Azure AD


## Aggiunta di CS Stars dalla raccolta
Per configurare l'integrazione di CS Stars in Azure AD, è necessario aggiungere CS Stars dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere CS Stars dalla raccolta, seguire questa procedura:**

1. Nel **portale di gestione di Azure** fare clic su **Active Directory** nel riquadro di spostamento sinistro. <br><br>![Active Directory][1]<br>

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto. <br><br>![Applicazioni][2]<br>

4. Fare clic su **Aggiungi** nella parte inferiore della pagina. <br><br>![Applicazioni][3]<br>

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungi un'applicazione dalla raccolta**. <br><br>![Applicazioni][4]<br>

6. Nella casella di ricerca digitare **CS Stars**. <br><br>![Applicazioni][5]<br>

7. Nel riquadro dei risultati selezionare **CS Stars** e quindi fare clic su **Completa** per aggiungere l'applicazione. <br><br>![Applicazioni][400]<br>



##  Configurazione e test dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come configurare e testare l'accesso Single Sign-On di Azure AD con CS Stars in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di CS Stars che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in CS Stars.<br> La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** in CS Stars.
 
Per configurare e testare l'accesso Single Sign-On di Azure AD con CS Stars, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Creazione di un utente test di CS Stars](#creating-a-cs-stars-test-user)**: per avere una controparte di Britta Simon in CS Stars collegata alla relativa rappresentazione in Azure AD.
5. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene descritto come abilitare Single Sign-On di Azure AD nel portale di Azure AD e configurare l'accesso Single Sign-On nell'applicazione CS Stars.<br>

**Per configurare Single Sign-On di Azure AD con CS Stars, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **CS Stars** del portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**. <br><br>![Configura accesso Single Sign-On][6]<br>

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a CS Stars** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**. <br><br>![Accesso Single Sign-On di Azure AD][7]<br>

3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura: <br><br>![Configurare le impostazioni dell'app][8]<br>
 
     3\.1 Nella casella di testo **URL di accesso** digitare l'URL usato dagli utenti per accedere all'applicazione CS Stars (ad esempio: **https://uat.csstars.com/enterprise/default.cmdx?ssoclient=C234UAT2*).

     >[AZURE.NOTE]Se non si conosce il valore corretto, contattare il rappresentante di Marsh ClearSight.

     3\.2. Fare clic su **Avanti**.
 
4. Nella pagina **Configura accesso Single Sign-On in CS Stars** fare clic su **Scarica metadati** e quindi salvare il file di metadati in locale nel computer. <br><br>![Cos'è Azure AD Connect][9]<br>

5. Per ottenere l'abilitazione dell'accesso Single Sign-On per CS Stars, contattare il rappresentante di Marsh ClearSight e fornire il file di metadati.


6. Nel portale di Azure AD selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**. <br><br>![Cos'è Azure AD Connect][10]<br>

7. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**. <br><br>![Cos'è Azure AD Connect][11]<br>




### Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test nel portale di Azure chiamato Britta Simon.<br> Nell'elenco di utenti selezionare **Britta Simon**. <br><br>![Creare un utente di Azure AD][20]<br>

**Per creare un utente test in Azure AD, seguire questa procedura:**

1. Nel **portale di gestione di Azure** fare clic su **Active Directory** nel riquadro di spostamento sinistro. <br><br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_02.png)<br> 

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per visualizzare l'elenco di utenti, nel menu in alto fare clic su **Utenti**. <br><br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_03.png)<br>
 
4. Per aprire la finestra di dialogo **Aggiungi utente**, nella barra degli strumenti in basso fare clic su **Aggiungi utente**. <br><br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_04.png)<br>

5. Nella pagina **Informazioni sull'utente** seguire questa procedura: <br><br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_05.png)<br>
  1. In Tipo di utente selezionare Nuovo utente nell'organizzazione.
  2. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
  3. Fare clic su Avanti.

6.  Nella pagina **Profilo utente** seguire questa procedura: <br><br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_06.png)<br>
  1. Nella casella di testo **Nome** digitare **Britta**.  
  2. Nella casella di testo **Cognome** digitare **Simon**.
  3. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
  4. Nell'elenco **Ruolo** selezionare **Utente**.
  5. Fare clic su **Avanti**.

7. Nella pagina **Ottieni password temporanea** fare clic su **Crea**. <br><br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_07.png)<br>
 
8. Nella pagina **Ottieni password temporanea** seguire questa procedura: <br><br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_08.png)<br>
  1. Prendere nota del valore in **Nuova password**.
  2. Fare clic su **Complete**.   

  
 
### Creazione di un utente di test CS Stars

Questa sezione descrive come creare un utente chiamato Britta Simon in CS Stars.

**Per creare un utente test denominato Britta Simon in CS Stars, seguire questa procedura:**

1. Contattare il rappresentante di Marsh ClearSight.


### Assegnazione dell'utente test di Azure AD

L'obiettivo di questa sezione consiste nell'abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a CS Stars. <br><br>![Assegna utente][200]<br>

**Per assegnare Britta Simon a CS Stars, seguire questa procedura:**

1. Per aprire la visualizzazione applicazioni nel portale di Azure, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto. <br><br>![Assegna utente][201]<br>
2. Nell'elenco delle applicazioni selezionare **CS Stars**. <br><br>![Assegna utente][202]<br>
1. Nel menu in alto fare clic su **Utenti**.<br> <br><br>![Assegna utente][203]<br>
1. Nell'elenco di utenti selezionare **Britta Simon**.

2. Sulla barra degli strumenti in basso fare clic su **Assegna**. <br><br>![Assegna utente][205]<br>



### Test dell'accesso Single Sign-On

L'obiettivo di questa sezione è testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.<br> Quando si fa clic sul riquadro CS Stars nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione CS Stars.


## Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_01.png
[6]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_02.png
[7]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_03.png
[8]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_04.png
[9]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_05.png
[10]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_06.png
[11]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_07.png
[20]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_202.png
[203]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_205.png

[400]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_403.png

<!---HONumber=Oct15_HO2-->