<properties
	pageTitle="Esercitazione Integrazione di Azure Active Directory con PerformanceCentre | Microsoft Azure"
	description="Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e PerformanceCentre."
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
	ms.date="01/26/2016"
	ms.author="jeedes"/>


# Esercitazione Integrazione di Azure Active Directory con PerformanceCentre

Questa esercitazione illustra come integrare PerformanceCentre con Azure Active Directory (Azure AD).<br>L'integrazione di PerformanceCentre con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a PerformanceCentre 
- È possibile abilitare gli utenti per l'accesso automatico a PerformanceCentre (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in una posizione centrale, il portale di Azure Active Directory classico.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Prerequisiti 

Per configurare l'integrazione di Azure AD con PerformanceCentre, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di PerformanceCentre abilitata per l'accesso Single Sign-On


> [AZURE.NOTE] Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non è disponibile un ambiente di prova di Azure AD, è possibile ottenere una versione di prova di un mese [qui](https://azure.microsoft.com/pricing/free-trial/). 

 
## Descrizione dello scenario
L'obiettivo di questa esercitazione è testare l'accesso Single Sign-On di Azure AD in un ambiente di test. <br> Lo scenario descritto in questa esercitazione prevede i tre blocchi predefiniti seguenti:

1. Aggiunta di PerformanceCentre dalla raccolta 
2. Configurazione e test dell'accesso Single Sign-On di Azure AD


## Aggiunta di PerformanceCentre dalla raccolta
Per configurare l'integrazione di PerformanceCentre in Azure AD, è necessario aggiungere PerformanceCentre dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere PerformanceCentre dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro. <br><br>![Active Directory][1] <br>

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.<br><br> ![Applicazioni][2]<br>
4. Fare clic su **Aggiungi** nella parte inferiore della pagina.<br><br> ![Applicazioni][3]<br>
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.<br><br> ![Applicazioni][4]<br>
6. Nella casella di ricerca digitare **PerformanceCentre**.<br><br> ![Applicazioni][5]<br>
7. Nel riquadro dei risultati selezionare **PerformanceCentre** e quindi fare clic su **Completa** per aggiungere l'applicazione. <br><br>![Applicazioni][500]<br>


##  Configurazione e test dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come configurare e testare l'accesso Single Sign-On di Azure AD con PerformanceCentre con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di PerformanceCentre che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in PerformanceCentre.<br> La relazione di collegamento viene stabilita assegnando al valore di **nome utente** in Azure AD lo stesso valore di **Username** in PerformanceCentre.
 
Per configurare e testare l'accesso Single Sign-On di Azure AD con PerformanceCentre, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Creazione di un utente test di PerformanceCentre](#creating-a-halogen-software-test-user)**: per avere una controparte di Britta Simon in PerformanceCentre collegata alla relativa rappresentazione in Azure AD.
5. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### Configurazione dell'accesso Single Sign-On di Azure AD

Questa sezione descrive come abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure AD classico e configurare l'accesso Single Sign-On nell'applicazione PerformanceCentre.<br>

**Per configurare l'accesso Single Sign-On di Azure AD con PerformanceCentre, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **PerformanceCentre** del portale di Azure AD classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**. <br><br> ![Configura accesso Single Sign-On][6] <br>

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a PerformanceCentre** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**. <br><br> ![Accesso Single Sign-On di Azure AD][7] <br>

3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura: <br><br>![Accesso Single Sign-On di Azure AD][8] <br>
 
     a. Nella casella di testo **URL di accesso** digitare l'URL usato dagli utenti per accedere al sito PerformanceCentre, ad esempio **http://companyname.performancecentre.com/saml/SSO*).
 
     b. Fare clic su **Avanti**.
 
4. Nella pagina **Configura accesso Single Sign-On in PerformanceCentre** seguire questa procedura: <br><br>![Accesso Single Sign-On di Azure AD][9] <br>

    a. Fare clic su **Scarica metadati** e quindi salvare il file nel computer.



1. Accedere al sito aziendale di **PerformanceCentre** come amministratore.

2. Nella barra spostamento sul lato sinistro fare clic su **Configure**. <br><br>![Accesso Single Sign-On di Azure AD][10]

2. Nella scheda sul lato sinistro fare clic su **Miscellaneous** e quindi su **Single Sign On**. <br><br>![Accesso Single Sign-On di Azure AD][11]

2. Per **Protocol** selezionare **SAML**. <br><br>![Accesso Single Sign-On di Azure AD][12]

2. Aprire il file di metadati nel Blocco note, copiarne il contenuto negli Appunti, incollarlo nella casella di testo **Identity Provider Metadata** e quindi fare clic su **Save**. <br><br>![Accesso Single Sign-On di Azure AD][13]

2. Verificare che i valori di **Entity Base URL** e **Entity ID URL** siano corretti. <br><br>![Accesso Single Sign-On di Azure AD][14]


6. Nel portale di Azure AD classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**. <br><br>![Accesso Single Sign-On di Azure AD][15]<br>

7. Nella pagina **Conferma Single Sign-On** fare clic su **Completa**. <br><br>![Accesso Single Sign-On di Azure AD][16]




### Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di Azure classico.<br> Nell'elenco di utenti selezionare **Britta Simon**.<br><br>![Creare un utente di Azure AD][20]<br>

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro. <br><br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_09.png) <br> 

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per visualizzare l'elenco di utenti, fare clic su **Utenti** nel menu in alto. <br><br> ![Creazione di un utente test di Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_03.png) <br>
 
