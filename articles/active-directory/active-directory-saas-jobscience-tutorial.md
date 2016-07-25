<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Jobscience | Microsoft Azure" 
    description="Informazioni su come usare Jobscience con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="07/09/2016" 
    ms.author="jeedes" />

#Esercitazione: Integrazione di Azure Active Directory con Jobscience
  
Questa esercitazione descrive l'integrazione di Azure e Jobscience.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Sottoscrizione di Jobscience abilitata per l'accesso Single Sign-On
  
Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Jobscience potranno accedere all'applicazione tramite il sito aziendale di Jobscience (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per Jobscience
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-jobscience-tutorial/IC784341.png "Scenario")
##Abilitazione dell'integrazione dell'applicazione per Jobscience
  
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Jobscience.

###Per abilitare l'integrazione dell'applicazione per Jobscience, seguire questa procedura:

1.  Nel portale di Azure classico fare clic su **Active Directory** nel riquadro di spostamento sinistro.

    ![Active Directory](./media/active-directory-saas-jobscience-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-jobscience-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-jobscience-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-jobscience-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **jobscience**.

    ![Raccolta di applicazioni](./media/active-directory-saas-jobscience-tutorial/IC784342.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **Jobscience** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Jobscience](./media/active-directory-saas-jobscience-tutorial/IC784357.png "Jobscience")
##Configurazione dell'accesso Single Sign-On
  
Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Jobscience tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.  
La configurazione dell'accesso Single Sign-On per Jobscience richiede di recuperare un valore di identificazione personale da un certificato.  
Se non si ha familiarità con questa procedura, vedere il video che descrive [come recuperare un valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI).

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Accedere al sito aziendale di Jobscience come amministratore.

2.  Passare a **Setup**.

    ![Impostazione](./media/active-directory-saas-jobscience-tutorial/IC784358.png "Impostazione")

3.  Nella sezione **Administer** del riquadro di spostamento sinistro fare clic su **Domain Management** per espandere la sezione correlata e quindi su **My Domain** per aprire la pagina **My Domain**.

    ![My Domain](./media/active-directory-saas-jobscience-tutorial/IC767825.png "My Domain")

4.  Per verificare che il dominio sia stato configurato correttamente, assicurarsi che sia presente in "**Step 4 Deployed to Users**" (Passaggio 4 Distribuzione agli utenti) e quindi verificare le "**My Domain Settings**" (Impostazioni personali del dominio).

    ![Domain Deployed to User](./media/active-directory-saas-jobscience-tutorial/IC784377.png "Domain Deployed to User")

5.  In una finestra diversa del Web browser accedere al portale di Azure classico.

6.  Nella pagina di integrazione dell'applicazione **Jobscience** fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-jobscience-tutorial/IC784360.png "Configura accesso Single Sign-On")

7.  Nella pagina **Stabilire come si desidera che gli utenti accedano a Jobscience** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-jobscience-tutorial/IC784361.png "Configura accesso Single Sign-On")

8.  Nella casella di testo **Jobscience Sign In URL** (URL accesso Jobscience) della pagina **Configura URL app** digitare l'URL usando il modello "*http://company.my.salesforce.com*" e fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-jobscience-tutorial/IC784362.png "Configura URL app")

9.  Nella pagina **Configura accesso Single Sign-On in Jobscience** fare clic su **Download certificato** per scaricare il file di certificato e quindi salvarlo localmente nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-jobscience-tutorial/IC784363.png "Configura accesso Single Sign-On")

10. Nel sito della società Jobscience fare clic su **Security Controls** e quindi su **Single Sign-On Settings**.

    ![Security Controls](./media/active-directory-saas-jobscience-tutorial/IC784364.png "Security Controls")

11. Nella sezione **Single Sign-On Settings**, eseguire la procedura seguente:

    ![Single Sign-On Settings](./media/active-directory-saas-jobscience-tutorial/IC781026.png "Single Sign-On Settings")

    1.  Selezionare **Abilitato SAML**.
    2.  Fare clic su **New**.

