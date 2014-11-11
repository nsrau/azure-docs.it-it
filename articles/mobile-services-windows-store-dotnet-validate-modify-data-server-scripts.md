<properties linkid="develop-mobile-tutorials-validate-modify-and-augment-data-dotnet" urlDisplayName="Validate and Modify Data" pageTitle="User server scripts to validate and modify data (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use server scripts to validate, modify, and augment data for your Windows Store app with Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Convalida e modifica dei dati in Servizi mobili mediante script del server

<div class="dev-center-tutorial-selector sublanding">

[Windows Store C#][Windows Store C#][Windows Store JavaScript][Windows Store JavaScript][Windows Phone][Windows Phone][iOS][iOS][Android][Android][HTML][HTML][Xamarin.iOS][Xamarin.iOS][Xamarin.Android][Xamarin.Android]

</div>

<div class="dev-center-tutorial-subselector">

[Back-end .NET][Back-end .NET] | [Back-end JavaScript][Back-end JavaScript]

</div>

<div class="dev-onpage-video-clear clearfix">

<div class="dev-onpage-left-content">

In questo argomento viene illustrato come utilizzare gli script del server in Servizi mobili di Azure. Gli script del server vengono registrati in un servizio mobile e possono essere utilizzati per eseguire numerose operazioni sui dati inseriti e aggiornati, incluse la convalida e la modifica dei dati. In questa esercitazione si procederà alla definizione e alla registrazione di script del server per la convalida e la modifica dei dati. Poiché il comportamento degli script sul lato server spesso influisce sul client, si procederà quindi all'aggiornamento dell'app di Windows Store per implementare questi nuovi comportamenti.

</div>

<div class="dev-onpage-video-wrapper">

[video di esercitazione][video di esercitazione] [<span class="icon">Riproduci video</span>][video di esercitazione] <span class="time">09:54:00</span>

</div>

</div>

In questa esercitazione vengono descritte le operazioni di base seguenti:

1.  [Aggiunta della convalida della lunghezza della stringa][Aggiunta della convalida della lunghezza della stringa]
2.  [Aggiornamento del client per il supporto della convalida][Aggiornamento del client per il supporto della convalida]
3.  [Aggiunta di un timestamp all'inserimento][Aggiunta di un timestamp all'inserimento]
4.  [Aggiornamento del client per la visualizzazione del timestamp][Aggiornamento del client per la visualizzazione del timestamp]

Questa esercitazione è basata sulle procedure e sull'app di esempio creata nell'esercitazione precedente [Introduzione ai dati][Introduzione ai dati]. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione ai dati][Introduzione ai dati].

## <a name="string-length-validation"></a>Aggiunta della convalida

È sempre consigliabile convalidare la lunghezza dei dati inviati dagli utenti. A questo scopo, è innanzitutto necessario registrare uno script che convalida la lunghezza dei dati di stringa inviati al servizio mobile e rifiuta le stringhe troppo lunghe, in questo caso lunghe più di 10 caratteri.

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure], fare clic su **Mobile Services** e quindi sull'app.

    ![][0]

2.  Fare clic sulla scheda **Data** e quindi sulla tabella **TodoItem**.

    ![][1]

3.  Fare clic su **Script** e quindi selezionare l'operazione **Insert**.

    ![][2]

4.  Sostituire lo script esistente con la funzione seguente e quindi fare clic su **Save**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                request.execute();
            }
        }

    Questo script verifica la lunghezza della proprietà **TodoItem.text** e invia una risposta di errore quando la lunghezza supera i 10 caratteri. In caso contrario, viene chiamato il metodo **execute** per completare l'operazione di inserimento.

    <div class="dev-callout">

    **Nota**
    È possibile rimuovere uno script registrato nella scheda **Script** facendo clic su **Clear** e quindi su **Save**.

    </div>

## <a name="update-client-validation"></a>Aggiornamento del client

Ora che il servizio mobile convalida dati e invia risposte di errore, è necessario aggiornare l'app .NET affinché sia in grado di gestire le risposte di errore dalla convalida.

1.  In Visual Studio 2012 Express per Windows 8 aprire il progetto modificato dopo avere completato l'esercitazione [Introduzione ai dati][Introduzione ai dati].

2.  Premere **F5** per eseguire l'app, digitare un testo più lungo di 10 caratteri in **Insert a TodoItem**, quindi fare clic su **Save**.

    Si noti che l'app genera un'eccezione **MobileServiceInvalidOperationException** non gestita a seguito della risposta 400 (Richiesta non valida) restituita dal servizio mobile.

3.  Aprire il file del progetto MainPage.xaml.cs e aggiungere l'istruzione **using** seguente:

        using Windows.UI.Popups;

