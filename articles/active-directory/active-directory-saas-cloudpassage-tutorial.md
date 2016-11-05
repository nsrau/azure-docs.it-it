---
title: 'Esercitazione: Integrazione di Azure Active Directory con CloudPassage | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e CloudPassage.
services: active-directory
documentationcenter: ''
author: jeevansd
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2016
ms.author: jeedes

---
# <a name="tutorial:-azure-active-directory-integration-with-cloudpassage"></a>Esercitazione: Integrazione di Azure Active Directory con CloudPassage
Questa esercitazione descrive l'integrazione di CloudPassage con Azure Active Directory (Azure AD).  
L'integrazione di CloudPassage con Azure AD offre i vantaggi seguenti: 

* È possibile controllare in Azure AD chi può accedere a CloudPassage. 
* È possibile abilitare gli utenti per l'accesso automatico a CloudPassage (Single Sign-On) con i propri account Azure AD.
* È possibile gestire gli account da una posizione centrale: Azure Active Directory 

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD con CloudPassage, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure AD.
* Sottoscrizione di CloudPassage abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.
> 
> 

A questo scopo, è consigliabile seguire le indicazioni seguenti:

* Non usare l'ambiente di produzione, a meno che non sia necessario.
* Se non si dispone di un ambiente di test di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è testare l'accesso Single Sign-On di Azure AD in un ambiente di test.  
Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di CloudPassage dalla raccolta 
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-cloudpassage-from-the-gallery"></a>Aggiunta di CloudPassage dalla raccolta
Per configurare l'integrazione di CloudPassage in Azure AD, è necessario aggiungere CloudPassage dalla raccolta al proprio elenco di app SaaS gestite.

### <a name="to-add-cloudpassage-from-the-gallery,-perform-the-following-steps:"></a>Per aggiungere CloudPassage dalla raccolta, seguire questa procedura:
1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro. 
   
    ![Active Directory][1]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications][2]
4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Applicazioni][3]
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Applicazioni][4]
6. Nella casella di ricerca digitare **CloudPassage**.
   
    ![Applicazioni][5]
7. Nel riquadro dei risultati selezionare **CloudPassage** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Applicazioni][6]

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come configurare e testare l'accesso Single Sign-On di Azure AD con CloudPassage in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di CloudPassage che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in CloudPassage.  
La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente) in CloudPassage.

Per configurare e testare l'accesso Single Sign-On di Azure AD con CloudPassage, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test CloudPassage](#creating-a-halogen-software-test-user)** : per avere una controparte di Britta Simon in CloudPassage collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure AD classico e configurare l'accesso Single Sign-On nell'applicazione CloudPassage.  
L'applicazione CloudPassage prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. La schermata seguente illustra un esempio relativo a questa operazione.

![Configura accesso Single Sign-On][21]

**Per configurare Single Sign-On di Azure AD con CloudPassage, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **CloudPassage** del portale di Azure AD classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On][7]
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a CloudPassage** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On][8]
3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura: 
   
    ![Configurare le impostazioni dell'app][9]
   
    a. Nella casella di testo **URL di accesso** digitare l'URL usato dagli utenti per accedere all'applicazione CloudPassage, ad esempio: *https://portal.cloudpassage.com/saml/init/accountid*. 
   
    b. Nella casella di testo **URL di risposta** digitare l'URL AssertionConsumerService, ad esempio: *https://portal.cloudpassage.com/saml/consume/accountid*. È possibile ottenere il valore di questo attributo facendo clic su **SSO Setup documentation** (Documentazione della configurazione SSO) nella sezione **Single Sign-on Settings** (Impostazioni Single Sign-On) del portale di CloudPassage.  
    ![Configura accesso Single Sign-On][10]
   
    C. Fare clic su **Avanti**.
4. Nella pagina **Configura accesso Single Sign-On in CloudPassage** fare clic su **Download certificato** e infine salvare il certificato localmente nel computer. 
   
    ![Configura accesso Single Sign-On][11]
5. In un'altra finestra del Web browser accedere al sito aziendale di CloudPassage come amministratore.
6. Nel menu in alto fare clic su **Settings** (Impostazioni) e quindi su **Site Administration** (Amministrazione sito). 
   
    ![Configura accesso Single Sign-On][12]
7. Fare clic sulla scheda **Authentication Settings** (Impostazioni autenticazione). 
   
    ![Configura accesso Single Sign-On][13]
8. Nella sezione **Single Sign-On Settings** (Impostazioni Single Sign-On) seguire questa procedura: 
   
    ![Configura accesso Single Sign-On][14]

    a. Nella finestra di dialogo **Configura accesso Single Sign-On in CloudPassage** del portale di Azure classico copiare il valore di **URL autorità di certificazione** e incollarlo nella casella di testo **SAML issuer URL** (URL autorità di certificazione SAML).

    b. Nella finestra di dialogo **Configura accesso Single Sign-On in CloudPassage** del portale di Azure classico copiare il valore di **Endpoint avviato da provider di servizi** e incollarlo nella casella di testo **SAML endpoint URL** (URL endpoint SAML).

    c. Nella finestra di dialogo **Configura accesso Single Sign-On in CloudPassage** del portale di Azure classico copiare il valore di **URL disconnessione** e incollarlo nella casella di testo **Logout landing page** (Pagina di destinazione disconnessione).

    d. Creare un file con codifica **Base 64** dal certificato scaricato. 

    >[AZURE.TIP] Per informazioni dettagliate, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

    e. Aprire il certificato con codifica Base 64 nel Blocco note, copiarne il contenuto negli Appunti e incollarlo nella casella di testo **x 509 certificate** .

    f. Fare clic su **Save**.


1. Nel portale di Azure AD classico selezionare la conferma della configurazione e fare clic su **Avanti**. 
   
    ![Configura accesso Single Sign-On][15]
2. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**. 
   
   ![Configura accesso Single Sign-On][16]
3. Nel menu in alto fare clic su **Attributi** per aprire la finestra di dialogo **Attributi token SAML**. 
   
   ![Configura accesso Single Sign-On][17]
4. Per aggiungere gli attributi utente necessari, per ciascuna riga della tabella seguire questa procedura: 
   
   | Nome attributo | Valore attributo |
   | --- | --- |
   | firstname |user.givenname |
   | lastname |user.surname |
   | email |user.mail |

    a. Fare clic su **Aggiungi attributo utente**. 

    ![Configura accesso Single Sign-On][18]

    b. Nella casella di testo **Nome attributo** digitare il nome dell'attributo visualizzato per la riga. In **Valore attributo** selezionare il valore dell'attributo visualizzato per la riga. 

    ![Configura accesso Single Sign-On][19]

    c. Fare clic su **Complete**.


1. Fare clic su **Applica modifiche**sulla barra degli strumenti in basso. 
   
   ![Configura accesso Single Sign-On][20]

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di Azure classico.  

![Creazione di un utente test di Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_01.png)

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_02.png) 
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_03.png) 
4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso. 
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_04.png) 
5. Nella pagina **Informazioni sull'utente** seguire questa procedura: 
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_05.png) 
   
    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.
   
    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
   
    c. Fare clic su Avanti.
