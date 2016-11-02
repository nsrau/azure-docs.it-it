<properties
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Promapp | Microsoft Azure"
    description="Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Promapp."
    services="active-directory"
    documentationCenter=""
    authors="jeevansd"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="jeedes"/>



# <a name="tutorial:-azure-active-directory-integration-with-promapp"></a>Esercitazione: Integrazione di Azure Active Directory con Promapp

Questa esercitazione descrive l'integrazione di Promapp con Azure Active Directory (Azure AD).  
L'integrazione di Promapp con Azure AD offre i vantaggi seguenti: 

- È possibile controllare in Azure AD chi può accedere a Promapp 
- È possibile abilitare gli utenti per l'accesso automatico a Promapp (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in una posizione centrale, il portale di Azure Active Directory classico.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti 

Per configurare l'integrazione di Azure AD con Promapp, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Promapp abilitata per l'accesso Single Sign-On


> [AZURE.NOTE] Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non è disponibile un ambiente di prova di Azure AD, è possibile ottenere una versione di prova di un mese [qui](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è testare l'accesso Single Sign-On di Azure AD in un ambiente di test.  
Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Promapp dalla raccolta 
2. Configurazione e test dell'accesso Single Sign-On di Azure AD


## <a name="adding-promapp-from-the-gallery"></a>Aggiunta di Promapp dalla raccolta
Per configurare l'integrazione di Promapp in Azure AD, è necessario aggiungere Promapp dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Promapp dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applications][2]

4. Fare clic su **Add** nella parte inferiore della pagina.

    ![Applicazioni][3]

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Applicazioni][4]

6. Nella casella di ricerca digitare **Promapp**.

    ![Applicazioni][5]

7. Nel riquadro dei risultati selezionare **Promapp** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Applicazioni][500]

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come configurare e testare l'accesso Single Sign-On di Azure AD con Promapp con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Promapp che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Promapp.  
La relazione di collegamento viene stabilita assegnando al valore di **nome utente** in Azure AD lo stesso valore di **Username** in Promapp.
 
Per configurare e testare l'accesso Single Sign-On di Azure AD con Promapp, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Creazione di un utente test per Promapp](#creating-a-halogen-software-test-user)** : per avere una controparte di Britta Simon in Promapp collegata alla relativa rappresentazione in Azure AD.
5. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

Questa sezione descrive come abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure AD classico e configurare l'accesso Single Sign-On nell'applicazione Promapp.

**Per configurare l'accesso Single Sign-On di Azure AD con Promapp, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Promapp** del portale di Azure AD classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On][6] 

2. Nella pagina **How would you like users to sign on to Promapp** (Stabilire come si desidera che gli utenti accedano a Promapp) selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.

    ![Single Sign-On di Microsoft Azure AD][7] 

3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:

    ![Accesso Single Sign-On di Azure AD][8] 
 
     a. Nella casella di testo **URL di accesso** digitare l'URL utilizzato dagli utenti per accedere al sito Promapp, ad esempio *https://companyname.promapp.com/instancename*.


     b. Fare clic su **Next**.
 
4. Nella pagina **Configure single sign-on at Promapp** (Configura accesso Single Sign-On in Promapp) seguire questa procedura:

    ![Accesso Single Sign-On di Azure AD][9] 

    a. Fare clic su Download certificato e quindi salvare il file nel computer.

    b. Fare clic su **Next**.


6. Accedere al sito aziendale di Promapp come amministratore. 

6. Nel menu in alto fare clic su **Admin**. 

    ![Accesso Single Sign-On di Azure AD][12]

6. Fare clic su **Configure**. 

    ![Accesso Single Sign-On di Azure AD][13]



4. Nella finestra di dialogo **Security** (Sicurezza) seguire questa procedura:

    ![Accesso Single Sign-On di Azure AD][14] 

    a. Nella pagina **Configure single sign-on at Promapp** (Configura accesso Single Sign-On in Promapp) del portale di Azure classico copiare il valore di **URL accesso remoto**, incollarlo nella casella di testo **SSO-Login URL** (URL di accesso SSO) e quindi fare clic su **Save** (Salva).

    b. Per **SSO - Single Sign-on Mode** selezionare **Optional** e quindi fare clic su **Save**.

    c. Aprire il certificato scaricato nel Blocco note, copiare il contenuto del certificato senza la prima riga (*-----BEGIN CERTIFICATE-----*) e l'ultima riga (*-----END CERTIFICATE-----*), incollarlo nella casella di testo **SSO-x.509 Certificate** e quindi fare clic su **Save**.




6. Nel portale di Azure AD classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**. 

    ![Single Sign-On di Microsoft Azure AD][10]

7. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  

    ![Single Sign-On di Microsoft Azure AD][11]




### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di Azure classico.

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-promapp-tutorial/create_aaduser_09.png)  

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-promapp-tutorial/create_aaduser_03.png) 
 
4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso. 

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-promapp-tutorial/create_aaduser_04.png) 

5. Nella pagina **Informazioni sull'utente** seguire questa procedura: 

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-promapp-tutorial/create_aaduser_05.png)  

    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.

    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina **Profilo utente** seguire questa procedura: 

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-promapp-tutorial/create_aaduser_06.png) 
 
    a. Nella casella di testo **Nome** digitare **Britta**.  

    b. Nella casella di testo **Cognome** digitare **Simon**.

    c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.

    d. Nell'elenco **Ruolo** selezionare **Utente**.
    e. Fare clic su **Avanti**.

7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-promapp-tutorial/create_aaduser_07.png) 
 
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-promapp-tutorial/create_aaduser_08.png) 
  
    a. Prendere nota del valore visualizzato in **Nuova password**.

    b. Fare clic su **Complete**.   

  
 
### <a name="creating-a-promapp-test-user"></a>Creazione di un utente test per Promapp

L'applicazione Promapp supporta il provisioning JIT (just-in-time).
Questo significa che, se necessario, un account utente viene creato automaticamente durante il tentativo di accedere all'applicazione tramite il pannello di accesso.  


### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

Questa sezione descrive come abilitare Britta Simon per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Promapp.

![Assegna utente][200] 

**Per assegnare Britta Simon a Promapp, seguire questa procedura:**

1. Per aprire la visualizzazione applicazioni nel portale di Azure classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **Promapp**.

    ![Assegna utente][202] 

1. Scegliere **Utenti**dal menu in alto.

    ![Assegna utente][203] 

1. Nell'elenco di utenti selezionare **Britta Simon**.

2. Fare clic su **Assegna**sulla barra degli strumenti in basso.

    ![Assegna utente][205]



### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.  
Quando si fa clic sul riquadro Promapp nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Promapp.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_01.png

[500]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_500.png

[6]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_02.png
[8]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_03.png
[9]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_04.png
[10]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_07.png
[12]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_05.png
[13]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_06.png
[14]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_07.png

[20]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_10.png
[203]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_400.png
[401]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_401.png
[402]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_402.png



<!--HONumber=Oct16_HO2-->