4.  Sostituire il metodo **InsertTodoItem** esistente con quanto segue:

        private async void InsertTodoItem(TodoItem todoItem)
        {
            // This code inserts a new TodoItem into the database.
            // When the operation completes and Mobile Services has
            // assigned an Id, the item is added to the collection.
            try
            {
                await todoTable.InsertAsync(todoItem);
                items.Add(todoItem);
            }
            catch (MobileServiceInvalidOperationException e)
            {
                MessageDialog errormsg = new MessageDialog(e.Message, 
                    string.Format("{0} (HTTP {1})",                     
                    e.Response.ReasonPhrase,
                    (int)e.Response.StatusCode));
                var ignoreAsyncOpResult = errormsg.ShowAsync();
            }
        }

    Questa versione del metodo include la gestione degli errori per l'eccezione **MobileServiceInvalidOperationException** che visualizza la risposta di errore in una finestra popup.

## <a name="add-timestamp"></a>Aggiunta di un timestamp

Nelle attività precedenti si è proceduto alla convalida di un'operazione di inserimento che è accettata o rifiutata. Ora, si procederà all'aggiornamento dei dati inseriti utilizzando uno script server che aggiunge una proprietà timestamp all'oggetto prima dell'inserimento.

<div class="dev-callout">

**Nota**
La proprietà timestamp **createdAt** illustrata in questo esempio è ora ridondante. Servizi mobili crea automaticamente una proprietà di sistema **\_\_createdAt** per ogni tabella. È possibile utilizzare questa proprietà di sistema nell'applicazione semplicemente aggiungendo il membro seguente alla classe TodoItem

    [JsonProperty(PropertyName = "__createdAt")]
    public DateTime createdAt { set; get; }

</div>

1.  Nella scheda **Scripts** del [portale di gestione][portale di gestione di Azure] sostituire lo script **Insert** corrente con la funzione seguente, quindi fare clic su **Save**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                item.createdAt = new Date();
                request.execute();
            }
        }

    Questa funzione aumenta lo script insert precedente tramite l'aggiunta di una nuova proprietà timestamp **createdAt** all'oggetto prima che questo venga inserito dalla chiamata al metodo **request**.**execute**.

    <div class="dev-callout">

    **Nota**
    È necessario abilitare lo schema dinamico la prima volta che viene eseguito lo script insert. Quando è abilitato lo schema dinamico, Servizi mobili aggiunge automaticamente la colonna **createdAt** alla tabella **TodoItem** alla prima esecuzione. Lo schema dinamico è abilitato per impostazione predefinita per un nuovo servizio mobile e deve essere disabilitato prima che l'app venga pubblicata in Windows Store.

    </div>

2.  In Visual Studio premere **F5** per eseguire l'app, digitare un testo più breve di 10 caratteri in **Insert a TodoItem**, quindi fare clic su **Salva**.

    Si noti che il nuovo timestamp non viene visualizzato nell'interfaccia utente dell'app.

3.  Tornare al portale di gestione e fare clic sulla scheda **Browse** nella tabella **todoitem**.

    È ora presente una colonna **createdAt** che contiene un valore timestamp per il nuovo elemento inserito.

È ora necessario aggiornare l'app di Windows Store affinché visualizzi questa nuova colonna.

## <a name="update-client-timestamp"></a>Nuovo aggiornamento del client

Il client del servizio mobile ignorerà i dati delle risposte che non è in grado di serializzare nelle proprietà del tipo definito. Nell'ultimo passaggio si procederà all'aggiornamento del client affinché visualizzi questi nuovi dati.

1.  In Visual Studio aprire il file MainPage.xaml.cs e sostituire la classe **TodoItem** esistente con la definizione seguente:

        public class TodoItem
        {
            public string Id { get; set; }

            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }

            [JsonProperty(PropertyName = "complete")]
            public bool Complete { get; set; }

            [JsonProperty(PropertyName = "createdAt")]
            public DateTime? CreatedAt { get; set; }
        }

    Questa nuova definizione di classe include la nuova proprietà timestamp come tipo DateTime nullable.

    <div class="dev-callout">

    **Nota**
    L'attributo `DataMemberAttribute` comunica al client di eseguire il mapping della nuova proprietà `CreatedAt` dell'app alla colonna `createdAt` definita nella tabella TodoItem che presenta una convenzione per l'uso di maiuscole e minuscole diversa. Grazie a questo attributo, i nomi di proprietà sugli oggetti dell'app possono essere diversi dai nomi di colonna in database SQL. Senza questo attributo, viene generato un errore dovuto alle differenze di convenzione per l'utilizzo di maiuscole e minuscole.

    </div>

