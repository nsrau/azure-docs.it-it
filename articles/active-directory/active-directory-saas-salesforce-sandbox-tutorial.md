<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Salesforce Sandbox | Microsoft Azure"
    description="Informazioni su come usare Salesforce Sandbox con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="stevenpo"/>

<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="12/18/2015" 
    ms.author="jeedes" />


#Esercitazione: Integrazione di Azure Active Directory con Salesforce Sandbox
>[AZURE.TIP]Per inviare commenti e suggerimenti, fare clic [qui](http://go.microsoft.com/fwlink/?LinkId=521878).
  
In questa esercitazione viene illustrata l'integrazione di Azure e Salesforce Sandbox. Sandbox offre la possibilità di creare più copie dell'organizzazione in ambienti distinti per diversi scopi, ad esempio sviluppo, test e formazione, senza compromettere i dati e le applicazioni dell’organizzazione di produzione Salesforce. Per ulteriori informazioni, vedere [Panoramica di Sandbox](https://help.salesforce.com/HTViewHelpDoc?id=create_test_instance.htm&language=en_US)
  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Sandbox in Salesforce.com
  
Se non si dispone di un sandbox valido in Salesforce.com, è necessario contattare Salesforce.
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per Salesforce Sandbox
2.  Configurazione dell'accesso Single Sign-On
3.  Abilitazione del dominio
4.  Configurazione del provisioning utente
5.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-salesforce-sandbox-tutorial/IC769571.png "Scenario")
##Abilitazione dell'integrazione dell'applicazione per Salesforce Sandbox
  
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per Salesforce Sandbox.

###Per abilitare l'integrazione dell'applicazione per Salesforce Sandbox, eseguire la procedura seguente:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel pannello di navigazione sinistro.

    ![Active Directory](./media/active-directory-saas-salesforce-sandbox-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-salesforce-sandbox-tutorial/IC700994.png "Applicazioni")

4.  Per aprire la **Raccolta di applicazioni**, fare clic su **Aggiungi app**, quindi fare clic su **Aggiungi un'applicazione che verrà utilizzata dall'organizzazione**.

    ![Come procedere](./media/active-directory-saas-salesforce-sandbox-tutorial/IC700995.png "Come procedere")

5.  Nella **casella di ricerca** digitare **Salesforce Sandbox**.

    ![Raccolta di applicazioni](./media/active-directory-saas-salesforce-sandbox-tutorial/IC710978.png "Raccolta di applicazioni")

6.  Nel riquadro dei risultati selezionare **Salesforce Sandbox**, quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Sandbox Salesforce](./media/active-directory-saas-salesforce-sandbox-tutorial/IC746474.png "Sandbox Salesforce")
##Configurazione dell'accesso Single Sign-On
  
In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a Salesforce Sandbox tramite il proprio account di Azure AD usando la federazione basata sul protocollo SAML.

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Nella pagina di integrazione dell'applicazione **Salesforce Sandbox** del portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-salesforce-sandbox-tutorial/IC749323.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a Salesforce Sandbox** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.

    ![Sandbox Salesforce](./media/active-directory-saas-salesforce-sandbox-tutorial/IC746479.png "Sandbox Salesforce")

3.  Nella casella di testo **URL di accesso** della pagina **Configura URL app** digitare l'URL usando il modello seguente `http://company.my.salesforce.com` e quindi fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781022.png "Configura URL app")

4. Se l'accesso Single Sign-On è già stato configurato per un'altra istanza di Salesforce Sandbox nella directory, sarà necessario configurare anche l’**Identificatore** in modo che abbia lo stesso valore di **URL di accesso**. Per trovare il campo **Identificatore**, selezionare la casella di controllo **Impostazioni avanzate** nella pagina **Configura URL app** della finestra di dialogo.

4.  Nella pagina **Configura accesso Single Sign-On in Salesforce Sandbox** fare clic su **Scarica certificato** e quindi salvare il file di certificato nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781023.png "Configura accesso Single Sign-On")

5.  In un'altra finestra del Web browser accedere al sito aziendale di Salesforce Sandbox come amministratore.

6.  Nel menu in alto fare clic su **Impostazione**.

    ![Impostazione](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781024.png "Impostazione")

7.  Nel riquadro di spostamento a sinistra, fare clic su **Controlli di sicurezza** e quindi fare clic su **Impostazioni Single Sign-On**.

    ![Impostazioni Single Sign-On](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781025.png "Impostazioni Single Sign-On")

8.  Nella sezione Impostazioni Single Sign-On, eseguire la procedura seguente:

    ![Impostazioni Single Sign-On](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781026.png "Impostazioni Single Sign-On")

    1.  Selezionare **Abilitato SAML**.
    2.  Fare clic su **New**.

9.  Nella sezione Impostazioni SAML Single Sign-On, eseguire la procedura seguente:

    ![Impostazioni SAML Single Sign-On](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781027.png "Impostazioni SAML Single Sign-On")

    1.  Nella casella di testo Nome digitare il nome della configurazione (ad esempio: *SPSSOWAAD\_Test*).
    2.  Nella finestra di dialogo **Configura accesso Single Sign-On in Salesforce Sandbox** del portale di Azure copiare il valore di **URL autorità di certificazione** e incollarlo nella casella di testo **Autorità di certificazione**.
    3.  Nella casella di testo **ID entità** digitare ****https://test.salesforce.com** se è la prima istanza di Salesforce Sandbox aggiunta alla directory. Se esiste già un'istanza di Salesforce Sandbox, in **ID entità** digitare l'**URL di accesso**, che deve essere nel formato: `http://company.my.salesforce.com`
