<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con TimeOffManager | Microsoft Azure" 
    description="Informazioni su come usare TimeOffManager con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." 
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
    ms.date="07/19/2016" 
    ms.author="jeedes" />

#Esercitazione: Integrazione di Azure Active Directory con TimeOffManager
  
In questa esercitazione verrà illustrata l'integrazione di Azure e TimeOffManager.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Sottoscrizione di TimeOffManager abilitata per l'accesso Single Sign-On
  
Al termine dell'esercitazione, gli utenti di Azure AD assegnati a TimeOffManager potranno accedere all'applicazione tramite il sito aziendale di TimeOffManager (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per TimeOffManager
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-timeoffmanager-tutorial/IC795909.png "Scenario")

##Abilitazione dell'integrazione dell'applicazione per TimeOffManager
  
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per TimeOffManager.

###Per abilitare l'integrazione dell'applicazione per TimeOffManager, eseguire la procedura seguente:

1.  Nel portale di Azure classico fare clic su **Active Directory** nel riquadro di spostamento sinistro.

    ![Active Directory](./media/active-directory-saas-timeoffmanager-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-timeoffmanager-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-timeoffmanager-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-timeoffmanager-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **TimeOffManager**.

    ![Raccolta di applicazioni](./media/active-directory-saas-timeoffmanager-tutorial/IC795910.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **TimeOffManager**, quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![TimeOffManager](./media/active-directory-saas-timeoffmanager-tutorial/IC795911.png "TimeOffManager")

##Configurazione dell'accesso Single Sign-On
  
In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a TimeOffManager tramite il relativo account in Azure AD utilizzando la federazione basata sul protocollo SAML.  
Come parte di questa procedura, verrà richiesto di caricare un file di certificato codificato in base 64 sul proprio tenant TimeOffManager.  
Se non si ha familiarità con questa procedura, vedere il video che descrive [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Nella pagina di integrazione dell'applicazione **TimeOffManager** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-timeoffmanager-tutorial/IC795912.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a TimeOffManager** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-timeoffmanager-tutorial/IC795913.png "Configura accesso Single Sign-On")

3.  Nella casella di testo **URL di risposta TimeOffManager** della pagina **Configura URL app** digitare l'URL di TimeOffManager AssertionConsumerService, ad esempio: "*https://www.timeoffmanager.com/cpanel/sso/consume.aspx?company\_id=IC34216*"*, quindi fare clic su *Avanti*.

    ![Configura URL app](./media/active-directory-saas-timeoffmanager-tutorial/IC795914.png "Configura URL app")

    È possibile ottenere l'URL di risposta dalla pagina delle impostazioni Single Sign-On di TimeOffManager.

    ![Impostazioni Single Sign-On](./media/active-directory-saas-timeoffmanager-tutorial/IC795915.png "Impostazioni Single Sign-On")

4.  Nella pagina **Configura accesso Single Sign-On in TimeOffManager** fare clic su **Scarica certificato**, quindi salvare il file di certificato sul computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-timeoffmanager-tutorial/IC795916.png "Configura accesso Single Sign-On")

5.  In un'altra finestra del Web browser accedere al sito aziendale di TimeOffManager come amministratore.

6.  Andare a **Account > Account Options > Single Sign-On Settings**.

    ![Impostazioni Single Sign-On](./media/active-directory-saas-timeoffmanager-tutorial/IC795917.png "Impostazioni Single Sign-On")

7.  Nella sezione **Single Sign-On Settings**, eseguire la procedura seguente:

    ![Impostazioni Single Sign-On](./media/active-directory-saas-timeoffmanager-tutorial/IC795918.png "Impostazioni Single Sign-On")

    a. Creare un file **con codifica Base 64** dal certificato scaricato.

        >[AZURE.TIP] For more details, see [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o)

    b. Aprire il certificato con codifica Base 64 nel Blocco note, copiarne il contenuto negli Appunti e incollare l’intero certificato nella casella di testo **Certificate X.509**.
    
    c. Nella pagina della finestra di dialogo **Configura accesso Single Sign-On in TimeOffManager** del portale di Azure classico copiare il valore di **URL autorità di certificazione** e incollarlo nella casella di testo **Idp Issuer** (Autorità di certificazione IdP).
    
    d. Nella pagina della finestra di dialogo **Configura accesso Single Sign-On in TimeOffManager** del portale di Azure classico copiare il valore di **URL accesso remoto** e incollarlo nella casella di testo **IdP Endpoint URL** (URL endpoint IdP).
    
    e. Per **Enforce SAML**, selezionare **No**.
    

    f. Per **Auto-Create Users**, selezionare **Yes**.
    
    g. Nella pagina della finestra di dialogo **Configura accesso Single Sign-On in TimeOffManager** del portale di Azure classico copiare il valore di **URL disconnessione remota** e incollarlo nella casella di testo **Logout URL** (URL disconnessione).
    
    h. Fare clic su **Salva modifiche**.

8.  Nella pagina della finestra di dialogo **Configura accesso Single Sign-On in TimeOffManager** del portale di Azure classico selezionare la conferma della configurazione e quindi fare clic su **Completa**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-timeoffmanager-tutorial/IC795919.png "Configura accesso Single Sign-On")

9.  Nel menu in alto fare clic su **Attributi** per aprire la finestra di dialogo **Attributi token SAML**.

    ![Attributi](./media/active-directory-saas-timeoffmanager-tutorial/IC795920.png "Attributi")

10. Per aggiungere i mapping di attributi obbligatori, eseguire la procedura seguente:

    ![Attributi token SAML](./media/active-directory-saas-timeoffmanager-tutorial/123.png "Attributi token SAML")

    |Nome attributo|Valore attributo|
	|---|---|
    |Email|User.mail|
    |Firstname|User.givenname|
	|Lastname|User.surname|

    a. Per ogni riga di dati nella tabella precedente, fare clic su **aggiungi attributo utente**.

    b. Nella casella di testo **Nome attributo**, digitare il nome dell'attributo indicato per quella riga.

    c. Nella casella di testo **Valore attributo**, selezionare il valore dell'attributo indicato per la riga.

    d. Fare clic su **Complete**.

11. Fare clic su **Applica modifiche**.

##Configurazione del provisioning utente
  
Per consentire agli utenti di Azure AD di accedere a TimeOffManager, è necessario eseguirne il provisioning in TimeOffManager.  
TimeOffManager supporta solo il provisioning just in time dell'utente. Non è necessario eseguire alcuna operazione.  
Gli utenti vengono aggiunti automaticamente durante l'utilizzo di Single Sign-on primo accesso.

>[AZURE.NOTE] È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da TimeOffManager per eseguire il provisioning degli account utente di Azure AD.

##Assegnazione degli utenti
  
Per testare la configurazione, è necessario concedere l'accesso all’applicazione agli utenti di Azure AD a cui si desidera consentirne l’uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a TimeOffManager, eseguire la procedura seguente:

1.  Nel portale di Azure classico creare un account di test.

2.  Nella pagina di integrazione dell’applicazione **TimeOffManager**, fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-timeoffmanager-tutorial/IC795922.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-timeoffmanager-tutorial/IC767830.png "Sì")
  
Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0720_2016-->