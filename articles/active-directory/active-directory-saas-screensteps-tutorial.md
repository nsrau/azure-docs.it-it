<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con ScreenSteps | Microsoft Azure" 
    description="Informazioni su come usare ScreenSteps con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/22/2015" 
    ms.author="markvi" />

#Esercitazione: Integrazione di Azure Active Directory con ScreenSteps
  
In questa esercitazione viene illustrata l'integrazione di Azure e ScreenSteps. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Tenant ScreenSteps
  
Al termine dell'esercitazione, gli utenti di Azure AD assegnati a ScreenSteps saranno in grado di eseguire l’accesso Single Sign-On all'applicazione tramite il sito aziendale di ScreenSteps (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per ScreenSteps
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-screensteps-tutorial/IC778516.png "Scenario")
##Abilitazione dell'integrazione dell'applicazione per ScreenSteps
  
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per ScreenSteps.

###Per abilitare l'integrazione dell'applicazione per ScreenSteps, eseguire la procedura seguente:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel pannello di navigazione sinistro.

    ![Active Directory](./media/active-directory-saas-screensteps-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-screensteps-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-screensteps-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-screensteps-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **ScreenSteps**.

    ![Raccolta di applicazioni](./media/active-directory-saas-screensteps-tutorial/IC778517.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **ScreenSteps**, quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![ScreenSteps](./media/active-directory-saas-screensteps-tutorial/IC778518.png "ScreenSteps")
##Configurazione dell'accesso Single Sign-On
  
In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a ScreenSteps tramite il proprio account di Azure AD usando la federazione basata sul protocollo SAML.

###Per configurare l'accesso Single Sign-On, eseguire la procedura seguente:

1.  Nella pagina di integrazione dell'applicazione **ScreenSteps** del portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-screensteps-tutorial/IC778519.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a ScreenSteps** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-screensteps-tutorial/IC778520.png "Configura accesso Single Sign-On")

3.  Nella pagina **Configura URL app** digitare l'URL nella casella di testo **URL di accesso ScreenSteps** usando il modello seguente "*https://\<tenant-name>.ScreenSteps.com*" e fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-screensteps-tutorial/IC778521.png "Configura URL app")

4.  Nella pagina **Configura accesso Single Sign-On in ScreenSteps** fare clic su **Scarica certificato** per scaricare il certificato e salvare il file di certificato sul computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-screensteps-tutorial/IC778522.png "Configura accesso Single Sign-On")

5.  In un'altra finestra del Web browser accedere al sito aziendale di ScreenSteps come amministratore.

6.  Fare clic su **Account Management**.

    ![Gestione account](./media/active-directory-saas-screensteps-tutorial/IC778523.png "Gestione account")

7.  Fare clic su **Remote Authentication**.

    ![Autenticazione remota](./media/active-directory-saas-screensteps-tutorial/IC778524.png "Autenticazione remota")

8.  Fare clic su **Create authentication endpoint**.

    ![Autenticazione remota](./media/active-directory-saas-screensteps-tutorial/IC778525.png "Autenticazione remota")

9.  Nella sezione **Create an Authentication Endpoint**, eseguire la procedura seguente:

    ![Crea endpoint di autenticazione](./media/active-directory-saas-screensteps-tutorial/IC778526.png "Crea endpoint di autenticazione")

    1.  Nella casella di testo **Title**, digitare un titolo.
    2.  Dall’elenco **Mode**, selezionare **SAML**.
    3.  Fare clic su **Crea**.

10. Nella sezione **Remote Authentication Endpoint**, eseguire la procedura seguente:

    ![Endpoint di autenticazione remota](./media/active-directory-saas-screensteps-tutorial/IC778527.png "Endpoint di autenticazione remota")

    1.  Nella pagina **Configura accesso Single Sign-On in ScreenSteps** del portale di Azure AD copiare il valore di **URL accesso remoto** e incollarlo nella casella di testo **Remote Login URL**.
    2.  Nella pagina **Configura accesso Single Sign-On in ScreenSteps** del portale di Azure AD copiare il valore di **URL disconnessione remota** e incollarlo nella casella di testo **Log out URL**.
    3.  Fare clic su **Choose a file** e quindi caricare il certificato scaricato.
    4.  Fare clic su **Update**.

11. Nel portale di Azure AD, selezionare la conferma della configurazione dell'accesso Single Sign-On, quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-screensteps-tutorial/IC778542.png "Configura accesso Single Sign-On")
##Configurazione del provisioning utente
  
Per consentire agli utenti di Azure AD di accedere a **ScreenSteps**, è necessario eseguirne il provisioning in **ScreenSteps**. Nel caso di **ScreenSteps**, il provisioning è un'attività manuale.

###Per eseguire il provisioning di un account utente in ScreenSteps, eseguire la procedura seguente:

1.  Accedere al tenant **ScreenSteps**.

2.  Fare clic su **Account Management**.

    ![Gestione account](./media/active-directory-saas-screensteps-tutorial/IC778523.png "Gestione account")

3.  Fare clic su **Users**.

    ![Utenti](./media/active-directory-saas-screensteps-tutorial/IC778544.png "Utenti")

4.  Fare clic su **Create a user**.

    ![All Users](./media/active-directory-saas-screensteps-tutorial/IC778545.png "Tutti gli utenti")

5.  Dall’elenco **User Role** selezionare un ruolo per l'utente.

6.  Nella sezione Ruolo utente, digitare il **nome**, il **cognome**, l’**indirizzo di posta elettronica**, l’**account di accesso**, la **password** e la **password di conferma** di un account di ADD valido di cui si desidera eseguire il provisioning nelle relative caselle di testo.

    ![Nuovo utente](./media/active-directory-saas-screensteps-tutorial/IC778546.png "Nuovo utente")

7.  Nella sezione Groups selezionare **Authentication Group (SAML)** e fare clic su **Create User**.

    ![Gruppi](./media/active-directory-saas-screensteps-tutorial/IC778547.png "Gruppi")

>[AZURE.NOTE]È possibile usare qualsiasi altro strumento o API di creazione di account utente offerti da ScreenSteps per eseguire il provisioning degli account utente di Azure AD.

##Assegnazione degli utenti
  
Per testare la configurazione, è necessario concedere l'accesso all’applicazione agli utenti di Azure AD a cui si desidera consentirne l’uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti ad ScreenSteps eseguire la procedura seguente:

1.  Nel portale di Azure AD creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **ScreenSteps** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-screensteps-tutorial/IC773094.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.

    ![Assegna utenti](./media/active-directory-saas-screensteps-tutorial/IC778548.png "Assegna utenti")
  
Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=Nov15_HO1-->