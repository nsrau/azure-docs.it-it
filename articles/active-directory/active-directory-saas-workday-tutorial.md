<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Workday | Microsoft Azure" 
    description="Informazioni su come usare Workday con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." 
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
    ms.date="09/09/2016" 
    ms.author="jeedes" />

#Esercitazione: Integrazione di Azure Active Directory con Workday
  
Questa esercitazione descrive l'integrazione di Azure e Workday. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Tenant di Workday
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per Workday
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Configurazione del provisioning utente

![Scenario](./media/active-directory-saas-workday-tutorial/IC782919.png "Scenario")

##Abilitazione dell'integrazione dell'applicazione Workday
  
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Salesforce.

###Per abilitare l'integrazione dell'applicazione per Workday, seguire questa procedura:

1.  Nel portale di Azure classico fare clic su **Active Directory** nel riquadro di spostamento sinistro.

    ![Active Directory](./media/active-directory-saas-workday-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-workday-tutorial/IC700994.png "Applicazioni")

4.  Per aprire la **Raccolta di applicazioni**, fare clic su **Aggiungi app**, quindi fare clic su **Aggiungi un'applicazione che verrà utilizzata dall'organizzazione**.

    ![Come procedere](./media/active-directory-saas-workday-tutorial/IC700995.png "Come procedere")

5.  Nella **casella di ricerca** digitare **Workday**.

    ![Workday](./media/active-directory-saas-workday-tutorial/IC701021.png "Workday")

6.  Nel riquadro dei risultati selezionare **Workday** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Workday](./media/active-directory-saas-workday-tutorial/IC701022.png "Workday")

##Configurazione dell'accesso Single Sign-On
  
Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Workday tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML. Per eseguire questa procedura, è necessario creare un certificato con codifica Base 64. Se non si ha familiarità con questa procedura, vedere il video che descrive [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Nella pagina di integrazione dell'applicazione **Workday** fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-workday-tutorial/IC782920.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a Workday** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-workday-tutorial/IC782921.png "Configura accesso Single Sign-On")

3.  Nella pagina **Configura URL app** seguire questa procedura e quindi fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-workday-tutorial/IC782957.png "Configura URL app")

	a. Nella casella di testo **URL di accesso** digitare l'URL usato dagli utenti per accedere a Workday usando il modello seguente: `https://impl.workday.com/<tenant>/login-saml2.htmld`

	b. Nella casella di testo **URL di risposta Workday** digitare l'URL di risposta di Workday usando il modello seguente: `https://impl.workday.com/<tenant>/login-saml.htmld`

	>[AZURE.NOTE] L'URL di risposta deve disporre di un sottodominio (ad esempio: www, wd2, wd3, wd3-impl, wd5, wd5-impl). Un URL simile a "*http://www.myworkday.com*" funziona; un URL simile a "*http://myworkday.com*" non funziona.
 
4.  Nella pagina **Configura accesso Single Sign-On in Workday** per scaricare il file del certificato, fare clic su **Download certificato** e quindi salvarlo nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-workday-tutorial/IC782922.png "Configura accesso Single Sign-On")

5.  In un'altra finestra del Web browser accedere al sito aziendale di Workday come amministratore.

6.  Passare a **Menu > Workbench**.

    ![Workbench](./media/active-directory-saas-workday-tutorial/IC782923.png "Workbench")

7.  Passare a **Account Administration**.

    ![Account Administration](./media/active-directory-saas-workday-tutorial/IC782924.png "Account Administration")

8.  Passare a **Edit Tenant Setup – Security**.

    ![Edit Tenant Security](./media/active-directory-saas-workday-tutorial/IC782925.png "Edit Tenant Security")

9.  Nella sezione **Redirection URLs** seguire questa procedura:

    ![Redirection URLs](./media/active-directory-saas-workday-tutorial/IC7829581.png "Redirection URLs")

	a. Fare clic su **Aggiungi riga**.

	b. Nelle caselle di testo **URL di reindirizzamento dell'accesso** e **URL di reindirizzamento dispositivi mobili** digitare il valore **URL del Tenant di Workday** immesso nella pagina **Configura URL app** del portale di Azure classico.
    
	c. Nella pagina della finestra di dialogo **Configura accesso Single Sign-On** del portale di Azure classico copiare il valore di **URL servizio Single Sign-On** e incollarlo nella casella di testo **URL di reindirizzamento disconnessione**.

	d. Nella casella di testo **Environment** digitare il nome dell'ambiente.


	>[AZURE.NOTE] Il valore dell'attributo Environment è collegato al valore dell'URL del tenant:
	>
    >-   Se il nome di dominio dell'URL tenant di Workday inizia con impl (ad esempio, *https://impl.workday.com/\tenant>/login-saml2.htmld*), l'attributo **Environment** deve essere impostato su Implementation.
    >-   Se il nome di dominio inizia con altro, è necessario contattare Workday per ottenere il valore **Environment** corrispondente.

