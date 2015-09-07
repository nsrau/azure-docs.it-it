<properties
	pageTitle="Esercitazione: Integrazione di Azure Active Directory con Amazon Web Service (AWS) | Microsoft Azure"
	description="Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Amazon Web Service (AWS)."
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
	ms.date="08/27/2015"
	ms.author="markvi"/>


# Esercitazione: Integrazione di Azure Active Directory con Amazon Web Service (AWS)

L'obiettivo di questa esercitazione è illustrare come integrare Amazon Web Service (AWS) con Azure Active Directory (Azure AD).<br>L'integrazione di Amazon Web Service (AWS) con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere ad Amazon Web Service (AWS). 
- È possibile abilitare gli utenti per l'accesso automatico ad Amazon Web Service (AWS) (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in una posizione centrale, il portale di Azure Active Directory.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Prerequisiti 

Per configurare l'integrazione di Azure AD con Amazon Web Service (AWS), sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione Amazon Web Service (AWS) abilitata per l'accesso Single Sign-On.


> [AZURE.NOTE]Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/). 

 
## Descrizione dello scenario
L'obiettivo di questa esercitazione è testare l'accesso Single Sign-On di Azure AD in un ambiente di test. <br> Lo scenario descritto in questa esercitazione prevede i tre blocchi predefiniti seguenti:

1. Aggiunta di Amazon Web Service (AWS) dalla raccolta 
2. Configurazione e test dell'accesso Single Sign-On di Azure AD


## Aggiunta di Amazon Web Service (AWS) dalla raccolta
Per configurare l'integrazione di Amazon Web Service (AWS) in Azure AD, è necessario aggiungere Amazon Web Service (AWS) dalla raccolta al proprio elenco di app SaaS gestite.

### Per aggiungere Amazon Web Service (AWS) dalla raccolta, seguire questa procedura:

1. Nel **portale di gestione di Azure** fare clic su **Active Directory** nel pannello di navigazione sinistro. <br><br> ![Active Directory][1]

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applicazioni** nel menu superiore.<br><br> ![Applicazioni][2]
4. Fare clic su **Aggiungi** nella parte inferiore della pagina.<br><br> ![Applicazioni][3]
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungi un'applicazione dalla raccolta**.<br><br> ![Applicazioni][4]
6. Nella casella di ricerca digitare **Amazon Web Service (AWS)**.<br><br> ![Applicazioni][5]
7. Nel riquadro dei risultati selezionare **Amazon Web Service (AWS)** e quindi fare clic su **Completa** per aggiungere l'applicazione.<br><br> ![Applicazioni][6]



##  Configurazione e test dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come configurare e testare l'accesso Single Sign-On di Azure AD con Amazon Web Service (AWS) in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Amazon Web Service (AWS) che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Amazon Web Service (AWS).<br> La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **nome utente** in Amazon Web Service (AWS).
 
Per configurare e testare l'accesso Single Sign-On di Azure AD con Amazon Web Service (AWS), è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Creazione di un utente test di Amazon Web Service (AWS)](#creating-a-halogen-software-test-user)**: per avere una controparte di Britta Simon in Amazon Web Service (AWS) collegata alla relativa rappresentazione in Azure AD.
5. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### Configurazione dell'accesso Single Sign-On di Azure AD

Questa sezione descrive come abilitare Single Sign-On di Azure AD nel portale di Azure AD e configurare l'accesso Single Sign-On nell'applicazione Amazon Web Service (AWS).<br> L'applicazione Amazon Web Service (AWS) prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione di **Attributi token SAML**. La schermata seguente mostra un esempio di questa operazione. <br><br> ![Configura accesso Single Sign-On][27]

**Per configurare Single Sign-On di Azure AD con Amazon Web Service (AWS), seguire questa procedura:**

1. Nel portale di Azure AD, nella pagina di integrazione dell'applicazione **Amazon Web Service (AWS)**, fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.<br><br> ![Configura accesso Single Sign-On][7]

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Amazon Web Service (AWS)** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.<br><br> ![Configura accesso Single Sign-On][8]

3. Nella pagina **Configurare le impostazioni dell'app** fare clic su Avanti. <br><br>![Configurare le impostazioni dell'app][9]
 
4. Nella pagina **Configura accesso Single Sign-On in Amazon Web Service (AWS)** fare clic su **Scarica metadati** e quindi salvare il file di metadati in locale nel computer.<br><br>![Configura accesso Single Sign-On][10]

5. In un'altra finestra del Web browser accedere al sito aziendale di Amazon Web Service (AWS) come amministratore.

6. Fare clic su **Home console**. <br><br> ![Configura accesso Single Sign-On][11]

7. Fare clic su **Identity and Access Management**. <br><br> ![Configura accesso Single Sign-On][12]

