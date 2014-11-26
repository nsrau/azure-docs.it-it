<properties linkid="develop-mobile-tutorials-dotnet-backend-validate-modify-and-augment-data-dotnet" urlDisplayName="Validate and Modify Data" pageTitle="Use the .Net backend to validate and modify data (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to validate, modify, and augment data for your Windows Store app with .Net backend Windows Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using the .Net backend" authors="wesmc" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# Convalida e modifica dei dati in Servizi mobili mediante il back-end .NET

<div class="dev-center-tutorial-selector sublanding">
<a href="/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/" title="Windows Store C#" class="current">Windows Store C#</a>
<a href="/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/" title="Windows Store JavaScript">Windows Store JavaScript</a>
<a href="/it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-validate-modify-data/" title="Windows Phone">Windows Phone</a>
<a href="/it-it/develop/mobile/tutorials/validate-modify-and-augment-data-ios" title="iOS">iOS</a>
<a href="/it-it/develop/mobile/tutorials/validate-modify-and-augment-data-android" title="Android">Android</a>
<a href="/it-it/develop/mobile/tutorials/validate-modify-and-augment-data-html" title="HTML">HTML</a><a href="/it-it/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a>
<a href="/it-it/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>
</div>

<div class="dev-center-tutorial-subselector">
    <a href="/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/" title=".NET backend" class="current">Back-end .NET</a> | 
    <a href="/it-it/documentation/articles/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/"  title="JavaScript backend">Back-end JavaScript</a>
</div>

Questo argomento illustra come usare il codice in Servizi mobili di Azure back-end .NET per convalidare e modificare dati. Il servizio back-end .NET è un servizio HTTP creato con il framework API Web ed Entity Framework. Se si ha familiarità con la classe `ApiController` definita nel framework API Web, la classe `TableController` fornita da Servizi mobili risulterà molto intuitiva. `TableController` deriva dalla classe `ApiController` e fornisce funzionalità aggiuntive per l'interfaccia con una tabella di database. Può essere utilizzato per eseguire numerose operazioni sui dati inseriti e aggiornati, incluse la convalida e la modifica dei dati, descritte in questa esercitazione.

In questa esercitazione vengono descritte le operazioni di base seguenti:

1.  [Aggiunta della convalida della lunghezza della stringa][Aggiunta della convalida della lunghezza della stringa]
2.  [Aggiornamento del client per il supporto della convalida][Aggiornamento del client per il supporto della convalida]
3.  [Test della convalida della lunghezza][Test della convalida della lunghezza]
4.  [Aggiunta di un campo timestamp per CompleteDate][Aggiunta di un campo timestamp per CompleteDate]
5.  [Aggiornamento del client per la visualizzazione del timestamp CompleteDate][Aggiornamento del client per la visualizzazione del timestamp CompleteDate]

Questa esercitazione è basata sulle procedure e sul codice di esempio creato nell'esercitazione precedente [Introduzione a Servizi mobili][Introduzione a Servizi mobili] o [Introduzione ai dati][Introduzione ai dati]. Prima di iniziare questa esercitazione, è necessario completare l'esercitazione [Introduzione a Servizi mobili][Introduzione a Servizi mobili] o [Introduzione ai dati][Introduzione ai dati].

## <a name="string-length-validation"></a>Aggiunta della convalida

[WACOM.INCLUDE [mobile-services-dotnet-backend-add-validation](../includes/mobile-services-dotnet-backend-add-validation.md)]

## <a name="update-client-validation"></a>Aggiornamento del client

Ora che il servizio mobile è configurato per convalidare dati e inviare risposte di errore per una lunghezza di testo non valida, è necessario aggiornare l'app per gestire le risposte di errore risultanti dalla convalida. L'errore verrà rilevato come `MobileServiceInvalidOperationException` dalla chiamata dell'app client a `IMobileServiceTable<TodoItem].InsertAsync()`.

1.  Nella finestra Esplora soluzioni di Visual Studio, passare al progetto client e aprire il file MainPage.xaml.cs. Aggiungere le istruzioni **using** seguenti al file:

        using Windows.UI.Popups;
        using Newtonsoft.Json;
        using Newtonsoft.Json.Linq;

