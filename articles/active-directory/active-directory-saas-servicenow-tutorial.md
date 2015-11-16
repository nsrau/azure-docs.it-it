<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con ServiceNow | Microsoft Azure" 
    description="Informazioni su come usare ServiceNow con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." 
    services="active-directory" 
    authors="MarkusVi"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="11/02/2015" 
    ms.author="markvi" />

#Esercitazione: Integrazione di Azure Active Directory con ServiceNow
  
Questa esercitazione descrive l'integrazione di Azure e ServiceNow.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Tenant in ServiceNow
  
Al termine dell'esercitazione, gli utenti di Azure AD assegnati a ServiceNow potranno accedere all'applicazione tramite il sito aziendale di ServiceNow (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per ServiceNow
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-servicenow-tutorial/IC769496.png "Scenario")
##Abilitazione dell'integrazione dell'applicazione per ServiceNow
  
Questa sezione descrive come abilitare l'integrazione dell'applicazione per ServiceNow.

###Per abilitare l'integrazione dell'applicazione per ServiceNow, seguire questa procedura:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel pannello di navigazione sinistro.

    ![Active Directory](./media/active-directory-saas-servicenow-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-servicenow-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-servicenow-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-servicenow-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **ServiceNow**.

    ![Raccolta di applicazioni](./media/active-directory-saas-servicenow-tutorial/IC701016.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **ServiceNow** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![ServiceNow](./media/active-directory-saas-servicenow-tutorial/IC701017.png "ServiceNow")
##Configurazione dell'accesso Single Sign-On
  
Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a ServiceNow tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.

Come parte di questa procedura, verrà richiesto di caricare un file di certificato con codifica Base 64 nel tenant di Dropbox for Business. Se non si ha familiarità con questa procedura, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

###Per configurare l'accesso Single Sign-On, eseguire la procedura seguente:

1.  Nella pagina di integrazione dell'applicazione **ServiceNow** del portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/IC749323.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a ServiceNow** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/IC749324.png "Configura accesso Single Sign-On")

3.  Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:

    ![Configura URL app](./media/active-directory-saas-servicenow-tutorial/IC769497.png "Configura URL app")

    a. Nella casella di testo **URL di accesso per ServiceNow** digitare l'URL usato dagli utenti per accedere all'applicazione ServiceNow, ad esempio *https://\<NomeIstanza>.service-now.com*.

    b. Nella casella di testo **URL autorità di certificazione** digitare l'URL usato dagli utenti per accedere all'applicazione ServiceNow, ad esempio *https://\<NomeIstanza>.service-now.com*.

    c. Fare clic su **Avanti**

4.  Nella pagina **Configura automaticamente l'accesso Single Sign-On** fare clic su **Configura manualmente l'applicazione per l'accesso Single Sign-On** e quindi fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-servicenow-tutorial/IC7694971.png "Configura URL app")



4.  Nella pagina **Configura accesso Single Sign-On in ServiceNow** fare clic su **Scarica certificato** per scaricare il file di certificato e salvarlo nel computer e quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/IC749325.png "Configura accesso Single Sign-On")

1. Accedere all'applicazione ServiceNow come amministratore.

2. Nel riquadro di spostamento a sinistra fare clic su **Properties**.

    ![Configura URL app](./media/active-directory-saas-servicenow-tutorial/IC7694980.png "Configura URL app")


3. Nella finestra di dialogo **Multiple Provider SSO Properties** seguire questa procedura:

    ![Configura URL app](./media/active-directory-saas-servicenow-tutorial/IC7694981.png "Configura URL app")

    a. Per **Enable multiple provider SSO** selezionare **Yes**.

    b. Per **Enable debug logging got the multiple provider SSO integration** selezionare **Yes**.

    c. Nella casella di testo **The field on the user table that...** digitare **user\_name**.

    d. Fare clic su **Save**.



1. Nel riquadro di spostamento a sinistra fare clic su **x509 Certificates**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/IC7694973.png "Configura accesso Single Sign-On")


1. Nella finestra di dialogo **X.509 Certificates** fare clic su **New**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/IC7694974.png "Configura accesso Single Sign-On")


1. Nella finestra di dialogo **X.509 Certificates** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/IC7694975.png "Configura accesso Single Sign-On")

    a. Fare clic su **New**.

    b. Nella casella di testo **Name** digitare un nome per la configurazione, ad esempio **TestSAML2.0**.

    c. Selezionare **Active**.

    d. Per **Format** selezionare **PEM**.

    e. Per **Type** selezionare **Trust Store Cert**.

    f. Creare un file con codifica Base 64 dal certificato scaricato.
    > [AZURE.NOTE]Per altre informazioni, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).
    
    g. Aprire il certificato con codifica Base 64 nel Blocco note, copiarne il contenuto negli Appunti e quindi incollarlo nella casella di testo **PEM Certificate**.

    h. Fare clic su **Update**.