12. Nella finestra di dialogo **SAML Single Sign-On Setting Edit** seguire questa procedura:

    ![SAML Single Sign-On Setting](./media/active-directory-saas-jobscience-tutorial/IC784365.png "SAML Single Sign-On Setting")

    1.  Nella casella di testo **Name** digitare un nome per la configurazione.
    2.  Nella finestra di dialogo **Configure single sign-on at Jobscience** (Configura accesso Single Sign-On in Jobscience) del portale di Azure classico copiare il valore di **URL autorità di certificazione** e incollarlo nella casella di testo **Issuer** (Autorità di certificazione).
    3.  Nella casella di testo **Entity Id**, digitare **https://salesforce-jobscience.com**.
    4.  Fare clic su **Browse** per caricare il certificato di Azure AD.
    5.  In **SAML Identity Type** selezionare **Assertion contains the Federation ID from the User object**.
    6.  In **SAML Identity Location** selezionare **Identity is in the NameIdentifier element of the Subject statement**.
    7.  Nella finestra di dialogo **Configure single sign-on at Jobscience** (Configura accesso Single Sign-On in Jobscience) del portale di Azure classico copiare il valore di **URL accesso remoto** e quindi incollarlo nella casella di testo **Identity Provider Login URL** (URL accesso provider di identità).
    8.  Nella finestra di dialogo **Configure single sign-on at Jobscience** (Configura accesso Single Sign-On in Jobscience) del portale di Azure classico copiare il valore di **URL disconnessione remota** e quindi incollarlo nella casella di testo **Identity Provider Logout URL** (URL di disconnessione provider di identità).
    9.  Fare clic su **Save**.

13. Nella sezione **Administer** del pannello di navigazione a sinistra fare clic su **Domain Management** per espandere la sezione correlata e quindi su **My Domain** per aprire la pagina **My Domain**.

    ![My Domain](./media/active-directory-saas-jobscience-tutorial/IC767825.png "My Domain")

14. Nella sezione **Login Page Branding** della pagina **My Domain** fare clic su **Edit**.

    ![Login Page Branding](./media/active-directory-saas-jobscience-tutorial/IC767826.png "Login Page Branding")

15. Nella sezione **Authentication Service** della pagina **Login Page Branding** viene visualizzato il nome delle **impostazioni SAML SSO**. Selezionarlo e quindi fare clic su **Save**.

    ![Login Page Branding](./media/active-directory-saas-jobscience-tutorial/IC784366.png "Login Page Branding")

16. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-jobscience-tutorial/IC784367.png "Configura accesso Single Sign-On")
  
Per ottenere l'URL di accesso Single Sign-On avviato dal provider di servizi, fare clic su **Single Sign-On Settings** nella sezione di menu **Security Controls**.

![Security Controls](./media/active-directory-saas-jobscience-tutorial/IC784368.png "Security Controls")
  
Fare clic sul profilo SSO creato nel passaggio precedente.  
Questa pagina mostra l'URL di accesso Single Sign-On per la società, ad esempio *https://companyname.my.salesforce.com?so=companyid*).
##Configurazione del provisioning utente
  
Per consentire agli utenti di Azure AD di accedere a Jobscience, è necessario eseguirne il provisioning in Jobscience.  
Nel caso di Jobscience, il provisioning è un'attività manuale.

###Per configurare il provisioning utente, eseguire la procedura seguente:

1.  Accedere al sito aziendale di **Jobscience** come amministratore.

2.  Passare a Setup.

    ![Impostazione](./media/active-directory-saas-jobscience-tutorial/IC784358.png "Impostazione")

3.  Passare a **Manage Users > Users**.

    ![Utenti](./media/active-directory-saas-jobscience-tutorial/IC784369.png "Utenti")

4.  Fare clic su **Nuovo utente**.

    ![All Users](./media/active-directory-saas-jobscience-tutorial/IC784370.png "All Users")

5.  Nella finestra di dialogo **Edit User** seguire la procedura seguente:

    ![User Edit](./media/active-directory-saas-jobscience-tutorial/IC784371.png "User Edit")

    1.  Immettere nelle caselle di testo correlate le proprietà relative a nome, cognome, alias, indirizzo di posta elettronica, nome utente e nome alternativo dell'utente di Azure AD di cui si vuole eseguire il provisioning.
    2.  Fare clic su **Salva**.

    >[AZURE.NOTE] Il titolare dell'account AD riceverà un messaggio di posta elettronica contenente un collegamento da selezionare per confermare e attivare l'account.

>[AZURE.NOTE] È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Jobscience per eseguire il provisioning degli account utente di AAD.

##Assegnazione degli utenti
  
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a Jobscience, seguire questa procedura:

1.  Nel portale di Azure classico creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **Jobscience** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-jobscience-tutorial/IC784372.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-jobscience-tutorial/IC767830.png "Sì")
  
Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->