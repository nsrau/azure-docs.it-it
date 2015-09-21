<properties pageTitle="Esercitazione: Integrazione di Azure Active Directory con Workday | Microsoft Azure" description="Informazioni su come usare Workday con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Esercitazione: Integrazione di Azure Active Directory con Workday
>[AZURE.TIP]Per inviare commenti e suggerimenti, fare clic [qui](http://go.microsoft.com/fwlink/?LinkId=330042).
  
Questa esercitazione descrive l'integrazione di Azure e Workday. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Tenant di Workday
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione Workday
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Configurazione del provisioning utente

![Scenario](./media/active-directory-saas-workday-tutorial/IC782919.png "Scenario")

##Abilitazione dell'integrazione dell'applicazione Workday
  
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Salesforce.

###Per abilitare l'integrazione dell'applicazione per Workday, seguire questa procedura:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel pannello di navigazione sinistro.

    ![Active Directory](./media/active-directory-saas-workday-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-workday-tutorial/IC700994.png "Applicazioni")

4.  Per aprire la **Raccolta di applicazioni**, fare clic su **Aggiungi app** e quindi su **Aggiungi un'applicazione che verrà utilizzata dall'organizzazione**.

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

    1.  Nella casella di testo **URL di accesso** digitare l'URL usato dagli utenti per accedere a Workday (ad esempio, *https://impl.workday.com/\<tenant>/login-saml2.htmld*).
    2.  Nella casella di testo **URL di risposta Workday** digitare l'URL di risposta di Workday (ad esempio, *https://impl.workday.com/\<tenant>/login-saml.htmld*).

        >[AZURE.NOTE]L'URL di risposta deve disporre di un sottodominio (ad esempio: www, wd2, wd3, wd3-impl, wd5, wd5-impl). Un URL simile a "**http://www.myworkday.com*" funziona; un URL simile a "**http://myworkday.com*" non funziona.
 
4.  Nella pagina **Configura accesso Single Sign-On in Workday** per scaricare il file del certificato, fare clic su **Download certificato** e quindi salvarlo nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-workday-tutorial/IC782922.png "Configura accesso Single Sign-On")

5.  In un'altra finestra del Web browser accedere al sito aziendale di Workday come amministratore.

6.  Passare a **Menu > Workbench**.

    ![Workbench](./media/active-directory-saas-workday-tutorial/IC782923.png "Workbench")

7.  Passare a **Amministrazione account**.

    ![Account Administration](./media/active-directory-saas-workday-tutorial/IC782924.png "Account Administration")

8.  Passare a **Modifica configurazione Tenant - sicurezza**.

    ![Edit Tenant Security](./media/active-directory-saas-workday-tutorial/IC782925.png "Edit Tenant Security")

