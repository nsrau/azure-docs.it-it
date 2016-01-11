<properties
	pageTitle="Esercitazione: Integrazione di Azure Active Directory con Halogen Software"
	description="Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Halogen Software."
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


# Esercitazione: Integrazione di Azure Active Directory con Halogen Software

L'obiettivo di questa esercitazione è illustrare come integrare Halogen Software con Azure Active Directory (Azure AD).<br>L'integrazione di Halogen Software con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Halogen Software. 
- È possibile abilitare gli utenti per l'accesso automatico a Halogen Software (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in una posizione centrale, il portale di Azure Active Directory.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Prerequisiti 

Per configurare l'integrazione di Azure AD con Halogen Software, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Halogen Software abilitata per l'accesso Single Sign-On.


> [AZURE.NOTE]Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/). 

 
## Descrizione dello scenario
L'obiettivo di questa esercitazione è testare l'accesso Single Sign-On di Azure AD in un ambiente di test. <br> Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Halogen Software dalla raccolta 
2. Configurazione e test dell'accesso Single Sign-On di Azure AD


## Aggiunta di Halogen Software dalla raccolta
Per configurare l'integrazione di Halogen Software in Azure AD, è necessario aggiungere Halogen Software dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Halogen Software dalla raccolta, eseguire la procedura seguente:**

1. Nel pannello di navigazione sinistro del **portale di gestione di Azure** fare clic su **Active Directory**. <br><br> ![Active Directory][1]

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.<br><br> ![Applicazioni][2]
4. Fare clic su **Aggiungi** nella parte inferiore della pagina.<br><br> ![Applicazioni][3]
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.<br><br> ![Applicazioni][4]
6. Nella casella di ricerca digitare **halogen software**.<br> ![Applicazioni][5]
7. Nel riquadro dei risultati selezionare **Halogen Software** e quindi fare clic su **Completa** per aggiungere l'applicazione.<br>



##  Configurazione e test dell'accesso Single Sign-On di Azure AD
L'obiettivo di questa sezione è illustrare come configurare e testare l'accesso Single Sign-On di Azure AD con Halogen Software in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Halogen Software che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Halogen Software.<br> La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **nome utente** in Halogen Software.
 
Per configurare e testare l'accesso Single Sign-On di Azure AD con Halogen Software, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Creazione di un utente test di Halogen Software ](#creating-a-halogen-software-test-user)**: per avere una controparte di Britta Simon in Halogen Software collegata alla relativa rappresentazione in Azure AD.
5. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### Configurazione dell'accesso Single Sign-On di Azure AD

L'obiettivo di questa sezione è abilitare Single Sign-On di Azure AD nel portale di Azure AD e configurare l'accesso Single Sign-On nell'applicazione Halogen Software.<br>

**Per configurare Single Sign-On di Azure AD con Halogen Software, eseguire la procedura seguente:**

1. Nella pagina di integrazione dell'applicazione **Halogen Software** nel portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.<br><br>![Configura accesso Single Sign-On][8]

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Halogen Software** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.<br><br> ![Accesso Single Sign-On di Azure AD][9]

3. Nella pagina **Configurare le impostazioni dell'app** eseguire la procedura seguente: <br><br>![Configurare le impostazioni dell'app][10]
 
     3\.1 Nella casella di testo **URL di accesso** digitare l'URL usato dagli utenti per accedere all'applicazione Halogen Software usando il modello seguente: **https://global.hgncloud.com/fabrikam/welcome.jsp*

     3\.2. Fare clic su **Avanti**.
 
