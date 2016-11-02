<properties
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Domo | Microsoft Azure"
    description="Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Domo."
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



# <a name="tutorial:-azure-active-directory-integration-with-domo"></a>Esercitazione: Integrazione di Azure Active Directory con Domo

Questa esercitazione descrive l'integrazione di Domo con Azure Active Directory (Azure AD).  
L'integrazione di Domo con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Domo
- È possibile abilitare gli utenti per l'accesso automatico a Domo (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).


## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Domo, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Domo abilitata per l'accesso Single Sign-On


> [AZURE.NOTE] Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non è disponibile un ambiente di prova di Azure AD, è possibile ottenere una versione di prova di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è testare l'accesso Single Sign-On di Azure AD in un ambiente di test.  
Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Domo dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD


## <a name="adding-domo-from-the-gallery"></a>Aggiunta di Domo dalla raccolta
Per configurare l'integrazione di Domo in Azure AD, è necessario aggiungere Domo dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Domo dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applications][2]

4. Fare clic su **Add** nella parte inferiore della pagina.

    ![Applicazioni][3]

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Applicazioni][4]

6. Nella casella di ricerca digitare **Domo**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-domo-tutorial/tutorial_domo_01.png)

7. Nel riquadro dei risultati selezionare **Domo** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-domo-tutorial/tutorial_domo_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come configurare e testare l'accesso Single Sign-On di Azure AD con Domo in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Domo che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Domo.  
La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente) in Domo.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Domo, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Creazione di un utente test di Domo](#creating-a-domo-test-user)** : per avere una controparte di Britta Simon in Domo collegata alla relativa rappresentazione in Azure AD.
5. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

Questa sezione descrive come abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure classico e configurare l'accesso Single Sign-On nell'applicazione Domo.

L'applicazione Domo prevede che le asserzioni SAML abbiano un formato specifico. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla scheda **Attribute (Attributo)** dell'applicazione. La schermata seguente illustra un esempio relativo a questa operazione. 

![Configura accesso Single Sign-On](./media/active-directory-saas-domo-tutorial/tutorial_domo_06.png) 

**Per configurare Single Sign-On di Azure AD con Domo, seguire questa procedura:**


1. Nel menu visualizzato nella parte superiore della pagina di integrazione dell'applicazione **Domo** del portale di Azure classico fare clic su **Attributi**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-domo-tutorial/tutorial_general_80.png) 


1. Nella finestra di dialogo **Attributi token SAML** seguire questa procedura per ciascuna riga della tabella:

  	| Nome attributo | Valore attributo |
  	| --- | --- |    
  	| name  | user.displayname |
  	| email | user.mail |

    a. Fare clic su **aggiungi attributo utente** per aprire la finestra di dialogo **Aggiungi attributo utente**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-domo-tutorial/tutorial_general_81.png) 

    b. Nella casella di testo **Nome attributo** digitare il nome dell'attributo indicato per quella riga.

    c. Nell'elenco **Valore attributo** selezionare il valore dell'attributo indicato per quella riga.

    d. Fare clic su **Completa**.  

2. Nella pagina di integrazione dell'applicazione **Domo** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On][6] 

3. Nella pagina **Stabilire come si desidera che gli utenti accedano a Domo** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-domo-tutorial/tutorial_domo_03.png) 

4. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-domo-tutorial/tutorial_domo_04.png) 


    a. Nella casella di testo **URL di accesso** digitare l'URL usato dagli utenti per accedere all'applicazione Domo usando il modello seguente: `https://<company name>.domo.com`

    b. Fare clic su **Avanti**.

5. Nella pagina **Configura accesso Single Sign-On in Domo** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-domo-tutorial/tutorial_domo_05.png) 

    a. Fare clic su **Scarica certificato**e quindi salvare il file nel computer.

    b. Fare clic su **Avanti**.


6. Per ottenere la configurazione dell'accesso SSO per l'applicazione, contattare il team di supporto di Domo all'indirizzo [support@domo.com](mailto: support@domo.com), allegare il certificato scaricato e specificare l'**URL autorità di certificazione**, l'**URL SSO SAML** e l'**URL disconnessione**.


7. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**.

    ![Single Sign-On di Microsoft Azure AD][10]

8. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  

    ![Single Sign-On di Microsoft Azure AD][11]




### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di Azure classico.  

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-domo-tutorial/create_aaduser_09.png) 

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-domo-tutorial/create_aaduser_03.png) 

4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-domo-tutorial/create_aaduser_04.png) 

5. Nella pagina **Informazioni sull'utente** seguire questa procedura:

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-domo-tutorial/create_aaduser_05.png) 

    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.

    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina **Profilo utente** seguire questa procedura:

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-domo-tutorial/create_aaduser_06.png) 

    a. Nella casella di testo **Nome** digitare **Britta**.  

    b. Nella casella di testo **Cognome** digitare **Simon**.

    c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.

    d. Nell'elenco **Ruolo** selezionare **Utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-domo-tutorial/create_aaduser_07.png) 

8. Nella pagina **Ottieni password temporanea** seguire questa procedura:

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-domo-tutorial/create_aaduser_08.png) 

    a. Prendere nota del valore visualizzato in **Nuova password**.

    b. Fare clic su **Complete**.   



### <a name="creating-a-domo-test-user"></a>Creazione di un utente test di Domo

L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon in Domo. Domo supporta il provisioning just-in-time, che è abilitato per impostazione predefinita.

Non è necessario alcun intervento dell'utente in questa sezione. Durante un tentativo di accesso a Domo verrà creato un nuovo utente, se questo non esiste già. [Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on).

> [AZURE.NOTE] Per creare un utente manualmente, è necessario contattare il team di supporto di Domo.


### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

Questa sezione descrive come abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Domo.

![Assegna utente][200] 

**Per assegnare Britta Simon a Domo, seguire questa procedura:**

1. Per aprire la visualizzazione applicazioni nel portale di Azure classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Domo**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-domo-tutorial/tutorial_domo_50.png) 

1. Scegliere **Utenti**dal menu in alto.

    ![Assegna utente][203] 

1. Nell'elenco di utenti selezionare **Britta Simon**.

2. Fare clic su **Assegna**sulla barra degli strumenti in basso.

    ![Assegna utente][205]



### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.  
Quando si fa clic sul riquadro Domo nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Domo.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-domo-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-domo-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-domo-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-domo-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-domo-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-domo-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-domo-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-domo-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-domo-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-domo-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-domo-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-domo-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-domo-tutorial/tutorial_general_205.png



<!--HONumber=Oct16_HO2-->


