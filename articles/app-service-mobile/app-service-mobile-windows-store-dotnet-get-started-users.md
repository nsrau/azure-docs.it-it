---
title: Aggiungere l'autenticazione all'app UWP (Universal Windows Platform) | Documentazione Microsoft
description: "Informazioni su come usare le app per dispositivi mobili del servizio app di Azure per autenticare gli utenti dell'app UWP (Universal Windows Platform) tramite vari provider di identità, tra cui AAD, Google, Facebook, Twitter e Microsoft."
services: app-service\mobile
documentationcenter: windows
author: conceptdev
manager: panarasi
editor: 
ms.assetid: 6cffd951-893e-4ce5-97ac-86e3f5ad9466
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 07/05/2017
ms.author: panarasi
ms.openlocfilehash: 4cc597f8aca13445034c8a1691b41018d4d9bc4b
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2018
---
# <a name="add-authentication-to-your-windows-app"></a>Aggiungere l'autenticazione all'app Windows
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

In questo argomento viene illustrato come aggiungere l'autenticazione basata su cloud all’app per dispositivi mobili. In questa esercitazione si aggiungerà l'autenticazione al progetto di guida introduttiva UWP (Universal Windows Platform) per App per dispositivi mobili tramite un provider di identità supportato dal Servizio app di Azure. In seguito all'autenticazione e all'autorizzazione da parte dell'app per dispositivi mobili, viene visualizzato il valore dell'ID utente.

Questa esercitazione è basata sulla guida introduttiva di App per dispositivi mobili. È necessario completare prima l'esercitazione relativa alla [creazione di un'app per dispositivi mobili](app-service-mobile-windows-store-dotnet-get-started.md).

## <a name="register"></a>Registrare l'app per l'autenticazione e configurare il servizio app
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Aggiungere l'app agli URL di reindirizzamento esterni consentiti

L'autenticazione sicura richiede la definizione di un nuovo schema URL per l'app. In questo modo il sistema di autenticazione reindirizza all'app al termine del processo di autenticazione. In questa esercitazione si usa lo schema URL _appname_. È tuttavia possibile usare QUALSIASI schema URL. Lo schema deve essere univoco per l'applicazione per dispositivi mobili. Per abilitare il reindirizzamento sul lato server:

1. Nel [portale di Azure] selezionare il servizio app.

2. Fare clic sull'opzione di menu **Autenticazione/Autorizzazione**.

3. In **URL di reindirizzamento esterni consentiti** specificare `url_scheme_of_your_app://easyauth.callback`.  Il valore **url_scheme_of_your_app** in questa stringa è lo schema URL per l'applicazione per dispositivi mobili.  Deve seguire le normale specifica URL per un protocollo, ovvero usare solo lettere e numeri e iniziare con una lettera.  È opportuno prendere nota della stringa scelta perché sarà necessario modificare il codice dell'applicazione per dispositivi mobili con lo schema URL in diverse posizioni.

4. Fare clic su **OK**.

5. Fare clic su **Save**.

## <a name="permissions"></a>Limitare le autorizzazioni agli utenti autenticati
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

A questo punto, è possibile verificare che l'accesso anonimo al back-end è stato disabilitato. Dopo aver impostato il progetto di app UWP come progetto di avvio, distribuire ed eseguire l'app. Verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (Non autorizzato). L'eccezione non gestita viene generata perché l'app prova ad accedere al codice dell'app per dispositivi mobili come utente non autenticato, mentre la tabella *TodoItem* richiede ora l'autenticazione.

A questo punto, si aggiornerà l'app in modo che autentichi gli utenti prima di richiedere risorse al servizio mobile.

## <a name="add-authentication"></a>Aggiungere l'autenticazione all'app
1. Nel file del progetto dell'app UWP MainPage.xaml.cs aggiungere il frammento di codice seguente:
   
        // Define a member variable for storing the signed-in user. 
        private MobileServiceUser user;
   
        // Define a method that performs the authentication process
        // using a Facebook sign-in. 
        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;
            try
            {
                // Change 'MobileService' to the name of your MobileServiceClient instance.
                // Sign-in using Facebook authentication.
                user = await App.MobileService
                    .LoginAsync(MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                message =
                    string.Format("You are now signed in - {0}", user.UserId);
   
                success = true;
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }
   
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
            return success;
        }
   
    L'utente viene autenticato nel codice tramite un account di accesso di Facebook. Se si usa un provider di identità diverso da Facebook, sostituire il valore di **MobileServiceAuthenticationProvider** con il nome del provider.
