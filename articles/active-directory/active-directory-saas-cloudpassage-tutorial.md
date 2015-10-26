<properties
	pageTitle="Esercitazione: Integrazione di Azure Active Directory con CloudPassage | Microsoft Azure"
	description="Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e CloudPassage."
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
	ms.date="08/26/2015"
	ms.author="markvi"/>


# Esercitazione: Integrazione di Azure Active Directory con CloudPassage

L'obiettivo di questa esercitazione è illustrare come integrare CloudPassage con Azure Active Directory (Azure AD).<br>L'integrazione di CloudPassage con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a CloudPassage. 
- È possibile abilitare gli utenti per l'accesso automatico a CloudPassage (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in una posizione centrale, il portale di Azure Active Directory.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Prerequisiti 

Per configurare l'integrazione di Azure AD con CloudPassage, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di CloudPassage abilitata per l'accesso Single Sign-On


> [AZURE.NOTE]Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non si dispone di un ambiente di test di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/). 

 
## Descrizione dello scenario
L'obiettivo di questa esercitazione è testare l'accesso Single Sign-On di Azure AD in un ambiente di test. <br> Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di CloudPassage dalla raccolta 
2. Configurazione e test dell'accesso Single Sign-On di Azure AD


## Aggiunta di CloudPassage dalla raccolta
Per configurare l'integrazione di CloudPassage in Azure AD, è necessario aggiungere CloudPassage dalla raccolta al proprio elenco di app SaaS gestite.

### Per aggiungere CloudPassage dalla raccolta, seguire questa procedura:

1. Nel **portale di gestione di Azure** fare clic su **Active Directory** nel pannello di navigazione sinistro. <br><br> ![Active Directory][1]

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applicazioni** nel menu superiore.<br><br> ![Applicazioni][2]
4. Fare clic su **Aggiungi** nella parte inferiore della pagina.<br><br> ![Applicazioni][3]
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungi un'applicazione dalla raccolta**.<br><br> ![Applicazioni][4]
6. Nella casella di ricerca digitare **CloudPassage**.<br><br> ![Applicazioni][5]
7. Nel riquadro dei risultati selezionare **CloudPassage** e quindi fare clic su **Completa** per aggiungere l'applicazione.<br><br> ![Applicazioni][6]



##  Configurazione e test dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come configurare e testare l'accesso Single Sign-On di Azure AD con CloudPassage in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di CloudPassage che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in CloudPassage.<br> La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **nome utente** in CloudPassage.
 
Per configurare e testare l'accesso Single Sign-On di Azure AD con CloudPassage, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Creazione di un utente test di CloudPassage](#creating-a-halogen-software-test-user)**: per avere una controparte di Britta Simon in CloudPassage collegata alla relativa rappresentazione in Azure AD.
5. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene descritto come abilitare Single Sign-On di Azure AD nel portale di Azure AD e configurare l'accesso Single Sign-On nell'applicazione CloudPassage.<br> L'applicazione CloudPassage prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. La schermata seguente mostra un esempio di questa operazione. <br><br> ![Configura accesso Single Sign-On][21]

**Per configurare Single Sign-On di Azure AD con CloudPassage, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **CloudPassage** del portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.<br><br> ![Configura accesso Single Sign-On][7]

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a CloudPassage** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.<br><br> ![Configura accesso Single Sign-On][8]

3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura: <br><br>![Configurare le impostazioni dell'app][9]
 
     3\.1. Nella casella di testo **URL di accesso** digitare l'URL usato dagli utenti per accedere all'applicazione CloudPassage, ad esempio: **https://portal.cloudpassage.com/saml/init/accountid*).

     3\.2. Nella casella di testo URL di risposta digitare l'URL AssertionConsumerService, ad esempio: **https://portal.cloudpassage.com/saml/consume/accountid*). <br> È possibile ottenere il valore di questo attributo facendo clic su **SSO Setup documentation** nella sezione **Single Sign-On Settings** del portale CloudPassage. <br><br>![Configura accesso Single Sign-On][10]

     3\.3. Fare clic su **Next**.



4. Nella pagina **Configura accesso Single Sign-On in CloudPassage** fare clic su **Download certificato** e infine salvare il certificato localmente nel computer. <br><br>![Configura accesso Single Sign-On][11]

5. In un'altra finestra del Web browser accedere al sito aziendale di CloudPassage come amministratore.

6. Nel menu in alto fare clic su **Settings** e quindi su **Site Administration**. <br><br> ![Configura accesso Single Sign-On][12]

7. Fare clic sulla scheda **Authentication Settings**. <br><br> ![Configura accesso Single Sign-On][13]


8. Nella sezione **Single Sign-On Settings** seguire questa procedura: <br><br> ![Configura accesso Single Sign-On][14]


     8\.1 Nella finestra di dialogo **Configura accesso Single Sign-On in CloudPassage** del portale di Azure copiare il valore di **URL autorità di certificazione** e incollarlo nella casella di testo **Issuer URL**.

     8\.2. Nella finestra di dialogo **Configura accesso Single Sign-On in CloudPassage** del portale di Azure copiare il valore di **Endpoint inizializzato da provider di servizi** e incollarlo nella casella di testo **EndPoint URL SAML**.

     8\.3. Nella finestra di dialogo **Configura accesso Single Sign-On in CloudPassage** del portale di Azure copiare il valore di **URL disconnessione** e incollarlo nella casella di testo **Pagina di destinazione disconnessione**.

     8\.4. Creare un file con codifica **Base 64** dal certificato scaricato.
          
      >[AZURE.TIP]Per informazioni dettagliate, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

     8\.5. Aprire il certificato con codifica Base 64 nel Blocco note, copiarne il contenuto negli Appunti e incollarlo nella casella di testo **x 509 certificate**.

     8\.6. Fare clic su **Save**.


9. Nel portale di Azure AD selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**. <br><br> ![Configura accesso Single Sign-On][15]


10. Nella pagina **Conferma Single Sign-On** fare clic su **Completa**. <br><br> ![Configura accesso Single Sign-On][16]



11. Nel menu in alto fare clic su **Attributi** per aprire la finestra di dialogo **Attributi token SAML**. <br><br> ![Configura accesso Single Sign-On][17]

12. Per aggiungere gli attributi utente necessari, per ciascuna riga della tabella seguire questa procedura: <br>

| Nome attributo | Valore attributo |
| --- | --- |
| firstname | user.givenname |
| lastname | user.surname |
| email | user.mail |  

     12.1. Click **add user attribute**. <br><br> ![Configure Single Sign-On][18]

     12.2. In the **Attribute Name** textbox, type the attribute name shown for that row and as **Attribure Value**, select the attribute value shown for that row . <br><br> ![Configure Single Sign-On][19]
     
     12.2.3 Click **Complete**.


13. Scegliere quindi **Applica modifiche** dal menu inferiore. <br><br> ![Configura accesso Single Sign-On][20]



### Creazione di un utente test di Azure AD

Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di Azure.<br><br> Nell'elenco utenti selezionare **Britta Simon**.<br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_01.png)

**Per creare un utente test in Azure AD, seguire questa procedura:**

1. Nel **portale di gestione di Azure** fare clic su **Active Directory** nel pannello di navigazione sinistro.<br> ![Creazione di un utente test di Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_02.png) 

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per visualizzare l'elenco di utenti, nel menu in alto fare clic su **Utenti**.<br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_03.png)
 
4. Per aprire la finestra di dialogo **Aggiungi utente**, nella barra degli strumenti in basso fare clic su **Aggiungi utente**. <br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_04.png)