4. Nella pagina **Configura accesso Single Sign-On in Halogen Software** fare clic su **Scarica metadati** e quindi salvare il file di metadati localmente nel computer.<br><br>![Cos'è Azure AD Connect][11]

5. In una finestra del browser diversa accedere all'applicazione **Software alogeno** come amministratore.
6. Fare clic sulla scheda **Options**. <br><br>![Cos'è Azure AD Connect][12]
7. Nel pannello di navigazione sinistro fare clic su **SAML Configuration**. <br><br>![Cos'è Azure AD Connect][13]
8. Nella pagina **SAML Configuration** eseguire i passaggi seguenti:<br><br>![Cos'è Azure AD Connect][14]

     8\.1 Come **Unique Identifier** selezionare **NameID**.

     8\.2. Come **Unique Identifier Maps To** selezionare **Username**.

     8\.3. Per caricare il file di metadati fare clic su **Browse** per selezionare il file e quindi fare clic su **Upload File**.

     8\.4. Per testare la configurazione, fare clic su **Run Test**. >[AZURE.NOTE]È necessario attendere la visualizzazione del messaggio"*The SAML test is complete. Please close this window*". chiudere quindi la ginestra del browser aperta. <br> La casella di controllo **Enable SAML** è selezionata solo se il test è stato completato.

     8\.5. Selezionare **Enable SAML**.
    
     8\.6. Fare clic su **Save** per salvare le modifiche.


9. Nel portale di Azure AD selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura Single Sign-On**.<br><br>![Cos'è Azure AD Connect][15]
10. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**. <br><br>![Cos'è Azure AD Connect][16]




### Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test nel portale di Azure chiamato Britta Simon.

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel pannello di navigazione sinistro del **portale di gestione di Azure** fare clic su **Active Directory**. <br><br>![Cos'è Azure AD Connect][100] 
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per visualizzare l'elenco di utenti, nel menu in alto fare clic su **Utenti**. <br><br>![Cos'è Azure AD Connect][101] 
4. Per aprire la finestra di dialogo **Aggiungi utente**, nella barra degli strumenti in basso fare clic su **Aggiungi utente**. <br><br>![Cos'è Azure AD Connect][102] 
5. Nella pagina **Informazioni sull'utente** eseguire la procedura seguente: <br><br>![Cos'è Azure AD Connect][103] 
  1. Come **Tipo di utente** selezionare **Nuovo utente nell'organizzazione**.
  2. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
  3. Fare clic su Avanti.
6.  Nella pagina **Profilo utente** eseguire la procedura seguente: <br><br>![Cos'è Azure AD Connect][104] 
  1. Nella casella di testo **Nome** digitare **Britta**.  
  2. Nella casella di testo **Cognome** digitare **Simon**.
  3. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
  4. Nell'elenco **Ruolo** selezionare **Utente**.
  5. Fare clic su **Avanti**.
7. Nella pagina **Ottieni password temporanea** fare clic su **Crea**. <br><br>![Cos'è Azure AD Connect][105]  
8. Nella pagina **Ottieni password temporanea** eseguire la procedura seguente: <br><br>![Cos'è Azure AD Connect][106]   
  1. Prendere nota del valore in **Nuova password**.
  2. Fare clic su **Complete**.   
  
 
### Creazione di un utente test di Halogen Software

L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon in Halogen Software.

**Per creare un utente test denominato Britta Simon in Halogen Software, eseguire la procedura seguente:**

1. Accedere all'applicazione **Halogen Software** come amministratore.
2. Fare clic sulla scheda **User Center** e quindi su **Create User**. <br><br>![Cos'è Azure AD Connect][300]  
3. Nella pagina **New User** eseguire la procedura seguente: <br><br>![Cos'è Azure AD Connect][301]
  1. Nella casella di testo **Nome** digitare **Britta**. 
  2. Nella casella di testo **Cognome** digitare **Simon**.
  3. Nella casella di testo **Nome utente** digitare **Nome utente di Britta Simon nel portale di Azure AD**.
  4. Nella casella di testo **Password** digitare una password per Britta.
  5. Fare clic su **Salva**.


### Assegnazione dell'utente test di Azure AD

L'obiettivo di questa sezione consiste nell'abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Halogen Software. <br><br>![Cos'è Azure AD Connect][200]

**Per assegnare Britta Simon a Halogen Software, eseguire la procedura seguente:**

1. Per aprire la visualizzazione applicazioni nel portale di Azure, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.<br> <br><br>![Cos'è Azure AD Connect][201]
2. Nell'elenco delle applicazioni selezionare **Halogen Software**. <br><br>![Cos'è Azure AD Connect][202]
1. Nel menu in alto fare clic su **Utenti**.<br> <br><br>![Cos'è Azure AD Connect][203]
1. Nell'elenco di utenti selezionare **Britta Simon**. <br><br>![Cos'è Azure AD Connect][204]
2. Sulla barra degli strumenti in basso fare clic su **Assegna**. <br><br>![Cos'è Azure AD Connect][205]



### Test dell'accesso Single Sign-On

L'obiettivo di questa sezione consiste nel testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.<br> Quando si fa clic sul riquadro Halogen Software nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Halogen Software.


## Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_01.png
[2]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_02.png
[3]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_03.png
[4]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_04.png
[5]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_05.png
[6]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_06.png
[7]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_07.png
[8]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_08.png
[9]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_09.png
[10]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_10.png
[11]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_11.png
[12]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_12.png
[13]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_13.png
[14]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_14.png
[15]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_15.png
[16]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_16.png
[100]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_100.png
[101]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_101.png
[102]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_102.png
[103]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_103.png
[104]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_104.png
[105]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_105.png
[106]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_106.png
[200]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_200.png
[201]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_201.png
[202]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_202.png
[203]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_203.png
[204]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_204.png
[205]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_205.png
[300]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_300.png
[301]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_301.png

<!---HONumber=AcomDC_1223_2015-->