4.  Per caricare il certificato scaricato, fare clic su **Sfoglia**.
    5.  In **Tipo di identità SAML** selezionare **L'asserzione contiene l'ID federazione dell'oggetto utente**.
    6.  In **Percorso identità SAML** selezionare **L’identità è nell’elemento NameIdentifier dell’istruzione Subject**.
    7.  Nella finestra di dialogo **Configura accesso Single Sign-On in Salesforce Sandbox** del portale di Azure copiare il valore di **URL accesso remoto** e incollarlo nella casella di testo **URL accesso provider di identità**.
    8.  Nella finestra di dialogo **Configura accesso Single Sign-On in Salesforce Sandbox** del portale di Azure copiare il valore di **URL disconnessione remota** e incollarlo nella casella di testo **URL disconnessione provider di identità**.
    9.  In **Binding richiesta avviato da provider di servizi** selezionare **HTTP POST**.
    10. Fare clic su **Salva**.

10. Nel portale di Azure AD selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781028.png "Configura accesso Single Sign-On")

##Abilitazione del dominio
  
In questa sezione si presuppone che sia già stato creato un dominio. Per informazioni dettagliate, vedere [Definizione del nome di dominio](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US).

###Per abilitare il dominio, eseguire la procedura seguente:

1.  Nel riquadro di spostamento a sinistra fare clic su **Gestione dominio** e quindi fare clic su **Dominio personale**.

    ![Dominio personale](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781029.png "Dominio personale")

    >[AZURE.NOTE]Assicurarsi che il dominio sia stato configurato correttamente.

2.  Nella sezione **Impostazioni pagina di accesso** fare clic su **Modifica**, quindi per **Servizio di autenticazione** selezionare il nome dell'impostazione del Single Sign-On SAML dalla sezione precedente e fare clic su **Salva**.

    ![Dominio personale](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781030.png "Dominio personale")
  
Non appena si dispone di un dominio configurato, gli utenti devono utilizzare l'URL del dominio per l'accesso a Salesforce Sandbox. Per ottenere il valore dell'URL, fare clic sul profilo SSO creato nella sezione precedente.
##Configurazione del provisioning utente
  
In questa sezione viene descritto come abilitare il provisioning utente degli account utente di Active Directory in Salesforce Sandbox.

###Per configurare il provisioning utente, eseguire la procedura seguente:

1.  Nella barra di spostamento in alto del portale di Salesforce, selezionare il nome per espandere il menu utente:

    ![Impostazioni personali](./media/active-directory-saas-salesforce-sandbox-tutorial/IC698773.png "Impostazioni personali")

2.  Nel menu utente selezionare **Impostazioni personali** per aprire la pagina **Impostazioni personali**.

3.  Nel riquadro a sinistra fare clic su **Personale** per espandere la sezione corrispondente e fare clic su **Reimposta token di sicurezza**.

    ![Impostazioni personali](./media/active-directory-saas-salesforce-sandbox-tutorial/IC698774.png "Impostazioni personali")

4.  Nella pagina **Reimposta token di sicurezza** fare clic su **Reimposta token di sicurezza** per richiedere un messaggio di posta elettronica con il token di sicurezza di Salesforce.com.

    ![Nuovo token](./media/active-directory-saas-salesforce-sandbox-tutorial/IC698776.png "Nuovo token")

5.  Verificare nella cartella Posta in arrivo la presenza di un messaggio da Salesforce.com con l’oggetto “**conferma di sicurezza di salesforce.com.com**”.

6.  Aprire il messaggio di posta elettronica e copiare il valore del token di sicurezza.

7.  Nella pagina di integrazione dell'applicazione **Salesforce Sandbox** del portale di gestione di Azure fare clic su **Configura provisioning utente** per aprire la finestra di dialogo **Configura provisioning utente**.

    ![Configurare il provisioning utente.](./media/active-directory-saas-salesforce-sandbox-tutorial/IC769573.png "Configurare il provisioning utente.")

8.  Nella pagina **Immettere le credenziali di Salesforce Sandbox per abilitare il provisioning automatico degli utenti** specificare le impostazioni di configurazione seguenti:

    ![Sandbox Salesforce](./media/active-directory-saas-salesforce-sandbox-tutorial/IC746476.png "Sandbox Salesforce")

    1.  Nella casella di testo **Nome utente amministratore Salesforce Sandbox** digitare un nome account di Salesforce Sandbox con il profilo **Amministratore di sistema** assegnato in Salesforce.com.

    2.  Nella casella di testo **Password amministratore Salesforce Sandbox** digitare la password per questo account.

    3.  Nella casella di testo **Token di sicurezza utente** incollare il valore del token di sicurezza.

    4.  Fare clic su **Convalida** per verificare la configurazione.

    5.  Fare clic sul pulsante **Avanti** per aprire la pagina **Conferma**.

9.  Nella pagina **Conferma** fare clic su **Completa** per salvare la configurazione.
##Assegnazione degli utenti
  
Per testare la configurazione, è necessario concedere l'accesso all’applicazione agli utenti di Azure AD a cui si desidera consentirne l’uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a Salesforce Sandbox, eseguire la procedura seguente:

1.  Nel portale di Azure AD creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **Salesforce Sandbox** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-salesforce-sandbox-tutorial/IC769574.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-salesforce-sandbox-tutorial/IC767830.png "Sì")
  
È ora necessario attendere 10 minuti e verificare che l'account sia stato sincronizzato con Salesforce Sandbox.
  
Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=AcomDC_0218_2016-->