8. Fare clic su **Identity Providers** e quindi fare clic su **Create Provider**. <br><br> ![Configura accesso Single Sign-On][13]

9. Nella pagina **Configure Provider** seguire questa procedura: <br><br>![Configura accesso Single Sign-On][14]

     9\.1. In **Provider Type** selezionare **SAML**.

     9\.2. Nella casella di testo **Provider Name** digitare un nome di provider, ad esempio *WAAD*.

     9\.3. Per caricare il file di metadati scaricato, fare clic su **Choose file**.

     9\.4. Fare clic su **Next Step**.


10. Nella finestra di dialogo **Verify Provider Information** fare clic su **Create**. <br><br> ![Configura accesso Single Sign-On][15]

11. Passare a **Roles > Create New Role**. <br><br> ![Configura accesso Single Sign-On][16]

12. Nella finestra di dialogo **Set Role Name** seguire questa procedura: <br><br> ![Configura accesso Single Sign-On][17] 12.1. Nella casella d testo **Role Name** digitare un nome, ad esempio *TestUser*.

     12\.2. Fare clic su **Next Step**.

13. Nella finestra di dialogo **Select Role Type** seguire questa procedura: <br><br> ![Configura accesso Single Sign-On][18]

     13\.1. Selezionare **Role For Identity Provider Access**.

     13\.2. Nella sezione **Grant Web Single Sign-On (WebSSO) access to SAML providers** fare clic su **Select**.


14. Nella finestra di dialogo Establish Trust seguire questa procedura: <br><br> ![Configura accesso Single Sign-On][19] 14.1. Come provider SAML selezionare quello creato in precedenza, ad esempio *WAAD*.



15. Nel portale di Azure AD selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**.<br><br>![Cos'è Azure AD Connect][20]

16. Nella pagina **Conferma Single Sign-On** fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.<br><br>![Cos'è Azure AD Connect][22]


17. Nel menu in alto fare clic su **Attributi** per aprire la finestra di dialogo **Attributi token SAML**. <br><br> ![Configura accesso Single Sign-On][21]

18. Fare clic su **Aggiungi attributo utente**. <br><br> ![Configura accesso Single Sign-On][23]

19. Nella finestra di dialogo Aggiungi attributo utente seguire questa procedura. <br><br> ![Configura accesso Single Sign-On][24]

     19\.1. Nella casella di testo **Nome attributo** digitare ****https://aws.amazon.com/SAML/Attributes/Role**.

     19\.2. Nella casella di testo **Valore attributo** digitare **arn:aws:iam::214510765665:role/Admin,arn:aws:iam::214510765665:saml-provider/WAAD**.

     19\.3. Fare clic su **Completa** per chiudere la finestra di dialogo **Aggiungi attributo utente**.

20. Fare clic su **Aggiungi attributo utente**. <br><br> ![Configura accesso Single Sign-On][23]


21. Nella finestra di dialogo Aggiungi attributo utente seguire questa procedura. <br><br> ![Configura accesso Single Sign-On][25]

     21\.1. Nella casella di testo **Nome attributo** digitare ****https://aws.amazon.com/SAML/Attributes/RoleSessionName**.

     21\.2. Nell'elenco **Valore attributo** selezionare **user:mail**.

     21\.3. Fare clic su **Completa** per chiudere la finestra di dialogo **Aggiungi attributo utente**.


22. Fare clic su **Applica modifiche**. <br><br> ![Configura accesso Single Sign-On][26]





### Creazione di un utente test di Azure AD

Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di Azure.<br> Nell'elenco utenti selezionare **Britta Simon**.<br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_01.png)

**Per creare un utente test in Azure AD, seguire questa procedura:**

1. Nel **portale di gestione di Azure** fare clic su **Active Directory** nel pannello di navigazione sinistro.<br> ![Creazione di un utente test di Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_02.png) 

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per visualizzare l'elenco di utenti, nel menu in alto fare clic su **Utenti**.<br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_03.png)
 
4. Per aprire la finestra di dialogo **Aggiungi utente**, nella barra degli strumenti in basso fare clic su **Aggiungi utente**. <br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_04.png)

5. Nella pagina **Informazioni sull'utente** seguire questa procedura: <br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_05.png)
  1. In Tipo di utente selezionare Nuovo utente nell'organizzazione.
  2. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
  3. Fare clic su Avanti.

6.  Nella pagina **Profilo utente** seguire questa procedura: <br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_06.png)
  1. Nella casella di testo **Nome** digitare **Britta**.  
  2. Nella casella di testo **Cognome** digitare **Simon**.
  3. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
  4. Nell'elenco **Ruolo** selezionare **Utente**.
  5. Fare clic su **Avanti**.

