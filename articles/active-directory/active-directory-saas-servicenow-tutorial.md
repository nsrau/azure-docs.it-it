<properties pageTitle="Esercitazione: Integrazione di Azure Active Directory con ServiceNow | Microsoft Azure" description="Informazioni su come usare ServiceNow con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Esercitazione: Integrazione di Azure Active Directory con ServiceNow
>[AZURE.TIP]Per inviare commenti e suggerimenti, fare clic [qui](http://go.microsoft.com/fwlink/?LinkId=521880).
  
Questa esercitazione descrive l'integrazione di Azure e ServiceNow. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

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

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel riquadro di spostamento sinistro.

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

3.  Nella casella di testo **URL accesso ServiceNow** della pagina **Configura URL app** digitare l'URL usando il modello seguente "*https://<InstanceName>.servicenow.com*" e quindi fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-servicenow-tutorial/IC769497.png "Configura URL app")

4.  Nella pagina **Configura accesso Single Sign-On in ServiceNow** fare clic su **Download certificato** per scaricare il file di certificato e salvarlo nel computer e quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/IC749325.png "Configura accesso Single Sign-On")

5.  Nella barra di spostamento sul lato sinistro nel tenant di ServiceNow fare clic su **Properties** per aprire la pagina **SAML 2.0 Single Sign on properties**.


6. Nella pagina **SAML 2.0 Single Sign-on properties** seguire questa procedura:

     6\.1 Per **Enable external authentication** selezionare **Yes**.


     6\.2. Nella casella di testo **The Identity Provider URL which will issue the SAML2 security token with user info** digitare **https://sts.windows.net/<GUID del tenant>/**.


     6\.3. Nella casella di testo **The base URL to the Identity Provider's AuthnRequest service** digitare **https://login.windows.net/<GUID del tenant>/saml2**.


     6\.4. Nella casella di testo **The base URL to the Identity Provider's SingleLogoutRequest service** digitare **https://login.windows.net/<GUID del tenant>/saml2**.


     6\.5. Nella casella di testo **The protocol binding for the Identity Provider's SingleLogoutRequest service** digitare **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**.

     6\.6. Per **Sign LogoutRequest** selezionare **Yes**.

     6\.7. Nella casella di testo **When SAML 2.0 single sign-on fails because the session is not authenticated, or this is the first login, redirect to this URL** digitare **https://login.windows.net/<GUID del tenant>/saml2**.

  

7. Nella sezione **Service Provider (Service-Now) properties** seguire questa procedura:

     7\.1. Nella casella di testo **The URL to Service-now instance homepage** digitare l'URL della home page dell'istanza di ServiceNow. L'URL della home page dell'istanza di ServiceNow è una concatenazione dell'**URL tenant di ServiceNow** e **/navpage.do**: **https://<InstanceName>.service-now.com/navpage.do** <br><br> ![Home page dell'istanza di ServiceNow](./media/active-directory-saas-servicenow-tutorial/IC700342.png "Home page dell'istanza di ServiceNow")


     7\.2. Nella casella di testo **The entity identification, or the issuer** digitare l'URL del tenant.

     7\.3. Nella casella di testo **The audience uri that accepts SAML2 token** digitare l'URL del tenant.

     7\.4. Nella casella di testo **The User table field to match with the Subject's NameID element in the SAMLResponse** digitare **email**.

     7\.5. Nella casella di testo **The NameID policy to use for returning the Subject's NameID in the SAMLResponse** digitare **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**.

     7\.6 Lasciare **Create an AuthnContextClass request in the AuthnRequest statement** deselezionata.

     7\.7 Nella casella di testo **The AuthnContextClassRef method that will be included in our SAML 2.0 AuthnRequest to the Identity Provider** digitare ****http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password**.



8. Nella sezione **Advanced settings** seguire questa procedura:

     8\.1 Nella casella di testo **The number in seconds before "notBefore" constraint, or after "notOnOrAfter" constraint to consider still valid** digitare **60**.


9. Per salvare la configurazione, fare clic su **Save**.

10. Nella barra di spostamento a sinistra fare clic su **Certificate** per aprire la pagina **Certificate**.



11. Per caricare il certificato, nella pagina del certificato seguire questa procedura:

     11\.1. Fare clic su **New**.

     11\.2. Nella casella di testo **Name** digitare **SAML 2.0**.

     11\.3. Selezionare **Active**.

     11\.4. Per **Format** selezionare **PEM**.

     11\.5. Creare un file con codifica Base 64 dal certificato scaricato.  > [AZURE.NOTE] Per informazioni dettagliate, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

     11\.6. Nel **Blocco note** aprire il file con codifica Base 64 file e quindi copiarne il contenuto negli Appunti.

     11\.7. Incollare il contenuto degli Appunti nella casella di testo **PEM Certificate**.

     11\.8. Fare clic su **Submit**.



12. Nel portale di Azure AD selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su Completa per chiudere la finestra di dialogo Configura accesso Single Sign-On. <br><br> ![Configura accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/IC749326.png "Configura accesso Single Sign-On")




##Configurazione del provisioning utente
  
Questa sezione descrive come abilitare il provisioning degli account utente di Azure Active Directory in ServiceNow.


### Per configurare il provisioning utente, eseguire la procedura seguente:

1. Nella pagina di integrazione dell'applicazione **ServiceNow** del portale di gestione di Azure fare clic su **Configura provisioning utenti**. <br><br> ![Provisioning utenti](./media/active-directory-saas-servicenow-tutorial/IC769498.png "Provisioning utenti")


2. Nella pagina **Immettere le credenziali di ServiceNow per abilitare il provisioning automatico degli utenti** specificare le impostazioni di configurazione seguenti: Configura provisioning utenti

     2\.1. Nella casella di testo **Nome istanza ServiceNow** digitare il nome dell'istanza di ServiceNow.

     2\.2. Nella casella di testo **Nome utente amministratore ServiceNow** digitare il nome dell'account amministratore di ServiceNow.

     2\.3. Nella casella di testo **Password amministratore ServiceNow** digitare la password per questo account.

     2\.4. Fare clic su **Convalida** per verificare la configurazione.

     2\.5. Fare clic sul pulsante **Avanti** per aprire la pagina **Passaggi successivi**.

     2\.6. Se si vuole effettuare il provisioning di tutti gli utenti in questa applicazione, selezionare "**Esegui automaticamente il provisioning di tutti gli account utente della directory in questa applicazione**". <br><br> ![Passaggi successivi](./media/active-directory-saas-servicenow-tutorial/IC698804.png "Passaggi successivi")

     2\.7. Nella pagina **Passaggi successivi** fare clic su **Completa** per salvare la configurazione.











##Assegnazione degli utenti
  
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si desidera consentirne l'uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a ServiceNow, seguire questa procedura:

1.  Nel portale di Azure AD creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **ServiceNow** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-servicenow-tutorial/IC769499.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-servicenow-tutorial/IC767830.png "Sì")
  
Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).


## Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!----HONumber=August15_HO8-->