2. Sostituire il metodo **OnNavigatedTo()** in MainPage.xaml.cs. A questo punto aggiungere un pulsante di **accesso** all'app che attiva l'autenticazione.

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            if (e.Parameter is Uri)
            {
                App.MobileService.ResumeWithURL(e.Parameter as Uri);
            }
        }

3. Aggiungere il seguente frammento di codice a MainPage.xaml.cs:
   
        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile app.
            if (await AuthenticateAsync())
            {
                // Switch the buttons and load items from the mobile app.
                ButtonLogin.Visibility = Visibility.Collapsed;
                ButtonSave.Visibility = Visibility.Visible;
                //await InitLocalStoreAsync(); //offline sync support.
                await RefreshTodoItems();
            }
        }
4. Aprire il file di progetto MainPage.xaml, individuare l'elemento che definisce il pulsante **Save** e sostituirlo con il codice seguente:
   
        <Button Name="ButtonSave" Visibility="Collapsed" Margin="0,8,8,0" 
                Click="ButtonSave_Click">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Add"/>
                <TextBlock Margin="5">Save</TextBlock>
            </StackPanel>
        </Button>
        <Button Name="ButtonLogin" Visibility="Visible" Margin="0,8,8,0" 
                Click="ButtonLogin_Click" TabIndex="0">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Permissions"/>
                <TextBlock Margin="5">Sign in</TextBlock> 
            </StackPanel>
        </Button>
5. Aggiungere il seguente frammento di codice ad App.xaml.cs:

        protected override void OnActivated(IActivatedEventArgs args)
        {
            if (args.Kind == ActivationKind.Protocol)
            {
                ProtocolActivatedEventArgs protocolArgs = args as ProtocolActivatedEventArgs;
                Frame content = Window.Current.Content as Frame;
                if (content.Content.GetType() == typeof(MainPage))
                {
                    content.Navigate(typeof(MainPage), protocolArgs.Uri);
                }
            }
            Window.Current.Activate();
            base.OnActivated(args);
        }
6. Aprire il file Package.appxmanifest, passare a **Dichiarazioni** nell'elenco a discesa **Dichiarazioni disponibili**, selezionare **Protocollo** e fare clic sul pulsante **Aggiungi**. Configurare ora le **Proprietà** della dichiarazione **Protocollo**. In **Nome visualizzato** aggiungere il nome da mostrare agli utenti dell'applicazione. In **Nome** aggiungere il valore {url_scheme_of_your_app}.
7. Premere il tasto F5 per eseguire l'app, fare clic sul pulsante **Sign in** e accedere all'app con il provider di identità scelto. Dopo che l'accesso è stato completato, l'app funziona senza errori ed è possibile eseguire query nel back-end e aggiornare i dati.

## <a name="tokens"></a>Archiviare il token di autenticazione sul client
Nell'esempio precedente è stato illustrato un accesso standard, che richiede al client di contattare sia il provider di identità sia il servizio app ogni volta che l'app viene avviata. Non solo questo metodo è inefficiente, ma si potrebbero riscontrare problemi relativi all'uso qualora molti clienti provassero ad avviare l'app contemporaneamente. Un miglior approccio consiste nel memorizzare nella cache il token di autorizzazione restituito dal servizio app e provare a usare questo prima di usare un accesso basato su provider.

> [!NOTE]
> È possibile memorizzare nella cache il token rilasciato dai Servizi app indipendentemente dal fatto che si usi l'autenticazione gestita dal client o gestita dal servizio. In questa esercitazione viene usata l'autenticazione gestita dal servizio.
> 
> 

[!INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Passaggi successivi
Dopo aver completato questa esercitazione sull'autenticazione di base, provare a continuare fino a una delle esercitazioni seguenti:

* [Aggiungere notifiche push all'app Xamarin.Forms](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  : informazioni su come aggiungere il supporto per le notifiche push all'app e configurare il back-end dell'app per dispositivi mobili per usare Hub di notifica di Azure per l'invio di notifiche push.
* [Abilitare la sincronizzazione offline per l'app per dispositivi mobili Xamarin.Forms](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  (Abilitare la sincronizzazione offline per l'app) Informazioni su come aggiungere il supporto offline all'app usando il back-end di un'app per dispositivi mobili. La sincronizzazione offline consente agli utenti finali di interagire con un'app&mdash;visualizzando, aggiungendo e modificando i dati&mdash;anche se non è disponibile una connessione di rete.

<!-- URLs. -->
[Get started with your mobile app]: app-service-mobile-windows-store-dotnet-get-started.md