5. Nella pagina **Informazioni sull'utente** seguire questa procedura: <br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_05.png)
  1. In Tipo di utente selezionare Nuovo utente nell'organizzazione.
  2. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
  3. Fare clic su Avanti.

6.  Nella pagina **Profilo utente** seguire questa procedura: <br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_06.png)
  1. Nella casella di testo **Nome** digitare **Britta**.  
  2. Nella casella di testo **Cognome** digitare **Simon**.
  3. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
  4. Nell'elenco **Ruolo** selezionare **Utente**.
  5. Fare clic su **Avanti**.

7. Nella pagina **Ottieni password temporanea** fare clic su **Crea**.<br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_07.png)
 
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:<br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_08.png)
  1. Prendere nota del valore in **Nuova password**.
  2. Fare clic su **Complete**.   


  
 
### Creazione di un utente test CloudPassage

Questa sezione descrive come creare un utente chiamato Britta Simon in CloudPassage.

#### Per creare un utente test denominato Britta Simon in CloudPassage, seguire questa procedura:

1.	Accedere al sito aziendale di **CloudPassage** come amministratore. 

2.	Nella barra degli strumenti in alto fare clic su **Settings** e quindi su **Site Administration**. <br>![Creazione di un utente test CloudPassage][22]

3.	Fare clic sulla scheda **Users** e quindi fare clic su **Add New User**. <br>![Creazione di un utente test CloudPassage][23]
	
4.	Nella sezione **Add New User** seguire questa procedura: <br>![Creazione di un utente test CloudPassage][24]

     4\.1. Nella casella di testo **Nome** digitare Britta.

     4\.2. Nella casella di testo **Cognome** digitare Simon.

     4\.3. Nelle caselle di testo **Username**, **Email** e **Retype Email** digitare il nome utente di Britta in Azure AD.

     4\.4. In **Access Type** selezionare **Enable Halo Portal Access**.

     4\.5. Fare clic su **Add**.










### Assegnazione dell'utente test di Azure AD

Questa sezione descrive come abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a CloudPassage. <br><br>![Assegna utente][30]

**Per assegnare Britta Simon a CloudPassage, seguire questa procedura:**

1. Per aprire la visualizzazione applicazioni nel portale di Azure, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.<br> <br><br>![Assegna utente][26]
2. Nell'elenco delle applicazioni selezionare **CloudPassage**. <br><br>![Assegna utente][27]
1. Nel menu in alto fare clic su **Utenti**.<br> <br><br>![Assegna utente][25]
1. Nell'elenco utenti selezionare **Britta Simon**.

2. Sulla barra degli strumenti in basso fare clic su **Assegna**. <br><br>![Assegna utente][29]



### Test dell'accesso Single Sign-On

Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.<br> Quando si fa clic sul riquadro CloudPassage nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione CloudPassage.


## Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_01.png
[6]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_02.png
[7]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_05.png
[8]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_03.png
[9]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_04.png
[10]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_05.png
[11]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_06.png
[12]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_10.png
[16]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_11.png
[17]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_10.png
[18]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_11.png
[19]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_12.png
[20]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_14.png
[21]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_14.png
[22]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_17.png
[25]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_15.png
[26]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_12.png
[27]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_13.png
[28]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_15.png
[29]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_16.png
[30]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_17.png

<!---HONumber=Oct15_HO3-->