9.  Nella sezione **URL di reindirizzamento** seguire questa procedura:

    ![Redirection URLs](./media/active-directory-saas-workday-tutorial/IC7829581.png "Redirection URLs")

     9\.1. Fare clic su **Aggiungi riga**.

     9\.2. Nelle caselle di testo **URL di reindirizzamento dell’accesso** e **URL di reindirizzamento dispositivi mobili** digitare il valore **URL del Tenant di Workday** immesso nella pagina **Configura URL app** del portale di Azure.
    
     9\.3. Nella pagina della finestra di dialogo **Configura accesso Single Sign-On** del portale di Azure copiare il valore di **URL servizio Single Sign-On** e incollarlo nella casella di testo **URL di reindirizzamento disconnessione**.

     9\.4. Nella casella di testo **Ambiente** digitare il nome dell'ambiente.


       >[AZURE.NOTE]Il valore dell'attributo di ambiente è associato al valore dell'URL del tenant: >>: se il nome di dominio dell’URL del tenant Workday inizia con impl (ad esempio: *https://impl.workday.com/\<tenant>/login-saml2.htmld*), l’attributo **Ambiente** deve essere impostato su Implementazione. >- Se il nome di dominio inizia con un altro elemento, è necessario contattare Workday per ottenere il valore**Ambiente** corrispondente.

10. Nella sezione **configurazione SAML**, attenersi alla procedura seguente:

    ![SAML Setup](./media/active-directory-saas-workday-tutorial/IC782926.png "SAML Setup")

     10\.1. Selezionare **Abilita autenticazione SAML**.

     10\.2. Fare clic su **Aggiungi riga**.

11. Nella sezione SAML Identity Providers seguire questa procedura:

    ![SAML Identity Providers](./media/active-directory-saas-workday-tutorial/IC7829271.png "SAML Identity Providers")

     11\.1. Nella casella di testo Nome del provider d’identità digitare il nome del provider (ad esempio, *SPInitiatedSSO*).

     11\.2. Nella finestra di dialogo **Configura accesso Single Sign-On in Workday** del portale di Azure copiare il valore di **ID provider di identità** e quindi incollarlo nella casella di testo **Issuer**.

     11\.3. Selezionare **Abilita Initialted Logout di Workday**.

     11\.4. Nella pagina della finestra di dialogo **Configura accesso Single Sign-On in Workday** del portale di Azure copiare il valore di **URL servizio Single Sign-On** e incollarlo nella casella di testo **URL di reindirizzamento disconnessione**.


     11\.3. Fare clic su **Certificato di chiave pubblica del provider di identità** e quindi su **Crea**.<br><br>![Create](./media/active-directory-saas-workday-tutorial/IC782928.png "Create")

     11\.4. Fare clic su **Crea chiave pubblica x509**.<br><br>![Create](./media/active-directory-saas-workday-tutorial/IC782929.png "Create")

     11\.5. Nella sezione **Visualizza chiave pubblica x509** seguire questa procedura:<br><br>![View x509 Public Key](./media/active-directory-saas-workday-tutorial/IC782930.png "View x509 Public Key")<br>

      1.  Nella casella di testo **Nome** digitare un nome per il certificato (ad esempio, *PPE\_SP*).
      2.  Nella casella di testo **Valido da** digitare il valore dell'attributo di inizio validità del certificato.
      3.  Nella casella di testo **Valido fino** digitare il valore dell'attributo di fine validità del certificato.
		
           >[AZURE.NOTE]Per individuare la data di inizio e di fine validità, fare doppio clic sul certificato scaricato. Le date sono elencate nella scheda **Dettagli**.

      4.  Creare un file con **codifica Base 64** dal certificato scaricato.

		>[AZURE.TIP]Per informazioni dettagliate, vedere il video relativo alla [Conversione di un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

      5.  Aprire il certificato con codifica Base 64 nel blocco note e quindi copiarne il contenuto.
      6.  Nella casella di testo **Certificato** incollare il valore copiato negli Appunti.
      7.  Fare clic su **OK**.

12.  Eseguire la procedura seguente: <br><br>![SSO configuration](./media/active-directory-saas-workday-tutorial/IC7829351111.png "SSO configuration")

     12\.1. Abilitare la **Coppia di chiavi private x509**.

     12\.2. Nella casella di testo **ID provider servizio** digitare ****http://www.workday.com**.

     12\.3. Selezionare **Abilita autenticazione SAML SP initiated**.

     12\.4. Nella finestra di dialogo **Configura accesso Single Sign-On in Workday** del portale di Azure copiare il valore di **URL servizio Single Sign-On** e quindi incollarlo nella casella di testo **URL servizio IdP SSO**.
     
     12\.5 Selezionare **Do Not Deflate SP-initiated Authentication Request**.

     12\.6. In **Authentication Request Signature Method** selezionare **SHA256**. <br><br>![Authentication Request Signature Method](./media/active-directory-saas-workday-tutorial/IC782932.png "Authentication Request Signature Method")<br><br>
 
     12\.7 Fare clic su **OK**. <br><br>![OK](./media/active-directory-saas-workday-tutorial/IC782933.png "OK")

12. Nella pagina **Configura accesso Single Sign-On in Workday** del portale di Azure AD fare clic su **Avanti**. <br><br>

    ![Configura accesso Single Sign-On](./media/active-directory-saas-workday-tutorial/IC782934.png "Configura accesso Single Sign-On")

13. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**. <br><br>

    ![Configura accesso Single Sign-On](./media/active-directory-saas-workday-tutorial/IC782935111.png "Configura accesso Single Sign-On")



##Configurazione del provisioning utente
  
Per eseguire il provisioning di un utente test in Workday, è necessario contattare il team di supporto di Workday. Il team di supporto provvederà a creare l'utente.

##Assegnazione degli utenti
  
Per testare la configurazione, è necessario assegnare l'accesso all'applicazione agli utenti di Azure AD a cui si desidera consentire l'uso dell'applicazione.

###Per assegnare gli utenti a Workday, seguire questa procedura:

1.  Nel portale di Azure AD creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **Workday** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-workday-tutorial/IC782935.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**, quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-workday-tutorial/IC767830.png "Sì")
  
Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Sept15_HO2-->