10. Nella sezione**SAML Setup** seguire questa procedura:

    ![SAML Setup](./media/active-directory-saas-workday-tutorial/IC782926.png "SAML Setup")

	a. Selezionare **Enable SAML authentication**.

	b. Fare clic su **Aggiungi riga**.

11. Nella sezione SAML Identity Providers seguire questa procedura:

    ![SAML Identity Providers](./media/active-directory-saas-workday-tutorial/IC7829271.png "SAML Identity Providers")

	a. Nella casella di testo Identity Provider Name digitare il nome del provider (ad esempio, *SPInitiatedSSO*).

    b. Nella finestra di dialogo **Configura accesso Single Sign-On in Workday** del portale di Azure classico copiare il valore di **ID provider di identità** e quindi incollarlo nella casella di testo **Autorità di certificazione**.

    c. Selezionare **Enable Workday Initialted Logout**.

    d. Nella pagina della finestra di dialogo **Configura accesso Single Sign-On in Workday** del portale di Azure classico copiare il valore di **URL servizio Single Sign-On** e incollarlo nella casella di testo **Autorità di certificazione**.


    e. Fare clic su **Certificato di chiave pubblica del provider di identità** e quindi su **Crea**.

	![Create](./media/active-directory-saas-workday-tutorial/IC782928.png "Create")

    f. Fare clic su **Crea chiave pubblica x509**.
        
	![Create](./media/active-directory-saas-workday-tutorial/IC782929.png "Create")


1. Nella sezione **Visualizza chiave pubblica x509** eseguire la procedura seguente:

	![View x509 Public Key](./media/active-directory-saas-workday-tutorial/IC782930.png "View x509 Public Key")

	a. Nella casella di testo **Name** digitare un nome per il certificato (ad esempio, *PPE\_SP*).
    	
	b. Nella casella di testo **Valid From** digitare il valore dell'attributo di inizio validità del certificato.
    
	c. Nella casella di testo **Valid To** digitare il valore dell'attributo di fine validità del certificato.
		
    >[AZURE.NOTE] Per individuare la data di inizio e di fine validità, fare doppio clic sul certificato scaricato. Le date sono elencate nella scheda **Details**.

	d. Creare un file **con codifica Base 64** dal certificato scaricato.

	>[AZURE.TIP] Per informazioni dettagliate, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

	e. Aprire il certificato con codifica Base 64 nel Blocco note e quindi copiarne il contenuto.
    
	f. Nella casella di testo **Certificate** incollare il valore copiato negli Appunti.
    
	g. Fare clic su **OK**.

12.  Eseguire la procedura seguente:

	![SSO configuration](./media/active-directory-saas-workday-tutorial/IC7829351111.png "SSO configuration")

	a. Abilitare **x509 Private Key Pair**.

	b. Nella casella di testo **Service Provider ID** digitare **http://www.workday.com**.

	c. Selezionare **Abilita autenticazione SAML SP initiated**.

	d. Nella finestra di dialogo **Configura accesso Single Sign-On in Workday** del portale di Azure classico copiare il valore di **URL servizio Single Sign-On** e quindi incollarlo nella casella di testo **URL servizio IdP SSO**.
     
	e. Selezionare **Do Not Deflate SP-initiated Authentication Request**.

    f. In **Authentication Request Signature Method** selezionare **SHA256**.
        
	![Authentication Request Signature Method](./media/active-directory-saas-workday-tutorial/IC782932.png "Authentication Request Signature Method")
 
	g. Fare clic su **OK**.
        
	![OK](./media/active-directory-saas-workday-tutorial/IC782933.png "OK")

12. Nella pagina **Configura accesso Single Sign-On in Workday** del portale di Azure classico e fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-workday-tutorial/IC782934.png "Configura accesso Single Sign-On")

13. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-workday-tutorial/IC782935111.png "Configura accesso Single Sign-On")



##Configurazione del provisioning utente
  
Per eseguire il provisioning di un utente test in Workday, è necessario contattare il team di supporto di Workday. Il team di supporto provvederà a creare l'utente.

##Assegnazione degli utenti
  
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a Workday, seguire questa procedura:

1.  Nel portale di Azure classico creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **Workday** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-workday-tutorial/IC782935.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-workday-tutorial/IC767830.png "Sì")
  
Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0914_2016-->