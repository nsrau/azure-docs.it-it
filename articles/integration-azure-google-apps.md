<properties umbracoNaviHide="0" pageTitle="Azure AD Integration with Google Apps" metaKeywords="Azure Google Apps Integration" description="Learn about integrating Azure AD with Google Apps." linkid="documentation-services-identity-windows-azure-ad-integration-with-google=apps" urlDisplayName="Azure AD Integration with Google Apps" headerExpose="" footerExpose="" disqusComments="0" editor="lisatoft" manager="terrylan" title="Azure AD Integration with Google Apps" authors="" />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author></tags>

# Integrazione di Azure AD con Google Apps

In questa esercitazione verrà illustrata l'integrazione di Azure e Google Apps. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Tenant di prova in Google Apps

Se ancora non si dispone di un tenant valido in Google Apps, è ad esempio possibile creare un account di prova nel sito Web Google Apps for Business.

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

Abilitazione dell'integrazione dell'applicazione per Google Apps
Configurazione dell'accesso Single Sign-On
Abilitazione dell'accesso all'API per Google Apps
Aggiunta di domini personalizzati
Configurazione del provisioning utente

# Abilitazione dell'integrazione dell'applicazione per Google Apps

In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per Google Apps.

## Per abilitare l'integrazione dell'applicazione per Google Apps, eseguire la procedura seguente:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel pannello di navigazione sinistro.
2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
4.  Nella parte inferiore della schermata fare clic su **Add** per aprire la finestra di dialogo **Add Application**.
5.  Nella finestra di dialogo **Integrate an app with Azure AD** fare clic su **Manage access to an application**.
6.  Nella pagina **Select an application to manage** selezionare **Google Apps** nell'elenco di applicazioni.
7.  Fare clic sul pulsante **Complete** per aggiungere l'applicazione e chiudere la finestra di dialogo.

# Configurazione dell'accesso Single Sign-On

In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a Google Apps tramite il relativo account in Azure AD usando la federazione basata sul protocollo SAML.

## Per configurare l'accesso Single Sign-On federato, eseguire la procedura seguente:

1.  Nel portale di gestione di Azure selezionare **Active Directory** nel pannello di navigazione sinistro per aprire la finestra di dialogo Active Directory.
2.  Nell'elenco visualizzato selezionare la directory desiderata per aprire la relativa pagina di configurazione.
3.  Selezionare **APPLICATIONS** nel menu di primo livello.
4.  Nell'elenco di applicazioni selezionare **Google Apps** per aprire la finestra di dialogo di configurazione di Google Apps.
5.  Per aprire la finestra di dialogo **CONFIGURE SINGLE SIGN-ON**, fare clic su **Configure single sign-on**.

    ![Esercitazione\_Google\_01][Esercitazione\_Google\_01]

6.  Nella pagina della finestra di dialogo Select the single sign-on mode for this app selezionare Users authenticate with their account in MODE e quindi fare clic sul pulsante Next.

    ![Esercitazione\_Google\_02][Esercitazione\_Google\_02]

7.  Nella pagina della finestra di dialogo **Configure App URL** digitare l'URL del tenant di Google Apps nella casella di testo **GOOGLE APPS TENANT URL** e quindi fare clic sul pulsante **Next**.

    ![Esercitazione\_Google\_03][Esercitazione\_Google\_03]

8.  Nella pagina della finestra di dialogo **Configure single sign-on at Google Apps** procedere come descritto di seguito e quindi fare clic sul pulsante **Complete**.

    -   Fare clic su **Download certificate** e quindi salvare il certificato come **c:\\googleapps.cer**.
    -   Passare alla pagina di Google Apps ed eseguire l'accesso.

    ![Esercitazione\_Google\_04][Esercitazione\_Google\_04]

    -   Nella **console di amministrazione** fare clic su Security.

    ![Esercitazione\_Google\_05][Esercitazione\_Google\_05]

    Se l'icona Security non è visibile, fare clic su More controls nella parte inferiore della pagina.

9.  Nella pagina **Security** fare clic su **Advanced**.

    ![Esercitazione\_Google\_06][Esercitazione\_Google\_06]

10. Nella sezione **Advanced settings** della pagina selezionare **Set up single sign-on**.

    ![Esercitazione\_Google\_07][Esercitazione\_Google\_07]

