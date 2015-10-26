<properties
	pageTitle="Esercitazione: Integrazione di Azure Active Directory con Questetra BPM Suite | Microsoft Azure"
	description="Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Questetra BPM Suite."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="msStevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/02/2015"
	ms.author="markusvi"/>


# Esercitazione: Integrazione di Azure Active Directory con Questetra BPM Suite

L'obiettivo di questa esercitazione è illustrare come integrare Questetra BPM Suite con Azure Active Directory (Azure AD).<br>L'integrazione di Questetra BPM Suite con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Questetra BPM Suite. 
- È possibile abilitare gli utenti per l'accesso automatico a Questetra BPM Suite (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in una posizione centrale, il portale di Azure Active Directory.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Prerequisiti 

Per configurare l'integrazione di Azure AD con Questetra BPM Suite, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di [Questetra BPM Suite](https://senbon-imadegawa-988.questetra.net/) abilitata per l'accesso Single Sign-On


> [AZURE.NOTE]Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese da [questa pagina](https://azure.microsoft.com/pricing/free-trial/). 

 
## Descrizione dello scenario
L'obiettivo di questa esercitazione è testare l'accesso Single Sign-On di Azure AD in un ambiente di test. <br> Lo scenario descritto in questa esercitazione prevede i tre blocchi predefiniti seguenti:

1. Aggiunta di Questetra BPM Suite dalla raccolta 
2. Configurazione e test dell'accesso Single Sign-On di Azure AD


## Aggiunta di Questetra BPM Suite dalla raccolta
Per configurare l'integrazione di Questetra BPM Suite in Azure AD, è necessario aggiungere Questetra BPM Suite dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Questetra BPM Suite dalla raccolta, seguire questa procedura:**

1. Nel **portale di gestione di Azure** fare clic su **Active Directory** nel pannello di navigazione sinistro. <br><br> ![Active Directory][1]

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.<br><br> ![Applicazioni][2]
4. Fare clic su **Aggiungi** nella parte inferiore della pagina.<br><br> ![Applicazioni][3]
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungi un'applicazione dalla raccolta**.<br><br> ![Applicazioni][4]
6. Nella casella di ricerca digitare **Questetra BPM Suite**.<br> ![Applicazioni][5]
7. Nel riquadro dei risultati selezionare **Questetra BPM Suite** e quindi fare clic su **Completa** per aggiungere l'applicazione.<br>



##  Configurazione e test dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come configurare e testare l'accesso Single Sign-On di Azure AD con Questetra BPM Suite in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Questetra BPM Suite che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Questetra BPM Suite.<br> La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **nome utente** in Questetra BPM Suite.
 
Per configurare e testare l'accesso Single Sign-On di Azure AD con Questetra BPM Suite, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Creazione di un utente test di Questetra BPM Suite ](#creating-a-questetra-bpm-suite-test-user)**: per avere una controparte di Britta Simon in Questetra BPM Suite collegata alla relativa rappresentazione in Azure AD.
5. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### Configurazione dell'accesso Single Sign-On di Azure AD

Questa sezione descrive come abilitare Single Sign-On di Azure AD nel portale di Azure AD e configurare l'accesso Single Sign-On nell'applicazione Questetra BPM Suite.<br>

**Per configurare Single Sign-On di Azure AD con Questetra BPM Suite, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Questetra BPM Suite** del portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.<br><br> ![Configura accesso Single Sign-On][8]

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Questetra BPM Suite** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.<br><br> ![Accesso Single Sign-On di Azure AD][9]


3. In un'altra finestra del Web browser accedere al sito aziendale di **Questetra BPM Suite** come amministratore.

4. Nel menu in alto fare clic su **System Settings**. <br><br> ![Accesso Single Sign-On di Azure AD][10]

5. Per aprire la pagina **SingleSignOnSAML** fare clic su **SSO (SAML)**. <br><br> ![Accesso Single Sign-On di Azure AD][11]


6. Nella pagina **Configurare le impostazioni dell'app** del portale di Azure seguire questa procedura: <br><br>![Configurare le impostazioni dell'app][13]
 
    a. Nella sezione SP Information del sito aziendale di **Questetra BPM Suite** copiare il valore di **ACS URL** e incollarlo nella casella di testo **URL di accesso**.

    b. Nella sezione SP Information del sito aziendale di **Questetra BPM Suite** copiare il valore di **Entity ID** e incollarlo nella casella di testo **URL autorità di certificazione**.

    c. Nella sezione SP Information del sito aziendale di **Questetra BPM Suite** copiare il valore di **ACS URL** e incollarlo nella casella di testo **URL di risposta**.

    d. Fare clic su **Avanti**.

 
7. Nella pagina **Configura accesso Single Sign-On in Questetra BPM Suite** fare clic su **Download certificato** e quindi salvare il file del certificato localmente nel computer.<br><br>![Configura accesso Single Sign-On][14]


