<properties
	pageTitle="Esercitazione: Integrazione di Azure Active Directory con Alcumus Info Exchange | Microsoft Azure"
	description="Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Alcumus Info Exchange."
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
	ms.date="12/18/2015"
	ms.author="jeedes"/>


# Esercitazione: Integrazione di Azure Active Directory con Alcumus Info Exchange

L'obiettivo di questa esercitazione è illustrare come integrare Alcumus Info Exchange con Azure Active Directory (Azure AD).<br>L'integrazione di Alcumus Info Exchange con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere ad Alcumus Info Exchange. 
- È possibile abilitare gli utenti per l'accesso automatico ad Alcumus Info Exchange (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in una posizione centrale, il portale di Azure Active Directory.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Prerequisiti 

Per configurare l'integrazione di Azure AD con Alcumus Info Exchange, sono necessari gli elementi seguenti:

- Sottoscrizione di [Azure AD](https://azure.microsoft.com/).
- Sottoscrizione di [Alcumus Info Exchange](http://www.alcumusgroup.com/) abilitata per l'accesso Single Sign-On.


> [AZURE.NOTE] Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non è disponibile un ambiente di prova di Azure AD, è possibile ottenere una versione di prova di un mese [qui](https://azure.microsoft.com/pricing/free-trial/). 

 
## Descrizione dello scenario
L'obiettivo di questa esercitazione è testare l'accesso Single Sign-On di Azure AD in un ambiente di test. <br> Lo scenario descritto in questa esercitazione prevede i tre blocchi predefiniti seguenti:

1. Aggiunta di Alcumus Info Exchange dalla raccolta 
2. Configurazione e test dell'accesso Single Sign-On di Azure AD


## Aggiunta di Alcumus Info Exchange dalla raccolta
Per configurare l'integrazione di Alcumus Info Exchange in Azure AD, è necessario aggiungere Alcumus Info Exchange dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Alcumus Info Exchange dalla raccolta, seguire questa procedura:**

1. Nel **portale di gestione di Azure** fare clic su **Active Directory** nel riquadro di spostamento sinistro. <br><br>![Active Directory][1]<br>

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto. <br><br>![Applicazioni][2]<br>

4. Fare clic su **Aggiungi** nella parte inferiore della pagina. <br><br>![Applicazioni][3]<br>

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungi un'applicazione dalla raccolta**. <br><br>![Applicazioni][4]<br>

6. Nella casella di ricerca digitare **Alcumus Info Exchange**. <br><br>![Applicazioni][5]<br>

7. Nel riquadro dei risultati selezionare **Alcumus Info Exchange** e quindi fare clic su **Completa** per aggiungere l'applicazione. <br><br>![Applicazioni][400]<br>



##  Configurazione e test dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come configurare e testare l'accesso Single Sign-On di Azure AD con Alcumus Info Exchange in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Alcumus Info Exchange che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Alcumus Info Exchange.<br> La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** in Alcumus Info Exchange.
 
Per configurare e testare l'accesso Single Sign-On di Azure AD con Alcumus Info Exchange, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Creazione di un utente test di Alcumus Info Exchange](#creating-a-alcumus-info-exchange-test-user)**: per avere una controparte di Britta Simon in Alcumus Info Exchange collegata alla relativa rappresentazione in Azure AD.
5. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene descritto come abilitare Single Sign-On di Azure AD nel portale di Azure AD e configurare l'accesso Single Sign-On nell'applicazione Alcumus Info Exchange.<br>

**Per configurare Single Sign-On di Azure AD con Alcumus Info Exchange, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Alcumus Info Exchange** del portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.<br><br> ![Configura accesso Single Sign-On][6]

2. Nella pagina **Stabilire come si desidera che gli utenti accedano ad Alcumus Info Exchange** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.<br><br> ![Accesso Single Sign-On di Azure AD][7]

3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura: <br><br>![Accesso Single Sign-On di Azure AD][8]<br>
 
     3\.1 Nella casella di testo **URL di risposta** digitare l'URL del consumer configurato dal team di supporto di Alcumus Info Exchange.

     >[AZURE.NOTE] Se non si conosce il valore corretto, contattare il team di supporto di Alcumus Info Exchange tramite [helpdesk@alcumusgroup.com](mailto:helpdesk@alcumusgroup.com).

     3\.2. Fare clic su **Avanti**.
 
4. Nella pagina **Configura accesso Single Sign-On in Alcumus Info Exchange** fare clic su **Scarica metadati** e quindi salvare il file di metadati localmente nel computer.<br><br>![Cos'è Azure AD Connect][9]

5. Contattare il team di supporto di Alcumus Info Exchange tramite [helpdesk@alcumusgroup.com](mailto:helpdesk@alcumusgroup.com), fornire il file di metadati e chiedere di abilitare SSO.


6. Nel portale di Azure AD selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**. <br><br>![Cos'è Azure AD Connect][10]

7. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**. <br><br>![Cos'è Azure AD Connect][11]




### Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test nel portale di Azure denominato Britta Simon.<br> Nell'elenco di utenti selezionare **Britta Simon**.<br><br>![Creare un utente di Azure AD][20]<br>

**Per creare un utente test in Azure AD, seguire questa procedura:**

1. Nel **portale di gestione di Azure** fare clic su **Active Directory** nel pannello di navigazione sinistro.<br> ![Creazione di un utente test di Azure AD](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_02.png) 

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per visualizzare l'elenco di utenti, nel menu in alto fare clic su **Utenti**.<br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_03.png)
 
4. Per aprire la finestra di dialogo **Aggiungi utente**, nella barra degli strumenti in basso fare clic su **Aggiungi utente**. <br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_04.png)

5. Nella pagina **Informazioni sull'utente** seguire questa procedura: <br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_05.png)
  1. In Tipo di utente selezionare Nuovo utente nell'organizzazione.
  2. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
  3. Fare clic su Avanti.

6.  Nella pagina **Profilo utente** seguire questa procedura: <br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_06.png)
  1. Nella casella di testo **Nome** digitare **Britta**.  
  2. Nella casella di testo **Cognome** digitare **Simon**.
  3. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
  4. Nell'elenco **Ruolo** selezionare **Utente**.
  5. Fare clic su **Avanti**.

7. Nella pagina **Ottieni password temporanea** fare clic su **Crea**.<br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_07.png)
 
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:<br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_08.png)
  1. Prendere nota del valore in **Nuova password**.
  2. Fare clic su **Operazione completata**.   

  
 
### Creazione di un utente test di Alcumus Info Exchange

Questa sezione descrive come creare un utente chiamato Britta Simon in Alcumus Info Exchange.

**Per creare un utente test denominato Britta Simon in Alcumus Info Exchange, seguire questa procedura:**

1. Contattare il team di supporto di Alcumus Info Exchange tramite [helpdesk@alcumusgroup.com](mailto:helpdesk@alcumusgroup.com).


### Assegnazione dell'utente test di Azure AD

Questa sezione descrive come abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad Alcumus Info Exchange. <br><br>![Assegna utente][200]

**Per assegnare Britta Simon ad Alcumus Info Exchange, seguire questa procedura:**

1. Per aprire la visualizzazione applicazioni nel portale di Azure, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.<br> <br><br>![Assegna utente][201]
2. Nell'elenco delle applicazioni selezionare **Alcumus Info Exchange**. <br><br>![Assegna utente][202]
1. Nel menu in alto fare clic su **Utenti**.<br> <br><br>![Assegna utente][203]
1. Nell'elenco utenti selezionare **Britta Simon**.

2. Fare clic su **Assegna** sulla barra degli strumenti in basso. <br><br>![Assegna utente][205]



### Test dell'accesso Single Sign-On

L'obiettivo di questa sezione è testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.<br> Quando si fa clic sul riquadro Alcumus Info Exchange nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Alcumus Info Exchange.


## Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_01.png
[6]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_02.png
[7]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_03.png
[8]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_04.png
[9]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_05.png
[10]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_06.png
[11]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_07.png
[20]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_08.png
[203]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_205.png
[400]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_402.png

<!---HONumber=AcomDC_0128_2016-->