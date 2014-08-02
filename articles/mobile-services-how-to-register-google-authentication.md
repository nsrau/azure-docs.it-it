<properties linkid="develop-mobile-how-to-guides-register-for-google-authentication" urlDisplayName="Register for Google Authentication" pageTitle="Register for Google authentication - Mobile Services" metaKeywords="Azure registering application, Azure authentication, Google application authenticate, authenticate mobile services" description="Learn how to register your apps to use Google to authenticate with Azure Mobile Services." metaCanonical="" services="" documentationCenter="" title="Register your apps for Google login with Mobile Services" authors="" solutions="" manager="" editor="" />

Registrazione delle app per l'autenticazione di Google con Servizi mobili
=========================================================================

In questo argomento viene illustrato come registrare le proprie app in modo da poter utilizzare Google per l'autenticazione con Servizi mobili di Azure.

**Nota**

Per completare la procedura descritta in questo argomento, è necessario disporre di un account Google con un indirizzo di posta elettronica verificato. Per creare un nuovo account Google, visitare il sito Web all'indirizzo [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302).

1.  Passare al sito Web [Google APIs](http://go.microsoft.com/fwlink/p/?LinkId=268303), eseguire l'accesso con le credenziali dell'account Google e quindi fare clic su **Create project...**.

	![][1]   

2.  Fare clic su **API Access** e quindi su **Create an OAuth 2.0 client ID...**.

	![][2]

3.  In **Branding Information** immettere le informazioni per **Product name** e quindi fare clic su **Next**.

	![][3]

4.  In **Client ID Settings** selezionare **Web application**, digitare l'URL del servizio mobile in **Your site or hostname**, fare clic su **more options**, sostituire l'URL generato in **Authorized Redirect URIs** con l'URL del servizio mobile aggiungendovi il percorso */login/google* e quindi fare clic su **Create client ID**.

	![][4]

    > [WACOM.NOTE]Per un servizio mobile back-end .NET pubblicato in Azure tramite Visual Studio, l'URL di reindirizzamento è l'URL del servizio mobile con l'aggiunta del percorso *signin-google*, ovvero il servizio mobile come servizio .NET, ad esempio `https://todolist.azure-mobile.net/signin-google`.

5.  In **Client ID for web applications** prendere nota dei valori di **Client ID** e **Client secret**.

	![][5]

    **Nota sulla sicurezza**

	Il segreto client è un'importante credenziale di sicurezza. Non condividere questo valore con altri né distribuirlo con l'app.

È ora possibile utilizzare un account di accesso Google per l'autenticazione nell'app fornendo i valori dell'ID e del segreto client a Servizi mobili.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-developers.png
[2]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client.png
[3]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client2.png
[4]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client3.png
[5]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-app-details.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/

[Azure Management Portal]: https://manage.windowsazure.com/