7. Nella pagina **Ottieni password temporanea** fare clic su **Crea**.<br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_07.png)
 
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:<br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_08.png)
  1. Prendere nota del valore in **Nuova password**.
  2. Fare clic su **Completa**.   
  
 
### Creazione di un utente test Amazon Web Service (AWS)

Questa sezione descrive come creare un utente chiamato Britta Simon in Amazon Web Service (AWS).

### Per creare un utente test denominato Britta Simon in Amazon Web Service (AWS), seguire questa procedura:

1. Accedere al sito aziendale di **Amazon Web Service (AWS)** come amministratore.

2. Fare clic sull'icona **Console Home**. <br><br> ![Configura accesso Single Sign-On][11]

3. Fare clic su Identity and Access Management. <br><br> ![Configura accesso Single Sign-On][28]

4. In Dashboard fare clic su Users e quindi fare clic su Create New Users. <br><br> ![Configura accesso Single Sign-On][29]

5. Nella finestra di dialogo Create User seguire questa procedura: <br><br> ![Configura accesso Single Sign-On][30]

     5\.1. Nelle caselle di testo **Enter User Names** digitare il nome utente di Britta Simon in Azure AD.

     5\.2. Fare clic su **Create**.




### Assegnazione dell'utente test di Azure AD

Questa sezione descrive come abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad Amazon Web Service (AWS). <br><br>![Assegna utente][31]

**Per assegnare Britta Simon ad Amazon Web Service (AWS), seguire questa procedura:**

1. Per aprire la visualizzazione applicazioni nel portale di Azure, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.<br> <br><br>![Assegna utente][26]
2. Nell'elenco delle applicazioni selezionare **Amazon Web Service (AWS)**. <br><br>![Assegna utente][27]
1. Nel menu in alto fare clic su **Utenti**.<br> <br><br>![Assegna utente][25]
1. Nell'elenco utenti selezionare **Britta Simon**.

2. Sulla barra degli strumenti in basso fare clic su **Assegna**. <br><br>![Assegna utente][29]

### Test dell'accesso Single Sign-On

Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.<br> Quando si fa clic sul riquadro Amazon Web Service (AWS) nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Amazon Web Service (AWS).


## Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-amazon-web-service/tutorial_general_01.png
[2]: ./media/active-directory-saas-amazon-web-service/tutorial_general_02.png
[3]: ./media/active-directory-saas-amazon-web-service/tutorial_general_03.png
[4]: ./media/active-directory-saas-amazon-web-service/tutorial_general_04.png
[5]: ./media/active-directory-saas-amazon-web-service/ic795019.png
[6]: ./media/active-directory-saas-amazon-web-service/ic795020.png
[7]: ./media/active-directory-saas-amazon-web-service/ic795027.png
[8]: ./media/active-directory-saas-amazon-web-service/ic795028.png
[9]: ./media/active-directory-saas-amazon-web-service/capture23.png
[10]: ./media/active-directory-saas-amazon-web-service/capture24.png
[11]: ./media/active-directory-saas-amazon-web-service/ic795031.png
[12]: ./media/active-directory-saas-amazon-web-service/ic795032.png
[13]: ./media/active-directory-saas-amazon-web-service/ic795033.png
[14]: ./media/active-directory-saas-amazon-web-service/ic795034.png
[15]: ./media/active-directory-saas-amazon-web-service/ic795035.png
[16]: ./media/active-directory-saas-amazon-web-service/ic795022.png
[17]: ./media/active-directory-saas-amazon-web-service/ic795023.png
[18]: ./media/active-directory-saas-amazon-web-service/ic795024.png
[19]: ./media/active-directory-saas-amazon-web-service/ic795025.png
[20]: ./media/active-directory-saas-amazon-web-service/ic7950351.png
[21]: ./media/active-directory-saas-amazon-web-service/tutorial_general_80.png
[22]: ./media/active-directory-saas-amazon-web-service/ic7950352.png
[23]: ./media/active-directory-saas-amazon-web-service/tutorial_general_81.png
[24]: ./media/active-directory-saas-amazon-web-service/ic7950353.png
[25]: ./media/active-directory-saas-amazon-web-service/tutorial_general_15.png
[26]: ./media/active-directory-saas-amazon-web-service/tutorial_general_18.png
[27]: ./media/active-directory-saas-amazon-web-service/ic7950357.png
[28]: ./media/active-directory-saas-amazon-web-service/ic7950321.png
[29]: ./media/active-directory-saas-amazon-web-service/tutorial_general_16.png
[30]: ./media/active-directory-saas-amazon-web-service/ic795038.png
[31]: ./media/active-directory-saas-amazon-web-service/tutorial_general_17.png

<!---HONumber=August15_HO9-->