8. Nel sito aziendale di **Questetra BPM Suite** seguire questa procedura: <br><br>![Configura accesso Single Sign-On][15]

    a. Selezionare **Enable Single Sign-On**.
     
    b. Nel portale di Azure copiare il valore di **URL autorità di certificazione** e incollarlo nella casella di testo **Entity ID**.

    c. Nel portale di Azure copiare il valore di **URL servizio Single Sign-On** e incollarlo nella casella di testo **Sign-in page URL**.

    d. Nel portale di Azure copiare il valore di **URL servizio Single Sign-Out** e incollarlo nella casella di testo **Sign-out page URL**.

    e. Nella casella di testo **NameID format** digitare **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**.


    f. Creare un file con codifica Base 64 dal certificato scaricato.

    >[AZURE.TIP]Per informazioni dettagliate, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

    g. Aprire il certificato con codifica Base 64 nel Blocco note, copiarne il contenuto negli Appunti e quindi incollarlo nella casella di testo **Validation certificate**.

    h. Fare clic su **Save**.


9. Nel portale di Azure AD selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**. <br><br>![Cos'è Azure AD Connect][17]


10. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**. <br><br>![Cos'è Azure AD Connect][18]




### Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test nel portale di Azure chiamato Britta Simon.

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel pannello di navigazione sinistro del **portale di gestione di Azure** fare clic su **Active Directory**. <br><br>![Creare un utente test di Azure AD][100] 

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per visualizzare l'elenco di utenti, nel menu in alto fare clic su **Utenti**. <br><br>![Creare un utente test di Azure AD][101]

4. Per aprire la finestra di dialogo **Aggiungi utente**, nella barra degli strumenti in basso fare clic su **Aggiungi utente**. <br><br>![Creare un utente test di Azure AD][102]

5. Nella pagina **Informazioni sull'utente** seguire questa procedura: <br><br>![Creare un utente test di Azure AD][103]
 
    a. In **Tipo di utente** selezionare **Nuovo utente nell'organizzazione**.
  
    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.

    c. Fare clic su Avanti.
6.  Nella pagina **Profilo utente** seguire questa procedura: <br><br>![Creare un utente test di Azure AD][104] 
  
    a. Nella casella di testo **Nome** digitare **Britta**.
 
    b. Nella casella di testo **Cognome** digitare **Simon**.

    c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.

    d. Nell'elenco **Ruolo** selezionare **Utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina **Ottieni password temporanea** fare clic su **Crea**. <br><br>![Creare un utente test di Azure AD][105]

8. Nella pagina **Ottieni password temporanea** seguire questa procedura: <br><br>![Creare un utente test di Azure AD][106]
  1. Prendere nota del valore in **Nuova password**.
  2. Fare clic su **Complete**.   
  
 
### Creazione di un utente test di Questetra BPM Suite

Questa sezione descrive come creare un utente chiamato Britta Simon in Questetra BPM Suite.

**Per creare un utente test denominato Britta Simon in Questetra BPM Suite, seguire questa procedura:**

1.	Accedere al sito aziendale di Questetra BPM Suite come amministratore.
2.	Scegliere **System Settings > User List > New User**. 
3.	Nella finestra di dialogo New User seguire questa procedura: <br><br>![Creare un utente test][300] 

    a. Nella casella di testo **Name** digitare il nome utente di Britta in Azure AD.

    b. Nella casella di testo **Email** digitare il nome utente di Britta in Azure AD.

    c. Nella casella di testo **Password** digitare una password.

4.	Fare clic su **Add new user**.



### Assegnazione dell'utente test di Azure AD

Questa sezione descrive come abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Questetra BPM Suite. <br><br>![Cos'è Azure AD Connect][200]

**Per assegnare Britta Simon a Questetra BPM Suite, seguire questa procedura:**

1. Per aprire la visualizzazione applicazioni nel portale di Azure, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.<br><br>![Cos'è Azure AD Connect][201]
2. Nell'elenco delle applicazioni selezionare **Questetra BPM Suite**. <br><br>![Cos'è Azure AD Connect][205]
1. Nel menu in alto fare clic su **Utenti**.<br> <br>![Cos'è Azure AD Connect][202]
1. Nell'elenco di utenti selezionare **Britta Simon**. <br><br>![Cos'è Azure AD Connect][203]
2. Sulla barra degli strumenti in basso fare clic su **Assegna**. <br><br>![Cos'è Azure AD Connect][204]



### Test dell'accesso Single Sign-On

L'obiettivo di questa sezione è testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.<br> Quando si fa clic sul riquadro Questetra BPM Suite nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Questetra BPM Suite.


## Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_01.png
[2]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_02.png
[3]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_03.png
[4]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_04.png
[5]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_01.png


[8]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_06.png
[9]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_02.png
[10]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_03.png
[11]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_04.png
[12]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_05.png
[13]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_06.png
[14]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_07.png
[15]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_08.png
[16]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_09.png
[17]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_10.png
[18]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_08.png


[100]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_09.png
[101]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_10.png
[102]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_11.png
[103]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_12.png
[104]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_13.png
[105]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_14.png
[106]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_15.png


[200]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_16.png
[201]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_17.png
[202]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_18.png
[203]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_19.png
[204]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_20.png
[205]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_12.png

[300]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_11.png

<!---HONumber=Oct15_HO3-->