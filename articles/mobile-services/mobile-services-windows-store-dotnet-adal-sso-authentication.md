<properties
	pageTitle="Autenticare l'app tramite il Single Sign-On di Active Directory Authentication Library (Windows Store) | Microsoft Azure"
	description="Informazioni su come autenticare gli utenti per l'accesso Single Sign-On con ADAL nell'applicazione per Windows Store."
	documentationCenter="windows"
	authors="wesmc7777"
	manager="dwrede"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-store"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="01/14/2016"
	ms.author="wesmc"/>

# Autenticare l'app tramite il Single Sign-On di Active Directory Authentication Library

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-adal-sso](../../includes/mobile-services-selector-adal-sso.md)]

##Panoramica

Questa esercitazione consente di aggiungere l'autenticazione al progetto di guida introduttiva tramite Active Directory Authentication Library per supportare le [operazioni di accesso dirette dal client](http://msdn.microsoft.com/library/azure/jj710106.aspx) con Azure Active Directory. Per supportare le [operazioni di accesso dirette dal servizio](http://msdn.microsoft.com/library/azure/dn283952.aspx) con Azure Active Directory, iniziare con l'esercitazione [Aggiungere l'autenticazione all'app di Servizi mobili](mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users.md).

Per poter autenticare gli utenti è necessario registrare l'app in Azure Active Directory. Questa operazione si esegue in due passaggi. Prima di tutto, è necessario registrare il servizio mobile ed esporre le registrazioni sul servizio. In secondo luogo, è necessario registrare l'app di Windows Store e concederle accesso alle autorizzazioni.


>[AZURE.NOTE] Questa esercitazione è stata ideata per illustrare come Servizi mobili consente di eseguire l'autenticazione con Single Sign-On di Azure Active Directory per le app di Windows Store con un'[operazione di accesso diretta dal client](http://msdn.microsoft.com/library/azure/jj710106.aspx). Se si tratta della prima esperienza con Servizi mobili, iniziare dall'esercitazione [Introduzione a Servizi mobili].


##Prerequisiti

Per completare questa esercitazione, è necessario disporre di:

* Visual Studio 2013 in esecuzione su Windows 8.1.
* Esercitazione [Introduzione a Servizi mobili] completata.
* Pacchetto NuGet Microsoft Azure Mobile Services SDK
* Pacchetto NuGet Active Directory Authentication Library

[AZURE.INCLUDE [mobile-services-dotnet-adal-register-service](../../includes/mobile-services-dotnet-adal-register-service.md)]

##Registrare l'app in Azure Active Directory

Per registrare l'app in Azure Active Directory è necessario associarla a Windows Store e disporre di un identificatore di sicurezza del pacchetto (SID) per l'app. Il SID pacchetto viene registrato con le impostazioni dell'applicazione nativa in Azure Active Directory.


###Associare l'app con un nuovo nome di app di Windows Store

1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto di app client, scegliere **Store** e quindi fare clic su **Associa applicazione a Store**.

    ![][1]

2. Accedere all'account Dev Center.

3. Immettere il nome che si desidera riservare per l'app e fare clic su **Riserva**.

    ![][2]

4. Selezionare il nuovo nome dell'app e fare clic su **Avanti**.

5. Fare clic su **Associa** per associare l'app con al nome riservato in Windows Store.


###Recuperare il SID pacchetto per l'app.

A questo punto è necessario recuperare il SID pacchetto che verrà configurato con le impostazioni dell'app nativa.

1. Accedere al [dashboard di Windows Dev Center] e fare clic su **Modifica** sull'app.

    ![][3]

2. Fare clic su **Gestione app** > **Identità app** e copiare il SID pacchetto dalla pagina.

    ![][4]


###Creare la registrazione dell'app nativa

1. Passare ad **Active Directory** nel [portale classico], quindi fare clic sulla directory.

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

7. Scorrere in basso nella pagina fino alla sezione **Autorizzazioni per altre applicazioni** e concedere accesso completo all'applicazione del servizio mobile registrata in precedenza. Fare quindi clic su **Salva**.

    ![][12]

Il servizio mobile è ora configurato in Azure Active Directory per ricevere accessi tramite Single Sign-On dall'app.



##Configurare il servizio mobile in modo che richieda l'autenticazione

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

##Aggiungere il codice di autenticazione all'app client

1. Aprire il progetto di app di Windows Store in Visual Studio.

[AZURE.INCLUDE [mobile-services-dotnet-adal-install-nuget](../../includes/mobile-services-dotnet-adal-install-nuget.md)]

4. Nella finestra Esplora soluzioni di Visual Studio aprire il file MainPage.cs e aggiungere le istruzioni using seguenti.

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

6. Nel codice per il metodo `AuthenticateAsync` precedente sostituire **INSERT-AUTHORITY-HERE** con il nome del tenant in cui è stato effettuato il provisioning dell'applicazione, nel formato https://login.windows.net/tenant-name.onmicrosoft.com. È possibile copiare questo valore dalla scheda Dominio di Azure Active Directory nel [portale di Azure classico].

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


##Testare il client usando l'autenticazione

1. In Visual Studio eseguire l'app client.
2. Verrà visualizzata la richiesta di accedere ad Azure Active Directory.
3. L'app esegue l'autenticazione e restituisce gli elementi todo.

    ![][15]




<!-- Images -->
[0]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-aad-app-manage-manifest.png
[1]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-vs-associate-app.png
[2]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-vs-reserve-store-appname.png
[3]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-store-app-edit.png
[4]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-store-app-services.png
[5]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-live-services-site.png
[6]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-store-app-package-sid.png
[7]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-select-aad.png
[8]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-aad-applications-tab.png
[9]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-native-selection.png
[10]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-native-sid-redirect-uri.png
[11]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-native-client-id.png
[12]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-native-add-permissions.png
[14]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-package-appxmanifest.png
[15]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-app-run.png

<!-- URLs. -->
[modalità di registrazione in Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[portale di Azure classico]: https://manage.windowsazure.com/
[portale classico]: https://manage.windowsazure.com/
[Introduzione a Servizi mobili]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[dashboard di Windows Dev Center]: http://go.microsoft.com/fwlink/p/?LinkID=266734

<!---HONumber=AcomDC_0413_2016-->