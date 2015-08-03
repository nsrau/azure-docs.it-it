<properties 
	pageTitle="Autenticare l'app tramite il Single Sign-On di Active Directory Authentication Library (Xamarin.iOS) | Mobile Dev Center" 
	description="Informazioni su come autenticare gli utenti per l'accesso Single Sign-On con ADAL nell'applicazione per Xamarin iOS." 
	documentationCenter="xamarin" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor="dwrede" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/19/2015" 
	ms.author="mahender"/>

# Autenticare l'app tramite il Single Sign-On di Active Directory Authentication Library

[AZURE.INCLUDE [mobile-services-selector-adal-sso](../../includes/mobile-services-selector-adal-sso.md)]

##Panoramica

Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite Active Directory Authentication Library.

Per poter autenticare gli utenti è necessario registrare l'app in Azure Active Directory. Questa operazione si esegue in due passaggi. Prima di tutto, è necessario registrare il servizio mobile ed esporre le registrazioni sul servizio. In secondo luogo, è necessario registrare l'app Xamarin.iOS e concederle accesso alle autorizzazioni.


>[AZURE.NOTE]Questa esercitazione è stata ideata per illustrare come usare Servizi mobili per eseguire l'autenticazione tramite Single Sign-On di Azure Active Directory per le app Xamarin.iOS. Se si tratta della prima esperienza con Servizi mobili, iniziare dall'esercitazione [Introduzione a Servizi mobili].

##Prerequisiti

Per completare questa esercitazione, è necessario disporre di:

* XCode 4.5 e iOS 6.0 (o versioni successive) 
* Visual Studio con l'[estensione Xamarin] o [Xamarin Studio] in OS X
* Prima di iniziare questa esercitazione, è necessario completare l'esercitazione [Introduzione a Servizi mobili] o [Introduzione ai dati].
* Microsoft Azure Mobile Services SDK
* Binding di [Xamarin per Active Directory Authentication Library per iOS].

[AZURE.INCLUDE [mobile-services-dotnet-adal-register-service](../../includes/mobile-services-dotnet-adal-register-service.md)]

[AZURE.INCLUDE [mobile-services-dotnet-adal-register-client](../../includes/mobile-services-dotnet-adal-register-client.md)]

##Configurare il servizio mobile in modo che richieda l'autenticazione

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

##Aggiungere il codice di autenticazione all'app client

1. Aggiungere il binding di Xamarin per Active Directory Authentication Library al progetto Xamarin.iOS. In Visual Studio 2013, fare clic con il pulsante destro del mouse su **Riferimenti**, quindi scegliere **Aggiungi riferimento**. Andare alla libreria di binding e fare clic su **Aggiungi**. Assicurarsi anche di aggiungere gli storyboard dell'origine ADAL (Active Directory Authentication Library).

2. Aggiungere il codice seguente alla classe QSTodoService.

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

6. Nel codice per il metodo `AuthenticateAsync` precedente sostituire **INSERT-AUTHORITY-HERE** con il nome del tenant in cui è stato effettuato il provisioning dell'applicazione, nel formato https://login.windows.net/tenant-name.onmicrosoft.com. È possibile copiare questo valore dalla scheda Dominio di Azure Active Directory nel [portale di gestione di Azure].

7. Nel codice per il metodo `AuthenticateAsync` precedente sostituire **INSERT-RESOURCE-URI-HERE** con l'**URI ID app** del servizio mobile. Se sono state seguite le istruzioni riportate nell'argomento sulla [modalità di registrazione in Azure Active Directory], l'URI ID app dovrebbe essere simile a https://todolist.azure-mobile.net/login/aad.

8. Nel codice per il metodo `AuthenticateAsync` precedente sostituire **INSERT-CLIENT-ID-HERE** con l'ID client copiato dall'applicazione client nativa.

9. Nel codice per il metodo `AuthenticateAsync` precedente, sostituire **INSERT-REDIRECT-URI-HERE** con l'endpoint /login/done del servizio mobile. Questo valore dovrebbe essere simile a https://todolist.azure-mobile.net/login/done.


3. In QSTodoListViewController, modificare **ViewDidLoad** aggiungendo il seguente codice subito prima della chiamata a RefreshAsync();

        if (QSTodoService.DefaultService.User == null)
        {
            await QSTodoService.DefaultService.Authenticate();
        }

##Testare il client usando l'autenticazione

1. Dal menu Esegui, fare clic su Esegui per eseguire l'app 
2. Verrà visualizzata la richiesta di accedere ad Azure Active Directory.  
3. L'app esegue l'autenticazione e restituisce gli elementi todo.

   ![](./media/mobile-services-dotnet-backend-xamarin-ios-adal-sso-authentication/mobile-services-app-run.png)



<!-- URLs. -->
[Introduzione ai dati]: mobile-services-ios-get-started-data.md
[Introduzione a Servizi mobili]: mobile-services-dotnet-backend-xamarin-ios-get-started.md
[modalità di registrazione in Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[portale di gestione di Azure]: https://manage.windowsazure.com/
[Xamarin per Active Directory Authentication Library per iOS]: https://github.com/AzureADSamples/NativeClient-Xamarin-iOS
[estensione Xamarin]: http://xamarin.com/visual-studio
[Xamarin Studio]: http://xamarin.com/download

<!---HONumber=July15_HO4-->