6. Nella pagina **Profilo utente** seguire questa procedura: 
   
   ![Creazione di un utente test di Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_06.png) 
   
   a. Nella casella di testo **Nome** digitare **Britta**.  
   
   b. Nella casella di testo **Cognome** digitare **Simon**.
   
   c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
   
   d. Nell'elenco **Ruolo** selezionare **Utente**.
   
   e. Fare clic su **Avanti**.
7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_07.png) 
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_08.png) 
   
    a. Prendere nota del valore visualizzato in **Nuova password**.
   
    b. Fare clic su **Complete**.   

### <a name="creating-a-cloudpassage-test-user"></a>Creazione di un utente test CloudPassage
Questa sezione descrive come creare un utente chiamato Britta Simon in CloudPassage.

#### <a name="to-create-a-user-called-britta-simon-in-cloudpassage,-perform-the-following-steps:"></a>Per creare un utente test denominato Britta Simon in CloudPassage, seguire questa procedura:
1. Accedere al sito aziendale di **CloudPassage** come amministratore. 
2. Nella barra degli strumenti in alto fare clic su **Settings** (Impostazioni) e quindi su **Site Administration** (Amministrazione sito). 
   
   ![Creazione di un utente test CloudPassage][22] 
3. Fare clic sulla scheda **Users** (Utenti) e quindi fare clic su **Add New User** (Aggiungi nuovo utente). 
   
   ![Creazione di un utente test CloudPassage][23]
4. Nella sezione **Aggiungi nuovo utente** eseguire la procedura seguente: 
   
   ![Creazione di un utente test CloudPassage][24]
   
   a. Nella casella di testo **Nome** digitare Britta.
   
   b. Nella casella di testo **Cognome** digitare Simon.
   
   c. Nelle caselle di testo **Username** (Nome utente), **Email** (Indirizzo di posta elettronica) e **Retype Email** (Digitare nuovamente l'indirizzo di posta elettronica) digitare il nome utente di Britta in Azure AD.
   
   d. In **Access Type** (Tipo di accesso) selezionare **Enable Halo Portal Access** (Abilita accesso portale Halo).
   
   e. Fare clic su **Aggiungi**.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD
Questa sezione descrive come abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a CloudPassage.

![Assegna utente][30]

**Per assegnare Britta Simon ad Amazon Web Service (AWS), seguire questa procedura:**

1. Per aprire la visualizzazione applicazioni nel portale di Azure classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.
   
    ![Assegna utente][26]
2. Nell'elenco delle applicazioni selezionare **CloudPassage**.
   
    ![Assegna utente][27]
3. Scegliere **Utenti**dal menu in alto.
   
    ![Assegna utente][25]
4. Nell'elenco di utenti selezionare **Britta Simon**.
5. Fare clic su **Assegna**sulla barra degli strumenti in basso.
   
    ![Assegna utente][29]

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On
Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.  
Quando si fa clic sul riquadro CloudPassage nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione CloudPassage.

## <a name="additional-resources"></a>Risorse aggiuntive
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
























<!--HONumber=Oct16_HO2-->


