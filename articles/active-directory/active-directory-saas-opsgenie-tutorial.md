<properties
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con OpsGenie | Microsoft Azure"
    description="Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e OpsGenie."
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
    ms.date="10/07/2016"
    ms.author="jeedes"/>



# <a name="tutorial:-azure-active-directory-integration-with-opsgenie"></a>Esercitazione: Integrazione di Azure Active Directory con OpsGenie

Questa esercitazione descrive l'integrazione di OpsGenie con Azure Active Directory (Azure AD).

L'integrazione di OpsGenie con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a OpsGenie
- È possibile abilitare gli utenti per l'accesso automatico a OpsGenie (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con OpsGenie, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di OpsGenie abilitata per l'accesso Single Sign-On


> [AZURE.NOTE] Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è testare l'accesso Single Sign-On di Azure AD in un ambiente di test. 

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di OpsGenie dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD


## <a name="adding-opsgenie-from-the-gallery"></a>Aggiunta di OpsGenie dalla raccolta
Per configurare l'integrazione di OpsGenie in Azure AD, è necessario aggiungere OpsGenie dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere OpsGenie dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applications][2]

4. Fare clic su **Add** nella parte inferiore della pagina.

    ![Applicazioni][3]

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Applicazioni][4]

6. Nella casella di ricerca digitare **OpsGenie**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_01.png)

7. Nel riquadro dei risultati selezionare **OpsGenie** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come configurare e testare l'accesso Single Sign-On di Azure AD con OpsGenie in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di OpsGenie che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in OpsGenie.

La relazione di collegamento viene stabilita assegnando al valore di **nome utente** in Azure AD lo stesso valore di **Username** in OpsGenie.

Per configurare e testare l'accesso Single Sign-On di Azure AD con OpsGenie, è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Creazione di un utente di test di OpsGenie](#creating-a-opsgenie-test-user)** : per avere una controparte di Britta Simon in OpsGenie collegata alla relativa rappresentazione in Azure AD.
5. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

Questa sezione descrive come abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure classico e configurare l'accesso Single Sign-On nell'applicazione OpsGenie.



**Per configurare Single Sign-On di Azure AD con OpsGenie, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **OpsGenie** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On][6] 

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a OpsGenie** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_03.png) 

3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_04.png) 


    a. Nella casella di testo URL di accesso, digitare l'URL usato dagli utenti per accedere all'applicazione OpsGenie adottando il modello seguente: **“https://app.opsgenie.com/auth/login”**.

    > [AZURE.NOTE] Se è necessario ottenere l'URL di accesso, contattare il [team di supporto di OpsGenie](mailto:support@opsgenie.com) .

    b. Fare clic su **Avanti**.


4. Nella pagina **Configura accesso Single Sign-On in OpsGenie** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_05.png) 

    a. Fare clic su **Scarica certificato**e quindi salvare il file nel computer. Questo certificato e gli URL dei metadati (ID entità, URL di accesso SSO e URL di disconnessione SSO) saranno necessari per configurare l'accesso Single Sign-On nel sito OpsGenie.

    b. Fare clic su **Avanti**.


5. Aprire un'altra istanza del browser e quindi accedere a OpsGenie come amministratore.

6. Fare clic su **Settings** e quindi selezionare la scheda **Single Sign On**.
 
    ![Accesso Single Sign-On di OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_06.png) 

7. Per abilitare l'accesso Single Sign-On, selezionare **Enabled**.

    ![Impostazioni di OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_07.png) 
   
8. Nella sezione **Provider** fare clic sulla scheda **Azure Active Directory**.

    ![Impostazioni di OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_08.png) 
    
9. Nella pagina Azure Active Directory, seguire questa procedura:
 
    ![Impostazioni di OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_09.png) 

    a. Nella pagina **Configura accesso Single Sign-On in OpsGenie** del portale di Azure classico copiare il valore di **URL servizio Single Sign-On** e incollarlo nella casella di testo **SAML 2.0 Endpoint**.

    b. Creare un file con codifica Base 64 dal certificato scaricato.      
    
    > [AZURE.NOTE] Per informazioni dettagliate, vedere il video che illustra [come convertire un certificato binario in un file di testo](https://www.youtube.com/watch?v=PlgrzUZ-Y1o&feature=youtu.be).

    c. Aprire il certificato con codifica Base 64 nel Blocco note, copiarne il contenuto negli Appunti e incollarlo nella casella di testo **X.500 Certificate** .

    d. Fare clic su **Salva modifiche**.


6. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**.

    ![Single Sign-On di Microsoft Azure AD][10]

7. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  

    ![Single Sign-On di Microsoft Azure AD][11]




### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di Azure classico.



![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_09.png) 

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_03.png) 

4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_04.png) 

5. Nella pagina **Informazioni sull'utente** seguire questa procedura:

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_05.png) 

    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.

    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina **Profilo utente** seguire questa procedura:

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_06.png) 

    a. Nella casella di testo **Nome** digitare **Britta**.  

    b. Nella casella di testo **Cognome** digitare **Simon**.

    c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.

    d. Nell'elenco **Ruolo** selezionare **Utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_07.png) 

8. Nella pagina **Ottieni password temporanea** seguire questa procedura:

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_08.png) 

    a. Prendere nota del valore visualizzato in **Nuova password**.

    b. Fare clic su **Complete**.   



### <a name="creating-a-opsgenie-test-user"></a>Creazione di un utente di test di OpsGenie

Questa sezione descrive come creare un utente chiamato Britta Simon in OpsGenie. 

1.  In una finestra del Web browser accedere al tenant di OpsGenie come amministratore.

2.  Passare all'elenco di utenti facendo clic su **User** nel pannello a sinistra.
   
    ![Impostazioni di OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_10.png) 

3.  Fare clic su "**Aggiungi utente**".

3.  Nella finestra di dialogo **Aggiungi utente** seguire questa procedura:

    ![Impostazioni di OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_11.png) 

    a. Nella casella di testo **E-mail** , digitare l'indirizzo di posta elettronica di Britta in Azure Active Directory.

    b. Nella casella di testo **Nome completo** digitare **Britta Simon**.

    c. Fare clic su **Save**. 

Britta riceverà un messaggio di posta elettronica con istruzioni per la configurazione del profilo.


### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

Questa sezione descrive come abilitare Britta Simon per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a OpsGenie.

![Assegna utente][200] 

**Per assegnare Britta Simon a OpsGenie, seguire questa procedura:**

1. Per aprire la visualizzazione applicazioni nel portale di Azure classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.
 
    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **OpsGenie**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_50.png) 

1. Scegliere **Utenti**dal menu in alto.

    ![Assegna utente][203] 

1. Nell'elenco di utenti selezionare **Britta Simon**.

2. Fare clic su **Assegna**sulla barra degli strumenti in basso.

    ![Assegna utente][205]



### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro OpsGenie nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione OpsGenie.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_205.png



<!--HONumber=Oct16_HO2-->


