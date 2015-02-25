<properties pageTitle="Autenticare l'app tramite il Single Sign-On di Active Directory Authentication Library (Windows Store) | Mobile Dev Center" description="Informazioni su come autenticare gli utenti per l'accesso Single Sign-On con ADAL nell'applicazione per Windows Store." documentationCenter="windows" authors="wesmc7777" manager="dwrede" editor="" services=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="10/14/2014" ms.author="wesmc"/>

# Autenticare l'app tramite il Single Sign-On di Active Directory Authentication Library

[AZURE.INCLUDE [mobile-services-selector-adal-sso](../includes/mobile-services-selector-adal-sso.md)]

In questa esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva con Active Directory Authentication Library per supportare [operazioni di accesso dirette dal client](http://msdn.microsoft.com/it-it/library/azure/jj710106.aspx) con Azure Active Directory. Per supportare le [operazioni di accesso dirette dal servizio](http://msdn.microsoft.com/it-it/library/azure/dn283952.aspx) con Azure Active Directory, iniziare con l'esercitazione [Aggiungere l'autenticazione all'app di Servizi mobili](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/).

Per poter autenticare gli utenti è necessario registrare l'app in Azure Active Directory. Questa operazione si esegue in due passaggi. Prima di tutto, è necessario registrare il servizio mobile ed esporre le registrazioni sul servizio. In secondo luogo, è necessario registrare l'app di Windows Store e concederle accesso alle autorizzazioni.


>[AZURE.NOTE] In questa esercitazione viene descritto come usare Servizi mobili per eseguire l'autenticazione con Single Sign-On di Azure Active Directory per le app di Windows Store con un'[operazione di accesso diretta dal client](http://msdn.microsoft.com/it-it/library/azure/jj710106.aspx). Se si tratta della prima esperienza con Servizi mobili, iniziare dall'esercitazione [Introduzione a Servizi mobili].

Questa esercitazione spiega come eseguire le operazioni di base seguenti:

1. [Registrare il servizio mobile in Azure Active Directory]
2. [Registrare l'app in Azure Active Directory] 
3. [Configurare il servizio mobile in modo che richieda l'autenticazione]
4. [Aggiungere il codice di autenticazione all'app client]
5. [Testare il client usando l'autenticazione]

Per completare questa esercitazione, è necessario soddisfare i seguenti requisiti:

* Visual Studio 2013 in esecuzione su Windows 8.1
* Completamento dell'esercitazione [Introduzione a Servizi mobili] o [Introduzione ai dati].
* Pacchetto NuGet Microsoft Azure Mobile Services SDK
* Pacchetto NuGet Active Directory Authentication Library 

[AZURE.INCLUDE [mobile-services-dotnet-adal-register-service](../includes/mobile-services-dotnet-adal-register-service.md)]

## <a name="register-app-aad"></a>Registrare l'app in Azure Active Directory

Per registrare l'app in Azure Active Directory è necessario associarla a Windows Store e avere un identificatore di sicurezza del pacchetto (SID) per l'app. Il SID pacchetto viene registrato con le impostazioni dell'applicazione nativa in Azure Active Directory.


### Associare l'app con un nuovo nome di app di Windows Store

1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto di app client, scegliere **Store** e quindi fare clic su **Associa applicazione a Store**.

    ![][1]

2. Accedere all'account Dev Center.

3. Immettere il nome che si desidera riservare per l'app e fare clic su**Riserva**.

    ![][2]

4. Selezionare il nuovo nome dell'app e fare clic su**Avanti**.

5. Fare clic su**Associa** per associare l'app al nome riservato in Windows Store.


### Recuperare il SID pacchetto per l'app.

A questo punto è necessario recuperare il SID pacchetto che verrà configurato con le impostazioni dell'app nativa.

1. Accedere al dashboard di [Windows Dev Center Dashboard] e fare clic su**Modifica** sull'app.

    ![][3]

2. Fare clic su **Servizi**

    ![][4]

3. Fare clic su **Sito di servizi Live**. 

    ![][5]

4. Copiare il SID pacchetto dalla parte superiore della pagina.

    ![][6]

### Creare la registrazione dell'app nativa

1. Passare ad **Active Directory** nel [portale di gestione di Azure], quindi fare clic sulla directory.

    ![][7] 

2. Fare clic sulla scheda **Applicazioni** nella parte superiore, quindi fare clic su **AGGIUNGI** per aggiungere un'app. 

    ![][8]

3. Fare clic su **Aggiungi un'applicazione che l'organizzazione sta sviluppando**.

4. Nella procedura guidata di aggiunta applicazione immettere un **Nome** per l'applicazione e fare clic sul tipo **Applicazione client nativa**. Fare quindi clic per continuare.

    ![][9]

5. Nella casella **URI di reindirizzamento** incollare il SID pacchetto dell'app copiato in precedenza, quindi fare clic per completare la registrazione dell'app nativa.

    ![][10]

6. Fare clic sulla scheda **Configura** per l'applicazione nativa e copiare l'**ID client**, che sarà necessario più avanti.

    ![][11]

7. Scorrere in basso nella pagina fino alla sezione**Autorizzazioni per altre applicazioni** e concedere accesso completo all'applicazione del servizio mobile registrata in precedenza. Fare quindi clic su **Salva**.

    ![][12]

Il servizio mobile è ora configurato in Azure Active Directory per ricevere accessi tramite Single Sign-On dall'app.



## <a name="require-authentication"></a>Configurare il servizio mobile in modo che richieda l'autenticazione

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-code"></a>Aggiungere il codice di autenticazione all'app client

1. Aprire il progetto di app di Windows Store in Visual Studio.

[AZURE.INCLUDE [mobile-services-dotnet-adal-install-nuget](../includes/mobile-services-dotnet-adal-install-nuget.md)]

4. Nella finestra Esplora soluzioni di Visual Studio aprire il file MainPage.xaml.cs e aggiungere le istruzioni using seguenti.

        using Windows.UI.Popups;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Newtonsoft.Json.Linq;


5. Aggiungere il codice seguente alla classe MainPage che dichiara il metodo `AuthenticateAsync`.

        private MobileServiceUser user; 
        private async Task AuthenticateAsync()
        {
            string authority = "<INSERT-AUTHORITY-HERE>";
            string resourceURI = "<INSERT-RESOURCE-URI-HERE>";
            string clientID = "<INSERT-CLIENT-ID-HERE>"; 
            while (user == null)
            {
                string message;
                try
                {
                  AuthenticationContext ac = new AuthenticationContext(authority);
                  AuthenticationResult ar = await ac.AcquireTokenAsync(resourceURI, clientID, (Uri) null);
                  JObject payload = new JObject();
                  payload["access_token"] = ar.AccessToken;
                  user = await App.MobileService.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
                  message = string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (InvalidOperationException)
                {
                  message = "You must log in. Login Required";
                } 
                var dialog = new MessageDialog(message);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            } 
        }

6. Nel codice per il metodo `AuthenticateAsync` precedente sostituire **INSERT-AUTHORITY-HERE** con il nome del tenant in cui è stato effettuato il provisioning dell'applicazione, nel formato https://login.windows.net/tenant-name.onmicrosoft.com. È possibile copiare questo valore dalla scheda Dominio di Azure Active Directory nel [portale di gestione di Azure].

7. Nel codice per il metodo `AuthenticateAsync` precedente sostituire **INSERT-RESOURCE-URI-HERE** con l'**URI ID app** del servizio mobile. Se sono state seguite le istruzioni riportate nell'argomento sulla [modalità di registrazione in Azure Active Directory], l'URI ID app dovrebbe essere simile a https://todolist.azure-mobile.net/login/aad.

8. Nel codice per il metodo `AuthenticateAsync` precedente sostituire **INSERT-CLIENT-ID-HERE** con l'ID client copiato dall'applicazione client nativa.

9. Nella finestra Esplora soluzioni di Visual Studio aprire il file Package.appxmanifest del progetto client. Fare clic sulla scheda **Funzionalità** e abilitare **Applicazione enterprise** e **Reti private (client e server)**. Salvare il file.

    ![][14]

10. Nel file MainPage.cs aggiornare il gestore eventi `OnNavigatedTo` per chiamare il metodo `AuthenticateAsync` come segue.

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await AuthenticateAsync();
            await RefreshTodoItems();
        }


## <a name="test-client"></a>Testare il client usando l'autenticazione

1. In Visual Studio eseguire l'app client.
2. Verrà visualizzata la richiesta di accedere ad Azure Active Directory.  
3. L'app esegue l'autenticazione e restituisce gli elementi todo.

    ![][15]



<!-- Anchors. -->
[Registrare il servizio mobile in Azure Active Directory]: #register-mobile-service-aad
[Registrare l'app in Azure Active Directory]: #register-app-aad
[Configurare il servizio mobile in modo che richieda l'autenticazione]: #require-authentication
[Servizio mobile back-end JavaScript]: #javascript-authentication
[Servizio mobile back-end .NET]: #dotnet-authentication
[Aggiungere il codice di autenticazione all'app client]: #add-authentication-code
[Testare il client usando l'autenticazione]: #test-client

<!-- Images -->
[0]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-aad-app-manage-manifest.png
[1]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-vs-associate-app.png
[2]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-vs-reserve-store-appname.png
[3]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-store-app-edit.png
[4]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-store-app-services.png
[5]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-live-services-site.png
[6]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-store-app-package-sid.png
[7]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-select-aad.png
[8]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-aad-applications-tab.png
[9]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-native-selection.png
[10]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-native-sid-redirect-uri.png
[11]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-native-client-id.png
[12]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-native-add-permissions.png
[14]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-package-appxmanifest.png
[15]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-app-run.png

<!-- URLs. -->
[Come eseguire la registrazione ad Azure Active Directory]: /it-it/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Introduzione ai dati]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
[Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
[Dashboard di Windows Dev Center]: http://go.microsoft.com/fwlink/p/?LinkID=266734

<!--HONumber=42-->