2.  Aggiungere l'elemento XAML seguente sotto l'elemento **CheckBoxComplete** nel file MainPage.xaml:

        <TextBlock Name="WhenCreated" Text="{Binding CreatedAt}" VerticalAlignment="Center"/>

    La nuova proprietà **CreatedAt** verrà visualizzata in una casella di testo.

3.  Premere **F5** per eseguire l'app.

    Si noti che il timestamp è visualizzato solo per gli elementi inseriti dopo l'aggiornamento dello script insert.

4.  Sostituire il metodo **RefreshTodoItems** esistente con il codice seguente:

        private async void RefreshTodoItems()
        {
            // This query filters out completed TodoItems and 
            // items without a timestamp. 
            items = await todoTable
               .Where(todoItem => todoItem.Complete == false
                   && todoItem.CreatedAt != null)
               .ToCollectionAsync();

            ListItems.ItemsSource = items;
        }

    Questo metodo aggiorna la query affinché filtri anche gli elementi che non dispongono di un valore timestamp.

5.  Premere **F5** per eseguire l'app.

    Si noti che tutti gli elementi creati senza un valore timestamp non vengono più visualizzati nell'interfaccia utente.

L'esercitazione relativa all'utilizzo dei dati è stata completata.

## <a name="next-steps"> </a>Passaggi successivi

Dopo avere completato anche questa esercitazione, provare a svolgere l'ultima esercitazione della serie relativa ai dati: [Utilizzo del paging per ridefinire le query][Utilizzo del paging per ridefinire le query].

Gli script server vengono inoltre utilizzati per l'autorizzazione degli utenti e per l'invio di notifiche push. Per ulteriori informazioni, vedere le esercitazioni seguenti:

-   [Autorizzazione di utenti con script][Autorizzazione di utenti con script]
    Informazioni su come filtrare i dati in base all'ID di un utente autenticato.

-   [Introduzione alle notifiche push][Introduzione alle notifiche push]
    Informazioni sull'invio di una notifica push di base all'app.

-   [Riferimento per gli script del server di Servizi mobili][Riferimento per gli script del server di Servizi mobili]
    Ulteriori informazioni sulla registrazione e l'utilizzo di script del server.

-   [Riferimento per i concetti e le procedure di Servizi mobili con .NET][Riferimento per i concetti e le procedure di Servizi mobili con .NET]
    Ulteriori informazioni su come utilizzare Servizi mobili con .NET.



  [Windows Store C#]: /it-it/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet "Windows Store C#"
  [Windows Store JavaScript]: /it-it/develop/mobile/tutorials/validate-modify-and-augment-data-js "Windows Store JavaScript"
  [Windows Phone]: /it-it/develop/mobile/tutorials/validate-modify-and-augment-data-wp8 "Windows Phone"
  [iOS]: /it-it/develop/mobile/tutorials/validate-modify-and-augment-data-ios "iOS"
  [Android]: /it-it/develop/mobile/tutorials/validate-modify-and-augment-data-android "Android"
  [HTML]: /it-it/develop/mobile/tutorials/validate-modify-and-augment-data-html "HTML"
  [Xamarin.iOS]: /it-it/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios "Xamarin.iOS"
  [Xamarin.Android]: /it-it/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android "Xamarin.Android"
  [Back-end .NET]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/ ".NET backend"
  [Back-end JavaScript]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/ "JavaScript backend"
  [video di esercitazione]: http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Validate-and-Modify-Data-with-Server-Scripts-in-Windows-Azure-Mobile-Services
  [Aggiunta della convalida della lunghezza della stringa]: #string-length-validation
  [Aggiornamento del client per il supporto della convalida]: #update-client-validation
  [Aggiunta di un timestamp all'inserimento]: #add-timestamp
  [Aggiornamento del client per la visualizzazione del timestamp]: #update-client-timestamp
  [Introduzione ai dati]: /it-it/develop/mobile/tutorials/get-started-with-data-dotnet
  [portale di gestione di Azure]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/mobile-services-selection.png
  [1]: ./media/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/mobile-portal-data-tables.png
  [2]: ./media/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/mobile-insert-script-users.png
  [Utilizzo del paging per ridefinire le query]: /it-it/develop/mobile/tutorials/add-paging-to-data-dotnet
  [Autorizzazione di utenti con script]: /it-it/develop/mobile/tutorials/authorize-users-in-scripts-dotnet
  [Introduzione alle notifiche push]: /it-it/develop/mobile/tutorials/get-started-with-push-dotnet
  [Riferimento per gli script del server di Servizi mobili]: http://go.microsoft.com/fwlink/?LinkId=262293
  [Riferimento per i concetti e le procedure di Servizi mobili con .NET]: /it-it/develop/mobile/how-to-guides/work-with-net-client-library