2.  Nel file MainPage.xaml.cs sostituire il metodo **InsertTodoItem** esistente con quanto segue:

        private async void InsertTodoItem(TodoItem todoItem)
        {
            // This code inserts a new TodoItem into the database. When the operation completes
            // and Mobile Services has assigned an Id, the item is added to the CollectionView
            MobileServiceInvalidOperationException invalidOpException = null;
            try
            {
                await todoTable.InsertAsync(todoItem);
                items.Add(todoItem);
            }
            catch(MobileServiceInvalidOperationException e)
            {
                invalidOpException = e;
            }
            if (invalidOpException != null)
            {
                string strJsonContent = await invalidOpException.Response.Content.ReadAsStringAsync();
                var responseContent = JObject.Parse(strJsonContent);
                MessageDialog errormsg = new MessageDialog(string.Format("{0} (HTTP {1})", 
                    (string)responseContent["message"],(int)invalidOpException.Response.StatusCode), 
                    invalidOpException.Message);
                var ignoreAsyncOpResult = errormsg.ShowAsync();
            }
        }

    Questa versione del metodo include la gestione degli errori per l'eccezione **MobileServiceInvalidOperationException** che visualizza il messaggio di errore deserializzato dal contenuto della risposta in una finestra di dialogo con messaggio.

## <a name="test-length-validation"></a>Test della convalida della lunghezza

1.  In Esplora soluzioni in Visual Studio fare clic con il pulsante destro del mouse sul progetto di app client e quindi scegliere **Imposta come progetto di avvio**. Quindi, premere il tasto **F5** per avviare l'app che ospita il servizio localmente in IIS Express.

2.  Immettere per un nuovo elemento Todo un testo di lunghezza superiore a 10 caratteri, quindi fare clic su **Salva**.

    ![][0]

3.  Se si immette testo non valido verrà visualizzato un messaggio simile al seguente.

    ![][1]

## <a name="add-timestamp"></a>Aggiunta di un campo timestamp per CompleteDate

[WACOM.INCLUDE [mobile-services-dotnet-backend-add-completedate](../includes/mobile-services-dotnet-backend-add-completedate.md)]

## <a name="update-client-timestamp"></a>Aggiornamento del client per la visualizzazione del timestamp CompleteDate

Nell'ultimo passaggio si procederà all'aggiornamento del client affinché visualizzi i nuovi dati **CompleteDate**.

1.  In Esplora soluzioni di Visual Studio, nel progetto client todolist, aprire il file MainPage.xaml e sostituire l'elemento **CheckBoxComplete** con la definizione riportata di seguito. Salvare il file. Il gestore eventi su **CheckBoxComplete** verrà modificato in modo da gestire l'evento `click`. Si aggiungerà inoltre un blocco test accanto alla casella di controllo eseguendone il binding al timestamp di data completo.

        <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" 
          Click="CheckBoxComplete_Clicked" Content="{Binding Text}" Margin="10,5" VerticalAlignment="Center"/>
        <TextBlock Name="textCompleteDate" Text="{Binding CompleteDate}" VerticalAlignment="Center"/>

2.  In Esplora soluzioni di Visual Studio, nel progetto client todolist, aprire il file MainPage.xaml.cs, sostituire il gestore eventi `CheckBoxComplete_Checked` con il gestore eventi `CheckBoxComplete_Clicked` che segue. In tal modo sarà visualizzata la data completa dopo aver completato l'elemento.

        private void CheckBoxComplete_Clicked(object sender, RoutedEventArgs e)
        {
            CheckBox cb = (CheckBox)sender;
            TodoItem item = cb.DataContext as TodoItem;
            UpdateCheckedTodoItem(item);
        }

3.  Nel file MainPage.xaml.cs sostituire quindi la classe **TodoItem** esistente con la definizione seguente, che include la nuova proprietà **CompleteData** come tipo nullable.

        public class TodoItem
        {
            public string Id { get; set; }
            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }
            [JsonProperty(PropertyName = "complete")]
            public bool Complete { get; set; }        
            [JsonProperty(PropertyName = "CompleteDate")]
            public DateTime? CompleteDate { get; set; }
        }

    > [WACOM.NOTE] L'attributo `DataMemberAttribute` comunica al client di eseguire il mapping della nuova proprietà `CompleteDate` dell'app alla colonna `CompleteDate` definita nella tabella TodoItem. Grazie a questo attributo, i nomi di proprietà sugli oggetti dell'app possono essere diversi dai nomi di colonna in database SQL.