11. Nella pagina Set up single sign-on eseguire la procedura seguente:

    ![Esercitazione\_Google\_08][Esercitazione\_Google\_08]

        + Select <strong>Enable Single Sign-on</strong>.
        + On the <strong>Configure single sign-on at Google Apps</strong> page in the Azure AD Portal, copy the <strong>SINGLE SIGN-ON URL</strong>, and then paste it into the related textbox on the <strong>Security page</strong> in the Google Apps <strong>Admin console</strong>.
        + On the <strong>Configure single sign-on at Google Apps</strong> page in the Azure AD Portal, copy the <strong>Single sign-out service URL</strong>, and then paste it into the related textbox on the <strong>Security</strong> page in the Google Apps <strong>Admin console</strong>.
        + On the <strong>Configure single sign-on at Google Apps</strong> page in the Azure AD Portal, copy the <strong>Change password URL</strong>, and then paste it into the related textbox on the <strong>Security</strong> page in the Google Apps <strong>Admin console</strong>.
        + Click the <strong>Browse</strong> button to locate the <strong>Verification certificate</strong>, and then click Upload.
        + Click <strong>Save</strong> changes.

12. Nella pagina **Configure single sign-on at Google Apps** del portale di Azure AD fare clic sul pulsante Complete.

È ora possibile passare al [pannello di accesso][pannello di accesso] e provare l'accesso Single Sign-On a Google Apps.

# Abilitazione dell'accesso all'API per Google Apps

Durante l'integrazione di Azure Active Directory con Google Apps per il provisioning utente, è necessario abilitare l'accesso all'API per il tenant in Google Apps.

## Per abilitare l'accesso all'API per Google Apps

1.  Accedere al tenant di Google Apps.
2.  Nella **console di amministrazione** fare clic su **Security**.

    ![Esercitazione\_Google\_05][Esercitazione\_Google\_05]

    </p>
    Se l'icona Security non è visibile, fare clic su More controls nella parte inferiore della console di amministrazione.

3.  Nella pagina Security fare clic su **API reference** per aprire la pagina della finestra di dialogo di configurazione corrispondente.
4.  Selezionare **Enable API access**.

    ![Esercitazione\_Google\_09][Esercitazione\_Google\_09]

    </p>

# Aggiunta di domini personalizzati

Per configurare il provisioning utente con Google Apps, è necessario che al dominio Azure AD e al dominio Google Apps sia assegnato lo stesso nome di dominio completo. Se tuttavia, ad esempio, si utilizzano tenant di prova per verificare lo scenario illustrato in questa esercitazione, i nomi di dominio completi dei tenant in genere non corrispondono. Per risolvere il problema, è possibile configurare domini personalizzati in Azure AD e in Google Apps.
Per configurare un dominio personalizzato, è necessario accedere al file della zona DNS del dominio pubblico.

![Esercitazione\_Google\_10][Esercitazione\_Google\_10]

## Per aggiungere un dominio personalizzato in Azure AD, eseguire la procedura seguente:

1.  Nel portale di gestione di Azure selezionare **Active Directory** nel pannello di navigazione sinistro per aprire la finestra di dialogo **Active Directory**.
2.  Nell'elenco visualizzato selezionare la directory desiderata per aprire la relativa pagina di configurazione.
3.  Selezionare **DOMAINS** nel menu di primo livello.
4.  Per aprire la casella di testo **ADD DOMAIN NAME** digitare il nome di dominio e quindi fare clic su **add**.
5.  Fare clic su **Next** per aprire la pagina della finestra di dialogo **Verify your domain name**.

    ![Esercitazione\_Google\_11][Esercitazione\_Google\_11]

6.  Selezionare un valore per **RECORD TYPE** e quindi registrare il record selezionato nel file della zona DNS.

    ![Esercitazione\_Google\_12][Esercitazione\_Google\_12]

7.  Utilizzando il **comando nslookup**, verificare se il record DNS è stato registrato correttamente.

    ![Esercitazione\_Google\_13][Esercitazione\_Google\_13]

## Per aggiungere un dominio personalizzato in Google Apps, eseguire la procedura seguente:

1.  Accedere al tenant di Google Apps.
2.  Nella **console di amministrazione** fare clic su **Domains**.

    ![Esercitazione\_Google\_14][Esercitazione\_Google\_14]

3.  Fare clic su **Add a custom domain**.

    ![Esercitazione\_Google\_15][Esercitazione\_Google\_15]

4.  Fare clic su **Use a domain you already own** e quindi su **Continue**.

    ![Esercitazione\_Google\_16][Esercitazione\_Google\_16]

5.  Digitare il nome del dominio personalizzato e quindi fare clic su **Continue**.

    ![Esercitazione\_Google\_17][Esercitazione\_Google\_17]