1. Nel riquadro di spostamento a sinistra fare clic su **Identity Providers**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/IC7694976.png "Configura accesso Single Sign-On")

1. Nella finestra di dialogo **Identity Providers**. fare clic su **New**:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/IC7694977.png "Configura accesso Single Sign-On")


1. Nella finestra di dialogo **Identity Providers**. fare clic su **SAML2 Update1?**:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/IC7694978.png "Configura accesso Single Sign-On")


1. Nella finestra di dialogo SAML2 Update1 Properties seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/IC7694982.png "Configura accesso Single Sign-On")


    a. Nella casella di testo **Name** digitare un nome per la configurazione, ad esempio **SAML 2.0**.

    b. Nella casella di testo **User Field** digitare **email**.

    c. Nel portale di Azure AD copiare il valore in **ID provider di identità** e incollarlo nella casella di testo **Identity Provider URL**.

    d. Nel portale di Azure AD copiare il valore in **URL richiesta di autenticazione** e incollarlo nella casella di testo **Identity Provider's AuthnRequest**.

    e. Nel portale di Azure AD copiare il valore in **URL servizio Single Sign-Out** e incollarlo nella casella di testo **Identity Provider's SingleLogoutRequest**.

    f. Nella casella di testo **ServiceNow Homepage** digitare l'URL della home page dell'istanza di ServiceNow.

    > [AZURE.NOTE]L'URL della home page dell'istanza di ServiceNow è una concatenazione dell'**URL del tenant di ServiceNow** e **/navpage.do**, ad esempio **https://fabrikam.service-now.com/navpage.do*).
 

    g. Nella casella di testo **Entity ID / Issuer** digitare l'URL del tenant ServiceNow.

    h. Nella casella di testo **Audience URL** digitare l'URL del tenant ServiceNow.

    i. Nella casella di testo **Protocol Binding for the IDP's SingleLogoutRequest** digitare **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**.

    j. Nella casella di testo NameID Policy digitare **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**.

    k. Fare clic su **Create an AuthnContextClass**.

    l. In **AuthnContextClassRef Method** digitare ****http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password**.

    m. Nella casella di testo **Clock Skew** digitare **60**.

    n. Per **Single Sign On Script** selezionare **MultiSSO\_SAML2\_Update1**.

    o. Per **x509 Certificate** selezionare il certificato creato nel passaggio precedente.

    p. Fare clic su **Submit**.



6. Nel portale di Azure AD selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/IC7694990.png "Configura accesso Single Sign-On")

7. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/IC7694991.png "Configura accesso Single Sign-On")



##Configurazione del provisioning utente
  
Questa sezione descrive come abilitare il provisioning degli account utente di Azure Active Directory in ServiceNow.


### Per configurare il provisioning utente, eseguire la procedura seguente:

1. Nella pagina di integrazione dell'applicazione **ServiceNow** del portale di gestione di Azure fare clic su **Configura provisioning utenti**. <br><br> ![Provisioning utenti](./media/active-directory-saas-servicenow-tutorial/IC769498.png "Provisioning utenti")


2. Nella pagina **Immettere le credenziali ServiceNow per abilitare il provisioning utente automatico** specificare le credenziali di configurazione seguenti: Configura provisioning utenti

     2\.1. Nella casella di testo **Nome istanza ServiceNow** digitare il nome dell'istanza di ServiceNow.

     2\.2. Nella casella di testo **Nome utente amministratore ServiceNow** digitare il nome dell'account amministratore ServiceNow.

     2\.3. Nella casella di testo **Password amministratore ServiceNow** digitare la password dell'account.

     2\.4. Fare clic su **Convalida** per verificare la configurazione.

     2\.5. Fare clic sul pulsante **Avanti** per aprire la pagina **Passaggi successivi**.

     2\.6. Se si vuole effettuare il provisioning di tutti gli utenti nell'applicazione, selezionare "**Esegui automaticamente il provisioning di tutti gli account utente della directory in questa applicazione**". <br><br> ![Passaggi successivi](./media/active-directory-saas-servicenow-tutorial/IC698804.png "Passaggi successivi")

     2\.7. Nella pagina **Passaggi successivi** fare clic su **Completa** per salvare la configurazione.











##Assegnazione degli utenti
  
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si desidera consentirne l'uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a ServiceNow, seguire questa procedura:

1.  Nel portale di Azure AD creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **ServiceNow** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-servicenow-tutorial/IC769499.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-servicenow-tutorial/IC767830.png "Sì")
  
Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).


## Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!---HONumber=Nov15_HO2-->