4.  Nel file MainPage.xaml.cs rimuovere o impostare come commento la funzione della clausola `.Where` nel metodo **RefreshTodoItems** esistente in modo da includere nei risultati gli elementi todo completati.

            // This query filters out completed TodoItems and 
            // items without a timestamp. 
            items = await todoTable
               //.Where(todoItem => todoItem.Complete == false)
               .ToCollectionAsync();

5.  Nel file MainPage.xaml.cs, aggiornare il metodo **updateCheckedTodoItem** nel modo seguente in modo che gli elementi vengano aggiornati dopo un aggiornamento e gli elementi completati non vengano rimossi dall'elenco. Salvare il file.

        private async void UpdateCheckedTodoItem(TodoItem item)
        {
            // This code takes a freshly completed TodoItem and updates the database.
            await todoTable.UpdateAsync(item);
            //items.Remove(item);
            RefreshTodoItems();
        }

6.  Nella finestra Esplora soluzioni di Visual Studio, fare clic con il pulsante destro del mouse su **Soluzione**, quindi scegliere **Ricompila soluzione** per ricompilare sia il client sia il servizio back-end .NET. Verificare che entrambi i progetti vengano compilati senza errori.

    ![][2]

7.  Premere **F5** per eseguire l'app client e il servizio in locale. Aggiungere alcuni nuovi elementi e selezionarne alcuni per contrassegnarli come completati modo da visualizzare l'aggiornamento del timestamp di **CompleteDate**.

    ![][3]

8.  In Esplora soluzioni di Visual Studio fare clic con il pulsante destro del mouse sul progetto del servizio todolist, quindi scegliere **Pubblica**. Pubblicare il servizio back-end .NET in Microsoft Azure utilizzando il file delle impostazioni di pubblicazione che è stato scaricato dal portale di Azure.

9.  Aggiornare il file App.xaml.cs file per il progetto client rimuovendo i simboli di commento dalla connessione all'indirizzo del servizio mobile. Testare l'app sul servizio back-end .NET ospitato nell'account Azure.

## <a name="next-steps"> </a>Passaggi successivi

Dopo avere completato anche questa esercitazione, provare a svolgere l'ultima esercitazione della serie relativa ai dati: [Utilizzo del paging per ridefinire le query][Utilizzo del paging per ridefinire le query].

Gli script server vengono inoltre utilizzati per l'autorizzazione degli utenti e per l'invio di notifiche push. Per ulteriori informazioni, vedere le esercitazioni seguenti:

-   [Autorizzazione lato servizio degli utenti][Autorizzazione lato servizio degli utenti]
    Informazioni su come filtrare i dati in base all'ID di un utente autenticato.

-   [Introduzione alle notifiche push][Introduzione alle notifiche push]
    Informazioni sull'invio di una notifica push di base all'app.

-   [Riferimento per i concetti e le procedure di Servizi mobili con .NET][Riferimento per i concetti e le procedure di Servizi mobili con .NET]
    Ulteriori informazioni su come utilizzare Servizi mobili con .NET.



  [Aggiunta della convalida della lunghezza della stringa]: #string-length-validation
  [Aggiornamento del client per il supporto della convalida]: #update-client-validation
  [Test della convalida della lunghezza]: #test-length-validation
  [Aggiunta di un campo timestamp per CompleteDate]: #add-timestamp
  [Aggiornamento del client per la visualizzazione del timestamp CompleteDate]: #update-client-timestamp
  [Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
  [Introduzione ai dati]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
  [0]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/mobile-services-invalid-text-length.png
  [1]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/mobile-services-invalid-text-length-exception-dialog.png
  [2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/mobile-services-rebuild-solution.png
  [3]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/mobile-services-final-local-app-run.png
  [Utilizzo del paging per ridefinire le query]: /it-it/develop/mobile/tutorials/add-paging-to-data-dotnet
  [Autorizzazione lato servizio degli utenti]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts/
  [Introduzione alle notifiche push]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/
  [Riferimento per i concetti e le procedure di Servizi mobili con .NET]: /it-it/develop/mobile/how-to-guides/work-with-net-client-library