6.  Completare la procedura per verificare la proprietà del dominio.

    Se è già configurato l'accesso Single Sign-On federato, è necessario aggiornare l'URL del tenant di Google Apps nella configurazione dell'accesso Single Sign-On federato.

# Configurazione del provisioning utente

In questa sezione viene descritto come abilitare il provisioning utente degli account utente di Active Directory in Google Apps.

## Per configurare il provisioning utente, eseguire la procedura seguente:

1.  Nel portale di gestione di Azure selezionare **Active Directory** nel pannello di navigazione sinistro per aprire la finestra di dialogo **Active Directory**.
2.  Nell'elenco visualizzato selezionare la directory desiderata per aprire la relativa pagina di configurazione.
3.  Selezionare **APPLICATIONS** nel menu di primo livello.
    Nell'elenco di applicazioni selezionare **Google Apps** per aprire la finestra di dialogo di configurazione di **google apps**.
4.  Per aprire la finestra di dialogo **CONFIGURE ACCOUNT SYNC**, fare clic su **Configure account sync**.
5.  Nella pagina della finestra di dialogo **CONFIGURE ACCOUNT SYNC** specificare il nome di dominio di Google Apps, il nome utente e la password di Google Apps e quindi fare clic sul pulsante **Next**.

    ![Esercitazione\_Google\_18][Esercitazione\_Google\_18]

6.  Nella pagina della finestra di dialogo **Confirmation** fare clic sul pulsante **Complete** per chiudere la finestra di dialogo **CONFIGURE ACCOUNT SYNC**.

È ora possibile creare un account di prova. Attendere 10 minuti e quindi verificare che l'account sia stato sincronizzato con Google Apps.

Vedere anche le
[procedure consigliate per la gestione dei miglioramenti relativi all'accesso all'applicazioneAzure Active Directory.][procedure consigliate per la gestione dei miglioramenti relativi all'accesso all'applicazioneAzure Active Directory.]
[pannello di accesso][pannello di accesso]



  [Esercitazione\_Google\_01]: ./media/integration-azure-google-apps/Google_Tutorial_01.png
  [Esercitazione\_Google\_02]: ./media/integration-azure-google-apps/Google_Tutorial_02.png
  [Esercitazione\_Google\_03]: ./media/integration-azure-google-apps/Google_Tutorial_03.png
  [Esercitazione\_Google\_04]: ./media/integration-azure-google-apps/Google_Tutorial_04.png
  [Esercitazione\_Google\_05]: ./media/integration-azure-google-apps/Google_Tutorial_05.png
  [Esercitazione\_Google\_06]: ./media/integration-azure-google-apps/Google_Tutorial_06.png
  [Esercitazione\_Google\_07]: ./media/integration-azure-google-apps/Google_Tutorial_07.png
  [Esercitazione\_Google\_08]: ./media/integration-azure-google-apps/Google_Tutorial_08.png
  [pannello di accesso]: https://login.microsoftonline.com/login.srf?wa=wsignin1.0&rpsnv=2&ct=1384289458&rver=6.1.6206.0&wp=MCMBI&wreply=https:%2F%2Faccount.activedirectory.windowsazure.com%2Flanding.aspx%3Ftarget%3D%252fapplications%252fdefault.aspx&lc=1033&id=500633
  [Esercitazione\_Google\_09]: ./media/integration-azure-google-apps/Google_Tutorial_09.png
  [Esercitazione\_Google\_10]: ./media/integration-azure-google-apps/Google_Tutorial_10.png
  [Esercitazione\_Google\_11]: ./media/integration-azure-google-apps/Google_Tutorial_11.png
  [Esercitazione\_Google\_12]: ./media/integration-azure-google-apps/Google_Tutorial_12.png
  [Esercitazione\_Google\_13]: ./media/integration-azure-google-apps/Google_Tutorial_13.png
  [Esercitazione\_Google\_14]: ./media/integration-azure-google-apps/Google_Tutorial_14.png
  [Esercitazione\_Google\_15]: ./media/integration-azure-google-apps/Google_Tutorial_15.png
  [Esercitazione\_Google\_16]: ./media/integration-azure-google-apps/Google_Tutorial_16.png
  [Esercitazione\_Google\_17]: ./media/integration-azure-google-apps/Google_Tutorial_17.png
  [Esercitazione\_Google\_18]: ./media/integration-azure-google-apps/Google_Tutorial_18.png
  [procedure consigliate per la gestione dei miglioramenti relativi all'accesso all'applicazioneAzure Active Directory.]: http://social.technet.microsoft.com/wiki/contents/articles/18409.best-practices-for-managing-the-application-access-enhancements-for-windows-azure-active-directory.aspx
