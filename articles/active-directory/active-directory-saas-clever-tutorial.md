<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Clever | Microsoft Azure" 
    description="Informazioni su come usare Clever con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/22/2015" 
    ms.author="markvi" />

#Esercitazione: Integrazione di Azure Active Directory con Clever

Questa esercitazione descrive l'integrazione di Azure e Clever. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Tenant di Clever

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Clever potranno accedere all'applicazione tramite il sito aziendale di Clever (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per Clever
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-clever-tutorial/IC798977.png "Scenario")
##Abilitazione dell'integrazione dell'applicazione per Clever

Questa sezione descrive come abilitare l'integrazione dell'applicazione per Clever.

###Per abilitare l'integrazione dell'applicazione per Clever, seguire questa procedura:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel pannello di navigazione sinistro.

    ![Active Directory](./media/active-directory-saas-clever-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-clever-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-clever-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-clever-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **Clever**.

    ![Raccolta di applicazioni](./media/active-directory-saas-clever-tutorial/IC798978.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **Clever** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Clever](./media/active-directory-saas-clever-tutorial/IC798979.png "Clever")
##Configurazione dell'accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Clever tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML. L'applicazione Clever prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione di **Attributi token SAML**. La schermata seguente illustra un esempio relativo a questa operazione.

![Attributi](./media/active-directory-saas-clever-tutorial/IC798980.png "Attributi")

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Nella pagina di integrazione dell'applicazione **Clever** del portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-clever-tutorial/IC784682.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a Clever** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-clever-tutorial/IC798981.png "Configura accesso Single Sign-On")

3.  Nella casella di testo **URL di accesso Clever** della pagina **Configura URL app** digitare l'URL usato dagli utenti per accedere all'applicazione Clever, ad esempio: **https://clever.com/in/azsandbox*), e quindi fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-clever-tutorial/IC798982.png "Configura URL app")

4.  Nella pagina **Configura accesso Single Sign-On in Clever** fare clic su **Download metadati** per scaricare il file di metadati e quindi salvarlo nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-clever-tutorial/IC798983.png "Configura accesso Single Sign-On")

5.  In un'altra finestra del Web browser accedere al sito aziendale di Clever come amministratore.

6.  Nella barra degli strumenti fare clic su **Instant Login**.

    ![Instant Login](./media/active-directory-saas-clever-tutorial/IC798984.png "Instant Login")

7.  Nella pagina **Instant Login** seguire questa procedura:

    ![Instant Login](./media/active-directory-saas-clever-tutorial/IC798985.png "Instant Login")

    1.  Digitare un valore in **URL di accesso**.  

        >[AZURE.NOTE]Per **URL di accesso** usare un valore personalizzato. È possibile ottenere il valore effettivo dal team di supporto di Clever.

    2.  Come **Sistema di identità** selezionare **ADFS**.
    3.  Fare clic su **Save**.

8.  Nel portale di Azure AD, selezionare la conferma della configurazione dell'accesso Single Sign-On, quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-clever-tutorial/IC798986.png "Configura accesso Single Sign-On")

9.  Nel menu in alto fare clic su **Attributi** per aprire la finestra di dialogo **Attributi token SAML**.

    ![Attributi](./media/active-directory-saas-clever-tutorial/IC795920.png "Attributi")

10. Per aggiungere i mapping di attributi obbligatori, eseguire la procedura seguente:

    ![Attributi token SAML](./media/active-directory-saas-clever-tutorial/IC795921.png "Attributi token SAML")

	|Nome attributo|Valore attributo|
    |---|---|
    |clever.student.credentials.district\_username|User.userprincipalname|

    1.  Per ogni riga di dati nella tabella precedente, fare clic su **aggiungi attributo utente**.
    2.  Nella casella di testo **Nome attributo**, digitare il nome dell'attributo indicato per la riga.
    3.  Nella casella di testo **Valore attributo**, selezionare il valore dell'attributo indicato per la riga.
    4.  Fare clic su **Complete**.

11. Fare clic su **Applica modifiche**.

##Configurazione del provisioning utente

Per consentire agli utenti di Azure AD di accedere a Clever, è necessario eseguirne il provisioning in Clever. Nel caso di Clever, il provisioning è un'attività manuale che deve essere eseguita dal team di supporto di Clever.

>[AZURE.NOTE]È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Clever per eseguire il provisioning degli account utente di AAD.

##Assegnazione degli utenti

Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a Clever seguire questa procedura:

1.  Nel portale di Azure AD creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **Clever** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-clever-tutorial/IC798987.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-clever-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=Nov15_HO1-->