4. Per aprire la finestra di dialogo **Aggiungi utente**, nella barra degli strumenti in basso fare clic su **Aggiungi utente**. <br><br> ![Creazione di un utente test di Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_04.png) <br>

5. Nella pagina della finestra di dialogo **Informazioni sull'utente** seguire questa procedura: <br><br> ![Creazione di un utente test di Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_05.png) <br>

    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.

    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina della finestra di dialogo **Profilo utente** seguire questa procedura: <br><br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_06.png) <br>
 
    a. Nella casella di testo **Nome** digitare **Britta**.

    b. Nella casella di testo **Cognome** digitare **Simon**.

    c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.

    d. Nell'elenco **Ruolo** selezionare **Utente**. e. Fare clic su **Avanti**.

7. Nella pagina della finestra di dialogo **Ottieni password temporanea** fare clic su **crea**. <br><br> ![Creazione di un utente test di Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_07.png) <br>
 
8. Nella pagina **Ottieni password temporanea** seguire questa procedura: <br><br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_08.png) <br>
  
    a. Prendere nota del valore visualizzato in **Nuova password**.

    b. Fare clic su **Complete**.

  
 
### Creazione di un utente test per PerformanceCentre

Questa sezione descrive come creare un utente chiamato Britta Simon in PerformanceCentre.

**Per creare un utente denominato Britta Simon in PerformanceCentre, seguire questa procedura:**

1. Accedere al sito aziendale di PerformanceCentre come amministratore.

2. Nel menu a sinistra fare clic su **Interrelate** e quindi su **Create Participant**. <br><br>![Create User][400]<br>

4. Nella finestra di dialogo **Interrelate - Create Participant** seguire questa procedura. <br><br>![Crea utente][401]<br>

    a. Immettere gli attributi richiesti per Britta Simon nelle caselle di testo corrispondenti.
    
    > [AZURE.IMPORTANT] L'attributo User Name di Britta in PerformanceCentre deve corrispondere al nome utente in Azure AD.


    b. Selezionare **Client Administrator** in **Choose Role**.

    c. Fare clic su **Save**.


### Assegnazione dell'utente test di Azure AD

Questa sezione descrive come abilitare Britta Simon per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a PerformanceCentre. <br><br>![Assegna utente][200] <br>

**Per assegnare Britta Simon a PerformanceCentre, seguire questa procedura:**

1. Per aprire la visualizzazione applicazioni nel portale di Azure classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto. <br><br>![Assegna utente][201] <br>
2. Nell'elenco di applicazioni selezionare **PerformanceCentre**. <br><br>![Assegna utente][202] <br>
1. Nel menu in alto fare clic su **Utenti**. <br><br>![Assegna utente][203] <br>
1. Nell'elenco di utenti selezionare **Britta Simon**.

2. Fare clic su **Assegna** sulla barra degli strumenti in basso. <br><br>![Assegna utente][205]



### Test dell'accesso Single Sign-On

Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.<br> Quando si fa clic sul riquadro PerformanceCentre nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione PerformanceCentre.


## Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_01.png
[500]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_02.png

[6]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_03.png
[8]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_04.png
[9]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_05.png
[10]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_06.png
[11]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_07.png
[12]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_08.png
[13]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_09.png
[14]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_10.png
[15]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_06.png
[16]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_07.png


[20]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_50.png
[203]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_11.png
[401]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_12.png
[402]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_402.png

<!---HONumber=AcomDC_0128_2016-->