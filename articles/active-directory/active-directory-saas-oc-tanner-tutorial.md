<properties
	pageTitle="Esercitazione: Integrazione di Azure Active Directory con O.C. Tanner - AppreciateHub | Microsoft Azure"
	description="Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e O.C. Tanner - AppreciateHub."
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
	ms.date="10/09/2015"
	ms.author="markusvi"/>


# Esercitazione: Integrazione di Azure Active Directory con O.C. Tanner - AppreciateHub

Questa esercitazione descrive l'integrazione di O.C. Tanner - AppreciateHub con Azure Active Directory (Azure AD).<br>L'integrazione di O.C. Tanner - AppreciateHub con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a O.C. Tanner - AppreciateHub 
- È possibile abilitare gli utenti ad accedere automaticamente a O.C. Tanner - AppreciateHub (Single Sign-On) con i relativi account Azure AD
- È possibile gestire gli account in una posizione centrale, il portale di Azure Active Directory.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Prerequisiti 

Per configurare l'integrazione di Azure Active Directory con O.C. Tanner - AppreciateHub, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di O.C. Tanner - AppreciateHub abilitata per l'accesso Single Sign-On


> [AZURE.NOTE]Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/). 

 
## Descrizione dello scenario
L'obiettivo di questa esercitazione è testare l'accesso Single Sign-On di Azure AD in un ambiente di test. <br> Lo scenario descritto in questa esercitazione prevede i tre blocchi predefiniti seguenti:

1. Aggiunta di O.C. Tanner - AppreciateHub dalla raccolta 
2. Configurazione e test dell'accesso Single Sign-On di Azure AD


## Aggiunta di O.C. Tanner - AppreciateHub dalla raccolta
Per configurare l'integrazione di O.C. Tanner - AppreciateHub in Azure AD, è necessario aggiungere O.C. Tanner - AppreciateHub dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere O.C. Tanner - AppreciateHub dalla raccolta, seguire questa procedura:**

1. Nel **portale di gestione di Azure** fare clic su **Active Directory** nel pannello di navigazione sinistro. <br><br> ![Active Directory][1] <br>

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto. <br><br> ![Applicazioni][2] <br>

4. Fare clic su **Aggiungi** nella parte inferiore della pagina. <br><br> ![Applicazioni][3] <br>

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**. <br><br> ![Applicazioni][4] <br>

6. Nella casella di ricerca digitare **O.C. Tanner - AppreciateHub**. <br><br> ![Applicazioni][5] <br>

7. Nel riquadro dei risultati selezionare **O.C. Tanner - AppreciateHub** e quindi fare clic su **Completa** per aggiungere l'applicazione. <br><br> ![Applicazioni][25] <br>




##  Configurazione e test dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come configurare e testare l'accesso Single Sign-On di Azure AD con O.C. Tanner - AppreciateHub in base a un utente test denominato "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di O.C. Tanner - AppreciateHub che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in O.C. Tanner - AppreciateHub.<br> La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** in O.C. Tanner - AppreciateHub.
 
Per configurare e testare l'accesso Single Sign-On di Azure AD con O.C. Tanner - AppreciateHub, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Creazione di un utente test di O.C. Tanner - AppreciateHub](#creating-a-halogen-software-test-user)** - per avere una controparte di Britta Simon in O.C. Tanner - AppreciateHub collegata alla relativa rappresentazione in Azure AD.
5. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### Configurazione dell'accesso Single Sign-On di Azure AD

L'obiettivo di questa sezione è abilitare Single Sign-On di Azure AD nel portale di Azure AD e configurare l'accesso Single Sign-On nell'applicazione O.C. Tanner - AppreciateHub.<br>

**Per configurare l'accesso Single Sign-On di Azure AD con O.C. Tanner - AppreciateHub, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **O.C. Tanner - AppreciateHub** fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.<br><br> ![Configura accesso Single Sign-On][6]

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a O.C. Tanner - AppreciateHub** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.<br><br> ![Accesso Single Sign-On di Azure AD][7]

