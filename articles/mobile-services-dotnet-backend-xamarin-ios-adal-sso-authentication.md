<properties linkid="develop-mobile-tutorials-sso-with-adal-xamarin-ios" urlDisplayName="Active Directory SSO Authentication with ADAL" pageTitle="Authenticate your app with Active Directory Authentication Library Single Sign-On (Xamarin.iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to authentication users for single sign-on with ADAL in your Xamarin.iOS application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Authenticate your app with Active Directory Authentication Library Single Sign-On" authors="wesmc,mahender" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="wesmc,mahender"></tags>

# Autenticare l'app tramite il Single Sign-On di Active Directory Authentication Library

<div class="dev-center-tutorial-selector sublanding">
<a href="/it-it/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication" title="Windows Store C#" >Windows Store C#</a>
<a href="/it-it/documentation/articles/mobile-services-dotnet-backend-ios-adal-sso-authentication" title="iOS" >iOS</a>
<a href="/it-it/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-adal-sso-authentication" title="Xamarin.iOS" class="current">Xamarin.iOS</a>
</div>

Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite Active Directory Authentication Library.

Per poter autenticare gli utenti è necessario registrare l'app in Azure Active Directory. Questa operazione si esegue in due passaggi. Prima di tutto, è necessario registrare il servizio mobile ed esporre le registrazioni sul servizio. In secondo luogo, è necessario registrare l'app Xamarin.iOS e concederle accesso alle autorizzazioni.

> [WACOM.NOTE] In questa esercitazione viene descritto come usare Servizi mobili per eseguire l'autenticazione tramite Single Sign-On di Azure Active Directory per le app Xamarin.iOS. Se si tratta della prima esperienza con Servizi mobili, iniziare dall'esercitazione [Introduzione a Servizi mobili][Introduzione a Servizi mobili].

In questa esercitazione vengono descritte le operazioni di base seguenti:

1.  [Registrare il servizio mobile in Azure Active Directory][Registrare il servizio mobile in Azure Active Directory]
2.  [Registrare l'app in Azure Active Directory][Registrare l'app in Azure Active Directory]
3.  [Configurare il servizio mobile in modo che richieda l'autenticazione][Configurare il servizio mobile in modo che richieda l'autenticazione]
4.  [Aggiungere il codice di autenticazione all'app client][Aggiungere il codice di autenticazione all'app client]
5.  [Testare il client usando l'autenticazione][Testare il client usando l'autenticazione]

Per completare questa esercitazione, è necessario disporre di:

-   XCode 4.5 e iOS 6.0 (o versioni successive)
-   Visual Studio con l'[estensione Xamarin][estensione Xamarin] o [Xamarin Studio][Xamarin Studio] in OS X
-   Prima di iniziare questa esercitazione, è necessario completare l'esercitazione [Introduzione a Servizi mobili][Introduzione a Servizi mobili] o [Introduzione ai dati][Introduzione ai dati].
-   SDK di Servizi mobili di Microsoft Azure
-   Binding di [Xamarin per Active Directory Authentication Library per iOS][Xamarin per Active Directory Authentication Library per iOS].

[WACOM.INCLUDE [mobile-services-dotnet-adal-register-service][mobile-services-dotnet-adal-register-service]]

[WACOM.INCLUDE [mobile-services-dotnet-adal-register-client][mobile-services-dotnet-adal-register-client]]

## <a name="require-authentication"></a>Configurare il servizio mobile in modo che richieda l'autenticazione

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend][mobile-services-restrict-permissions-dotnet-backend]]

## <a name="add-authentication-code"></a>Aggiungere il codice di autenticazione all'app client

1.  Aggiungere il binding di Xamarin per Active Directory Authentication Library al progetto Xamarin.iOS. In Visual Studio 2013, fare clic con il pulsante destro del mouse su **Riferimenti**, quindi scegliere **Aggiungi riferimento**. Andare alla libreria di binding e fare clic su **Aggiungi**. Assicurarsi anche di aggiungere gli storyboard dell'origine ADAL (Active Directory Authentication Library).

