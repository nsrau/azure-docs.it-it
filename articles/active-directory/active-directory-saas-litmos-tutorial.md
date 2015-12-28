<properties
	pageTitle="Esercitazione Integrazione di Azure Active Directory con Litmos | Microsoft Azure"
	description="Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Litmos."
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
	ms.date="10/30/2015"
	ms.author="markusvi"/>


# Esercitazione Integrazione di Azure Active Directory con Litmos

Questa esercitazione illustra come integrare Litmos con Azure Active Directory (Azure AD).<br>L'integrazione di Litmos con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Litmos 
- È possibile abilitare gli utenti per l'accesso automatico a Litmos (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in una posizione centrale, il portale di Azure Active Directory.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Prerequisiti 

Per configurare l'integrazione di Azure AD con Litmos, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Litmos abilitata per l'accesso Single Sign-On


> [AZURE.NOTE]Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/). 

 
## Descrizione dello scenario
L'obiettivo di questa esercitazione è testare l'accesso Single Sign-On di Azure AD in un ambiente di test. <br> Lo scenario descritto in questa esercitazione prevede i tre blocchi predefiniti seguenti:

1. Aggiunta di Litmos dalla raccolta 
2. Configurazione e test dell'accesso Single Sign-On di Azure AD


## Aggiunta di Litmos dalla raccolta
Per configurare l'integrazione di Litmos in Azure AD, è necessario aggiungere Litmos dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Litmos dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure** fare clic su **Active Directory** nel riquadro di spostamento sinistro. <br><br> ![Active Directory][1]<br>

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.<br><br> ![Applicazioni][2]<br>
4. Fare clic su **Aggiungi** nella parte inferiore della pagina.<br><br> ![Applicazioni][3]<br>
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.<br><br> ![Applicazioni][4]<br>
6. Nella casella di ricerca digitare **Litmos**.<br><br> ![Applicazioni][5]<br>
7. Nel riquadro dei risultati selezionare **Litmos** e quindi fare clic su **Completa** per aggiungere l'applicazione. <br><br>![Applicazioni][500]<br>


##  Configurazione e test dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come configurare e testare l'accesso Single Sign-On di Azure AD con Litmos in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Litmos che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Litmos.<br> La relazione di collegamento viene stabilita assegnando al valore di **nome utente** in Azure AD lo stesso valore di **Username** in Litmos.
 
Per configurare e testare l'accesso Single Sign-On di Azure AD con Litmos, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Creazione di un utente test di Litmos](#creating-a-halogen-software-test-user)**: per avere una controparte di Britta Simon in Litmos collegata alla relativa rappresentazione in Azure AD.
5. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### Configurazione dell'accesso Single Sign-On di Azure AD

Questa sezione descrive come abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure AD e configurare l'accesso Single Sign-On nell'applicazione Litmos.<br> Per eseguire questa procedura, è necessario creare un certificato con codifica Base 64. Se non si ha familiarità con questa procedura, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

La procedura di configurazione prevede la personalizzazione degli **Attributi token SAML** per l'applicazione Litmos. <br><br> ![Accesso Single Sign-On di Azure AD][17] <br>

**Per configurare l'accesso Single Sign-On di Azure AD con Litmos, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Litmos** del portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**. <br><br> ![Configura accesso Single Sign-On][6] <br>

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Litmos** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**. <br><br> ![Accesso Single Sign-On di Azure AD][7] <br>


1. Accedere al sito aziendale di Litmos, ad esempio **https://azureapptest.litmos.com/account/Login*, come amministratore. <br><br> ![Accesso Single Sign-On di Azure AD][21] <br>


1. Nella barra spostamento sul lato sinistro fare clic su **Accounts**. <br><br> ![Accesso Single Sign-On di Azure AD][22] <br>


1. Fare clic sulla scheda **Integrations**. <br><br> ![Accesso Single Sign-On di Azure AD][23] <br>


1. Nella scheda **Integrations** scorrere verso il basso fino a **3rd Party Integrations** e quindi fare clic sulla scheda **SAML 2.0**. <br><br> ![Accesso Single Sign-On di Azure AD][24] <br>

1. Copiare il valore in **The SAML endpoint for litmos is:**. <br><br> ![Accesso Single Sign-On di Azure AD][26] <br>


3. Nella pagina **Configurare le impostazioni dell'app** del portale di Azure, seguire questa procedura: <br><br>![Accesso Single Sign-On di Azure AD][8] <br>
 
    a. Nella casella di testo **Identificatore** digitare l'URL usato dagli utenti per accedere all'applicazione Litmos, ad esempio **https://azureapptest.litmos.com/account/Login*).
     
    b. Nella casella di testo **URL di risposta** incollare il valore copiato dall'applicazione Litmos nel passaggio precedente.

    c. Fare clic su **Avanti**.
 
4. Nella pagina **Configura accesso Single Sign-On in Litmos** seguire questa procedura: <br><br>![Accesso Single Sign-On di Azure AD][2] <br>

    a. Fare clic su Scarica certificato e quindi salvare il file nel computer.


1. Nell'applicazione **Litmos** seguire questa procedura: <br><br>![Accesso Single Sign-On di Azure AD][25] <br>

    a. Fare clic su **Enable SAML**.

    b. Creare un file con **codifica Base 64** dal certificato scaricato.

    >[AZURE.TIP]Per altre informazioni, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

    c. Aprire il certificato con codifica Base 64 nel Blocco note, copiarne il contenuto negli Appunti e quindi incollarlo nella casella di testo **SAML X.509 Certificate**.

    d. Fare clic su **Save Changes**.