3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura: <br><br>![Configurare le impostazioni dell'app][8]
 
     a. Aprire il file dei metadati usando il collegamento seguente: [https://fed.appreciatehub.com/fed/sp/metadata](https://fed.appreciatehub.com/fed/sp/metadata).

     b. Individuare il nodo **md:AssertionConsumerService**.

     c. Copiare il valore dell'attributo **Location**. <br><br>![Configurare le impostazioni dell'app][12]
     
     d. Nella casella di testo **Sign On URL** incollare il valore ottenuto nel passaggio precedente.

     >[AZURE.NOTE]Nel caso di problemi per ottenere l'URL di risposta dal file dei metadati, contattare il team di supporto di O.C. Tanner - AppreciateHub tramite [sso@octanner.com](mailto:sso@octanner.com).

     e. Fare clic su **Next**.
 
4. Nella pagina **Configura accesso Single Sign-On in O.C. Tanner - AppreciateHub** fare clic su **Scarica metadati** e quindi salvare il file di metadati in locale nel computer.<br><br>![Cos'è Azure AD Connect][9]

5. Contattare il team di supporto di O.C. Tanner - AppreciateHub tramite xyz, fornire il file di metadati e chiedere di abilitare Single Sign-On.


6. Nel portale di Azure AD selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**. <br><br>![Cos'è Azure AD Connect][10]

7. Nella pagina **Conferma Single Sign-On** fare clic su **Completa**. <br><br>![Cos'è Azure AD Connect][11]




### Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test nel portale di Azure denominato Britta Simon.<br> Nell'elenco di utenti selezionare **Britta Simon**.<br><br>![Creare un utente di Azure AD][20]<br>

**Per creare un utente test in Azure AD, seguire questa procedura:**

1. Nel **portale di gestione di Azure** fare clic su **Active Directory** nel pannello di navigazione sinistro.<br> ![Creazione di un utente test di Azure AD](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_02.png) 

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per visualizzare l'elenco di utenti, nel menu in alto fare clic su **Utenti**.<br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_03.png)
 
4. Per aprire la finestra di dialogo **Aggiungi utente**, nella barra degli strumenti in basso fare clic su **Aggiungi utente**. <br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_04.png)

5. Nella pagina **Informazioni sull'utente** seguire questa procedura: <br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_05.png)

    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.

    b. Nella **casella di testo** Nome utente digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina **Profilo utente** seguire questa procedura: <br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_06.png)
 
    a. Nella casella di testo **Nome** digitare **Britta**.

    b. Nella casella di testo **Cognome** digitare **Simon**.

    c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.

    d. Nell'elenco **Ruolo** selezionare **Utente**. e. Fare clic su **Avanti**.

7. Nella pagina **Ottieni password temporanea** fare clic su **Crea**.<br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_07.png)
 
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:<br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_08.png)
  
    a. Prendere nota del valore in **Nuova password**.

    b. Fare clic su **Completa**.

  
 
### Creazione di un utente test di O.C. Tanner - AppreciateHub

L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon in O.C. Tanner - AppreciateHub.

**Per creare un utente denominato Britta Simon in O.C. Tanner - AppreciateHub, seguire questa procedura:**

1. Chiedere al team di supporto di O.C. Tanner di creare un utente con un valore per l'attributo nameID equivalente al nome utente di Britta Simon in Azure AD.


### Assegnazione dell'utente test di Azure AD

L'obiettivo di questa sezione consiste nell'abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a O.C. Tanner - AppreciateHub. <br><br>![Assegna utente][200]

**Per assegnare Britta Simon a O.C. Tanner - AppreciateHub, seguire questa procedura:**

1. Per aprire la visualizzazione applicazioni nel portale di Azure, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.<br> <br><br>![Assegna utente][201]
2. Nell'elenco delle applicazioni selezionare **O.C. Tanner - AppreciateHub**. <br><br>![Assegna utente][202]
1. Nel menu in alto fare clic su **Utenti**. <br> <br><br>![Assegna utente][203]
1. Nell'elenco di utenti selezionare **Britta Simon**.

2. Sulla barra degli strumenti in basso fare clic su **Assegna**. <br><br>![Assegna utente][205]



### Test dell'accesso Single Sign-On

L'obiettivo di questa sezione consiste nel testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.<br> Quando si fa clic sul riquadro O.C. Tanner - AppreciateHub nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione O.C. Tanner - AppreciateHub.


## Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)


<!--Image references-->
[1]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_01.png
[25]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_25.png


[6]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_02.png
[8]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_03.png
[9]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_04.png
[10]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_05.png
[11]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_06.png
[12]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_08.png
[20]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_07.png
[203]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_205.png

<!---HONumber=Oct15_HO3-->