2.  Aggiungere il codice seguente alla classe QSTodoService.

        private MobileServiceUser user;
        public MobileServiceUser User { get { return user; } }

        public async Task Authenticate()
        {
            var aadAuthority = @"<INSERT-AUTHORITY-HERE>";
            var aadResource = @"<INSERT-RESOURCE-URI-HERE>";
            var aadClientId = @"<INSERT-CLIENT-ID-HERE>";
            var aadRedirect = @"<INSERT-REDIRECT-URI-HERE>";

            ADAuthenticationError error;
            var aadContext = ADAuthenticationContext.AuthenticationContextWithAuthority(aadAuthority, out error);
            if (error != null)
            {
                throw new InvalidOperationException("Error creating the AAD context: " + error.ErrorDetails);
            }

            var tcs = new TaskCompletionSource<string>();
            aadContext.AcquireTokenWithResource(aadResource, aadClientId, new NSUrl(aadRedirect), result =>
                {
                    if (result.Status == ADAuthenticationResultStatus.SUCCEEDED)
                    {
                        tcs.SetResult(result.AccessToken);
                    }
                    else
                    {
                        string errorDetails;
                        if (result.Status == ADAuthenticationResultStatus.USER_CANCELLED)
                        {
                            errorDetails = "Authentication cancelled by user";
                        }
                        else
                        {
                            errorDetails = result.Error.ErrorDetails;
                        }

                        tcs.SetException(new InvalidOperationException("Error during authentication: " + errorDetails));
                    }
                });
            string accessToken = await tcs.Task;

            var token = new JObject(new JProperty("access_token", accessToken));

            try
            {
                user = await this.client.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, token);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

3.  Nel codice per il metodo `AuthenticateAsync` precedente sostituire **INSERT-AUTHORITY-HERE** con il nome del tenant in cui è stato effettuato il provisioning dell'applicazione, nel formato <https://login.windows.net/tenant-name.onmicrosoft.com>. È possibile copiare questo valore dalla scheda Dominio di Azure Active Directory nel [portale di gestione di Azure][portale di gestione di Azure].

4.  Nel codice per il metodo `AuthenticateAsync` precedente sostituire **INSERT-RESOURCE-URI-HERE** con l'**URI ID app** del servizio mobile. Se sono state seguite le istruzioni riportate nell'argomento sulla [registrazione in Azure Active Directory][registrazione in Azure Active Directory], l'URI ID app dovrebbe essere simile a <https://todolist.azure-mobile.net/login/aad>.

5.  Nel codice per il metodo `AuthenticateAsync` precedente sostituire **INSERT-CLIENT-ID-HERE** con l'ID client copiato dall'applicazione client nativa.

6.  Nel codice per il metodo `AuthenticateAsync` precedente, sostituire **INSERT-REDIRECT-URI-HERE** con l'endpoint /login/done del servizio mobile. Questo valore dovrebbe essere simile a <https://todolist.azure-mobile.net/login/done>.

7.  In QSTodoListViewController, modificare **ViewDidLoad** aggiungendo il seguente codice subito prima della chiamata a RefreshAsync();

        if (QSTodoService.DefaultService.User == null)
        {
            await QSTodoService.DefaultService.Authenticate();
        }

## <a name="test-client"></a>Testare il client usando l'autenticazione

1.  Dal menu Esegui, fare clic su Esegui per eseguire l'app
2.  Verrà visualizzata la richiesta di accedere ad Azure Active Directory.
3.  L'app esegue l'autenticazione e restituisce gli elementi todo.

![][]

<!-- Anchors. -->
<!-- URLs. -->

  [Windows Store C#]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication "Windows Store C#"
  [iOS]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-adal-sso-authentication "iOS"
  [Xamarin.iOS]: /it-it/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-adal-sso-authentication "Xamarin.iOS"
  [Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started/
  [Registrare il servizio mobile in Azure Active Directory]: #register-mobile-service-aad
  [Registrare l'app in Azure Active Directory]: #register-app-aad
  [Configurare il servizio mobile in modo che richieda l'autenticazione]: #require-authentication
  [Aggiungere il codice di autenticazione all'app client]: #add-authentication-code
  [Testare il client usando l'autenticazione]: #test-client
  [estensione Xamarin]: http://xamarin.com/visual-studio
  [Xamarin Studio]: http://xamarin.com/download
  [Introduzione ai dati]: /it-it/documentation/articles/partner-xamarin-mobile-services-ios-get-started-data/
  [Xamarin per Active Directory Authentication Library per iOS]: https://github.com/AzureADSamples/NativeClient-Xamarin-iOS
  [mobile-services-dotnet-adal-register-service]: ../includes/mobile-services-dotnet-adal-register-service.md
  [mobile-services-dotnet-adal-register-client]: ../includes/mobile-services-dotnet-adal-register-client.md
  [mobile-services-restrict-permissions-dotnet-backend]: ../includes/mobile-services-restrict-permissions-dotnet-backend.md
  [portale di gestione di Azure]: https://manage.windowsazure.com/
  [registrazione in Azure Active Directory]: /it-it/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
  []: ./media/mobile-services-dotnet-backend-xamarin-ios-adal-sso-authentication/mobile-services-app-run.png