6. Nel portale di Azure AD selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**. <br><br>![Accesso Single Sign-On di Azure AD][10]<br>

7. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**. <br><br>![Accesso Single Sign-On di Azure AD][11]


20. Nel menu in alto fare clic su **Attributi** per aprire la finestra di dialogo **Attributi token SAML**. <br><br>![Configura accesso Single Sign-On][12]<br>


24. Nella finestra di dialogo **Aggiungi attributo utente** seguire questa procedura: <br><br>![Configura accesso Single Sign-On][14]<br>

    | Nome attributo | Valore attributo |
    | ---            | ---             |
    | Email | user.mail |
    | FirstName | user.givenname |
    | Lastname | user.surname |

    Per ogni riga di dati nella tabella precedente, seguire questa procedura:
   
    a. Fare clic su **Aggiungi attributo utente**. <br><br>![Configura accesso Single Sign-On][15]<br>


    a. Nella casella di testo **Nome attributo** digitare il **Nome attributo** indicato per la riga.

    b. Selezionare il **Valore attributo** indicato per la riga.

    c. Fare clic su **Completa** per chiudere la finestra di dialogo **Aggiungi attributo utente**.


25. Fare clic su **Applica modifiche**. <br><br>![Configura accesso Single Sign-On][16]<br>




### Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di Azure.<br> Nell'elenco di utenti selezionare **Britta Simon**.<br><br>![Creare un utente di Azure AD][20]<br>

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic su **Active Directory** nel riquadro di spostamento sinistro. <br><br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_09.png) <br> 

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per visualizzare l'elenco di utenti, nel menu in alto fare clic su **Utenti**. <br><br> ![Creazione di un utente test di Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_03.png) <br>
 
4. Per aprire la finestra di dialogo **Aggiungi utente**, nella barra degli strumenti in basso fare clic su **Aggiungi utente**. <br><br> ![Creazione di un utente test di Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_04.png) <br>

5. Nella pagina **Informazioni sull'utente** seguire questa procedura: <br><br> ![Creazione di un utente test di Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_05.png) <br>

    a. In **Tipo di utente** selezionare **Nuovo utente nell'organizzazione**.

    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina **Profilo utente** seguire questa procedura: <br><br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_06.png) <br>
 
    a. Nella casella di testo **Nome** digitare **Britta**.

    b. Nella casella di testo **Cognome** digitare **Simon**.

    c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.

    d. Nell'elenco **Ruolo** selezionare **Utente**. e. Fare clic su **Avanti**.

7. Nella pagina **Ottieni password temporanea** fare clic su **crea**. <br><br> ![Creazione di un utente test di Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_07.png) <br>
 
8. Nella pagina **Ottieni password temporanea** seguire questa procedura: <br><br>![Creazione di un utente test di Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_08.png) <br>
  
    a. Prendere nota del valore in **Nuova password**.

    b. Fare clic su **Complete**.

  
 
### Creazione di un utente test per Litmos

Questa sezione descrive come creare un utente chiamato Britta Simon in Litmos.<br> L'applicazione Litmos supporta il provisioning JIT (just-in-time). Questo significa che, se necessario, un account utente viene creato automaticamente durante il tentativo di accedere all'applicazione tramite il pannello di accesso.

**Per creare un utente denominato Britta Simon in Litmos, seguire questa procedura:**


1. Accedere al sito aziendale di Litmos, ad esempio **https://azureapptest.litmos.com/account/Login*, come amministratore. <br><br> ![Accesso Single Sign-On di Azure AD][21] <br>


1. Nella barra spostamento sul lato sinistro fare clic su **Accounts**. <br><br> ![Accesso Single Sign-On di Azure AD][22] <br>


1. Fare clic sulla scheda **Integrations**. <br><br> ![Accesso Single Sign-On di Azure AD][23] <br>


1. Nella scheda **Integrations** scorrere verso il basso fino a **3rd Party Integrations** e quindi fare clic sulla scheda **SAML 2.0**. <br><br> ![Accesso Single Sign-On di Azure AD][24] <br>

1. Selezionare **Autogenerate Users:**. <br><br> ![Accesso Single Sign-On di Azure AD][27] <br>


### Assegnazione dell'utente test di Azure AD

Questa sezione descrive come abilitare Britta Simon per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Litmos. <br><br>![Assegna utente][200] <br>

**Per assegnare Britta Simon a Litmos, seguire questa procedura:**

1. Per aprire la visualizzazione applicazioni nel portale di Azure, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto. <br><br>![Assegna utente][201] <br>
2. Nell'elenco di applicazioni selezionare **Litmos**. <br><br>![Assegna utente][202] <br>
1. Nel menu in alto fare clic su **Utenti**. <br><br>![Assegna utente][203] <br>
1. Nell'elenco di utenti selezionare **Britta Simon**.

2. Fare clic su **Assegna** sulla barra degli strumenti in basso. <br><br>![Assegna utente][205]



### Test dell'accesso Single Sign-On

Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.<br> Quando si fa clic sul riquadro Litmos nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Litmos.


## Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_01.png
[500]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_02.png

[6]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_03.png
[8]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_04.png
[9]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_05.png
[10]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_07.png
[12]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_80.png
[13]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_81.png
[14]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_82.png
[15]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_81.png
[16]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_19.png
[17]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_67.png


[20]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_60.png
[22]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_61.png
[23]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_62.png
[24]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_63.png
[25]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_64.png
[26]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_65.png
[27]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_66.png

[200]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_68.png
[203]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_400.png
[401]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_401.png
[402]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_402.png

<!---HONumber=AcomDC_1217_2015-->