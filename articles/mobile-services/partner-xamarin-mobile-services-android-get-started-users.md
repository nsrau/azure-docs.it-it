---
title: Introduzione all'autenticazione (Xamarin.Android) - Servizi mobili
description: Informazioni su come usare l'autenticazione nell'app Servizi mobili di Azure per Xamarin.Android.
services: mobile-services
documentationcenter: xamarin
manager: dwrede
author: lindydonna
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 07/21/2016
ms.author: donnam

---
# Aggiungere l'autenticazione all'app di Servizi mobili
[!INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

&nbsp;

[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

> Per la versione equivalente di questo argomento per le app per dispositivi mobili, vedere [Aggiungere l'autenticazione all'app Xamarin.Android](../app-service-mobile/app-service-mobile-xamarin-android-get-started-users.md).
> 
> 

<p>Questo argomento descrive come autenticare gli utenti in Servizi mobili di Azure dalla propria app di Xamarin.Android. Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite un provider di identità supportato da Servizi mobili. In seguito all'autenticazione e all'autorizzazione di Servizi mobili, viene visualizzato il valore dell'ID utente.</p>

Questa esercitazione descrive le operazioni di base per abilitare l'autenticazione in un'app:

1. [Registrare l'app per l'autenticazione e configurare Servizi mobili]
2. [Limitare le autorizzazioni per la tabella agli utenti autenticati]
3. [Aggiungere l'autenticazione all'app]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. È inoltre necessario completare prima l'esercitazione [Introduzione a Servizi mobili].

Per completare questa esercitazione, è necessario avere Visual Studio con Xamarin su Windows o Xamarin Studio su Mac OS X. Le istruzioni complete sull'installazione sono disponibili nella pagina relativa alla [configurazione e installazione di Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx).

## <a name="register"></a>Registrare l'app per l'autenticazione e configurare Servizi mobili
[!INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

## <a name="permissions"></a>Limitare le autorizzazioni agli utenti autenticati
[!INCLUDE [mobile-services-restrict-permissions-javascript-backend](../../includes/mobile-services-restrict-permissions-javascript-backend.md)]

1. In Xamarin Studio aprire il progetto creato dopo aver completato l'esercitazione [Introduzione a Servizi mobili].
2. Nel menu **Run** fare clic su **Start debugging** per avviare l'app e verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (Non autorizzato).
   
     L'eccezione non gestita viene generata perché l'app tenta di accedere a Servizi mobili come utente non autenticato, mentre la tabella *TodoItem* richiede ora l'autenticazione.

A questo punto, si aggiornerà l'app in modo che autentichi gli utenti prima di richiedere risorse al servizio mobile.

## <a name="add-authentication"></a>Aggiungere l'autenticazione all'app
1. Aggiungere la proprietà seguente alla classe **ToDoActivity**:
   
        private MobileServiceUser user;
2. Aggiungere il metodo seguente alla classe **ToDoActivity**:
   
        private async Task Authenticate()
        {
            try
            {
                user = await client.LoginAsync(this, MobileServiceAuthenticationProvider.MicrosoftAccount);
                CreateAndShowDialog(string.Format("you are now logged in - {0}", user.UserId), "Logged in!");
            }
            catch (Exception ex)
            {
                CreateAndShowDialog(ex, "Authentication failed");
            }
        }
   
    In questo modo viene creato un nuovo metodo per gestire il processo di autenticazione. L'utente viene autenticato tramite un account di accesso Microsoft. Viene visualizzata una finestra di dialogo che riporta l'ID dell'utente autenticato. Senza un'autenticazione positiva non è possibile procedere.
   
   > [!NOTE]
   > Se si usa un provider di identità diverso da Microsoft, sostituire il valore passato al metodo **login** riportato in precedenza con uno dei seguenti: *Facebook*, *Google*, *Twitter* o *WindowsAzureActiveDirectory*.
   > 
   > 
3. Nel metodo **OnCreate** aggiungere la riga di codice seguente dopo il codice che crea un'istanza dell'oggetto `MobileServiceClient`.
   
        await Authenticate();
   
    Questa chiamata avvia il processo di autenticazione e ne attende il completamento in modalità asincrona.
4. Spostare il codice rimanente dopo `await Authenticate();` nel metodo **OnCreate** in un nuovo metodo **CreateTable** simile al seguente:
   
        private async Task CreateTable()
        {
   
            await InitLocalStoreAsync();
   
            // Get the Mobile Service Table instance to use
            toDoTable = client.GetSyncTable<ToDoItem>();
   
            textNewToDo = FindViewById<EditText>(Resource.Id.textNewToDo);
   
            // Create an adapter to bind the items with the view
            adapter = new ToDoItemAdapter(this, Resource.Layout.Row_List_To_Do);
            var listViewTodo = FindViewById<ListView>(Resource.Id.listViewToDo);
            listViewTodo.Adapter = adapter;
   
            // Load the items from the Mobile Service
            await RefreshItemsFromTableAsync();
        }
5. Chiamare quindi il nuovo metodo **CreateTable** in **OnCreate** dopo la chiamata **Authenticate** aggiunta nel passaggio 2:
   
        await CreateTable();
6. Nel menu **Run** fare clic su **Start debugging** per avviare l'app e accedere con il provider di identità scelto.
   
       Dopo avere eseguito l'accesso, l'app dovrebbe funzionare senza errori e dovrebbe essere possibile eseguire query in Servizi mobili e aggiornare i dati.

## Download dell'esempio completato
Scaricare il [progetto di esempio completato]. Assicurarsi di aggiornare le variabili **applicationURL** e **applicationKey** con le proprie impostazioni di Azure.

## <a name="next-steps"></a>Passaggi successivi
Nella prossima esercitazione, [Autorizzazione di utenti con script], il valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato verrà utilizzato per filtrare i dati restituiti da Servizi mobili.

<!-- Anchors. -->
[Registrare l'app per l'autenticazione e configurare Servizi mobili]: #register
[Limitare le autorizzazioni per la tabella agli utenti autenticati]: #permissions
[Aggiungere l'autenticazione all'app]: #add-authentication
[Next Steps]: #next-steps

<!-- Images. -->
[4]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-services-selection.png
[5]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-service-uri.png

[13]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-identity-tab.png
[14]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-portal-data-tables.png
[15]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-portal-change-table-perms.png

<!-- URLs. -->
[Introduzione a Servizi mobili]: partner-xamarin-mobile-services-android-get-started.md
[Autorizzazione di utenti con script]: mobile-services-javascript-backend-service-side-authorization.md
[progetto di esempio completato]: http://go.microsoft.com/fwlink/p/?LinkId=331328

<!---HONumber=AcomDC_0727_2016-->