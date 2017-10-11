---
title: Introduzione all'autenticazione per app per dispositivi mobili in Xamarin iOS
description: "Informazioni su come usare le app per dispositivi mobili per autenticare gli utenti dell'app Xamarin iOS tramite vari provider di identità, tra cui AAD, Google, Facebook, Twitter e Microsoft."
services: app-service\mobile
documentationcenter: xamarin
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: 180cc61b-19c5-48bf-a16c-7181aef3eacc
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 07/05/2017
ms.author: glenga
ms.openlocfilehash: 454b2df5a9bf8cfba93befea54370957ab044d95
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2017
---
# <a name="add-authentication-to-your-xamarinios-app"></a>Aggiungere l'autenticazione all'app per Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Questo argomento descrive come autenticare gli utenti di un'app mobile del servizio app dall'applicazione client. In questa esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva Xamarin.iOS tramite un provider di identità supportato dal servizio app. In seguito all'autenticazione e all'autorizzazione da parte dell'app per dispositivi mobili, viene visualizzato il valore dell'ID utente e si sarà in grado di accedere ai dati della tabella con restrizioni.

È necessario completare prima l'esercitazione [Creare un'app per Xamarin iOS]. Se non si usa il progetto server di avvio rapido scaricato, è necessario aggiungere il pacchetto di estensione di autenticazione al progetto. Per altre informazioni sui pacchetti di estensione server, vedere l'articolo relativo all' [utilizzo dell'SDK del server back-end .NET per app per dispositivi mobili di Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Registrare l'app per l'autenticazione e configurare i servizi app
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a>Aggiungere l'app agli URL di reindirizzamento esterni consentiti

L'autenticazione sicura richiede la definizione di un nuovo schema URL per l'app. In questo modo il sistema di autenticazione reindirizza all'app al termine del processo di autenticazione. In questa esercitazione si usa lo schema URL _appname_. È tuttavia possibile usare QUALSIASI schema URL. Lo schema deve essere univoco per l'applicazione per dispositivi mobili. Per abilitare il reindirizzamento sul lato server:

1. Nel [portale di Azure] selezionare il servizio app.

2. Fare clic sull'opzione di menu **Autenticazione/Autorizzazione**.

3. In **URL di reindirizzamento esterni consentiti** specificare `url_scheme_of_your_app://easyauth.callback`.  Il valore **url_scheme_of_your_app** in questa stringa è lo schema URL per l'applicazione per dispositivi mobili.  Deve seguire le normale specifica URL per un protocollo, ovvero usare solo lettere e numeri e iniziare con una lettera.  È opportuno prendere nota della stringa scelta perché sarà necessario modificare il codice dell'applicazione per dispositivi mobili con lo schema URL in diverse posizioni.

4. Fare clic su **OK**.

5. Fare clic su **Salva**.

## <a name="restrict-permissions-to-authenticated-users"></a>Limitare le autorizzazioni agli utenti autenticati
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

&nbsp;&nbsp;4. In Visual Studio o Xamarin Studio, eseguire il progetto client su un dispositivo o un emulatore. Verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (Non autorizzato). L'errore viene registrato nella console del debugger. In Visual Studio l'errore dovrebbe quindi essere visualizzato nella finestra di output.

&nbsp;&nbsp;Questo errore non autorizzato viene generato perché l'app prova ad accedere al back-end dell'app per dispositivi mobili come utente non autenticato. La tabella *TodoItem* richiede ora l'autenticazione.

Si aggiornerà quindi l'app client per richiedere le risorse dal back-end dell'app per dispositivi mobili con un utente autenticato.

## <a name="add-authentication-to-the-app"></a>Aggiungere l'autenticazione all'app
In questa sezione si procederà alla modifica dell'app in modo da visualizzare una schermata di accesso prima dei dati. All'avvio, l'app non si connetterà al servizio app e non sarà visualizzato alcun dato. Dopo la prima volta che l'utente esegue il movimento di aggiornamento verrà visualizzata la schermata di accesso. Dopo aver eseguito correttamente l'accesso, verrà visualizzato un elenco di elementi ToDo.

1. Nel progetto client aprire il file **QSTodoService.cs** e aggiungere quanto segue tramite l'istruzione `MobileServiceUser` con la funzione di accesso alla classe QSTodoService:
 
        using UIKit;
       
        // Logged in user
        private MobileServiceUser user;
        public MobileServiceUser User { get { return user; } }
2. Aggiungere un nuovo metodo denominato **Authenticate** a **QSTodoService** con la definizione seguente:

        public async Task Authenticate(UIViewController view)
        {
            try
            {
                AppDelegate.ResumeWithURL = url => url.Scheme == "zumoe2etestapp" && client.ResumeWithURL(url);
                user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

    >[AZURE.NOTE] Se si usa un provider di identità diverso da Google, sostituire il valore passato a **LoginAsync** riportato in precedenza con uno dei seguenti: _MicrosoftAccount_, _Twitter_, _Google_ o _WindowsAzureActiveDirectory_.

3. Aprire **QSTodoListViewController.cs**. Modificare la definizione del metodo di **ViewDidLoad** rimuovendo la chiamata a **RefreshAsync()** verso la fine:
   
        public override async void ViewDidLoad ()
        {
            base.ViewDidLoad ();
   
            todoService = QSTodoService.DefaultService;
            await todoService.InitializeStoreAsync();
   
            RefreshControl.ValueChanged += async (sender, e) => {
                await RefreshAsync();
            }
   
            // Comment out the call to RefreshAsync
            // await RefreshAsync();
        }
4. Modificare il metodo **RefreshAsync** per autenticare e visualizzare una schermata di accesso se la proprietà **User** è Null. Aggiungere il codice seguente nella parte superiore della definizione del metodo:
   
        // start of RefreshAsync method
        if (todoService.User == null) {
            await QSTodoService.DefaultService.Authenticate(this);
            if (todoService.User == null) {
                Console.WriteLine("couldn't login!!");
                return;
            }
        }
        // rest of RefreshAsync method
5. Aprire **AppDelegate.cs** e aggiungere il seguente metodo:

        public static Func<NSUrl, bool> ResumeWithURL;

        public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
        {
            return ResumeWithURL != null && ResumeWithURL(url);
        }
6. Aprire il file **Info.plist** e passare a **Tipi di URL** nella sezione **Avanzate**. Configurare ora l'**identificatore** e gli **schemi URL** del tipo di URL e fare clic su **Aggiungi tipo di URL**. Gli **schemi URL** devono essere gli stessi del valore {url_scheme_of_your_app}.
7. In Visual Studio o Xamarin Studio connesso all'host di compilazione Xamarin sul Mac eseguire il progetto client destinato a un dispositivo o un emulatore. Verificare che nell'app non siano visualizzati dati.
   
    Eseguire il movimento di aggiornamento spostando verso il basso l'elenco di elementi, in modo da visualizzare la schermata di accesso. Dopo aver correttamente immesso le credenziali valide, verrà visualizzato l'elenco di elementi ToDo e sarà possibile aggiornare i dati nell'app.

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Creare un'app per Xamarin iOS]: app-service-mobile-xamarin-ios-get-started.md