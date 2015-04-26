<properties 
	pageTitle="Usare il back-end .NET per convalidare e modificare i dati (Windows Store) | Mobile Dev Center" 
	description="Informazioni su come convalidare, modificare e aumentare i dati per l'app per Windows Store con i servizi mobili di back-end .NET di Azure." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/23/2014" 
	ms.author="wesmc"/>

# Convalidare e modificare i dati in Servizi mobili mediante il back-end .NET

[AZURE.INCLUDE [mobile-services-selector-validate-modify-data](../includes/mobile-services-selector-validate-modify-data.md)]

Questo argomento descrive come usare il codice in Servizi mobili di Azure back-end .NET per convalidare e modificare dati. Il servizio back-end .NET è un servizio HTTP creato con il framework API Web ed Entity Framework. Se si ha familiarità con la classe `ApiController` definita nel framework API Web, la classe `TableController` fornita da Servizi mobili risulterà molto intuitiva. `TableController` deriva dalla classe `ApiController` e fornisce funzionalità aggiuntive per l'interfaccia con una tabella di database. Può essere usato per eseguire numerose operazioni sui dati inseriti e aggiornati, incluse la convalida e la modifica dei dati, descritte in questa esercitazione. 

Questa esercitazione descrive le operazioni di base seguenti:

1. [Aggiungere la convalida della lunghezza della stringa]
2. [Aggiornare il client per il supporto della convalida]
3. [Testare la convalida della lunghezza]
4. [Aggiungere un campo timestamp per CompleteDate]
5. [Aggiornare il client per la visualizzazione del timestamp CompleteDate]

Questa esercitazione è basata sulle procedure e sul codice di esempio creato nell'esercitazione precedente, [Introduzione a Servizi mobili] o [Introduzione ai dati]. Prima di iniziare questa esercitazione, è necessario completare l'esercitazione [Introduzione a Servizi mobili] o [Introduzione ai dati].  

## <a name="string-length-validation"></a>Aggiungere la convalida

[AZURE.INCLUDE [mobile-services-dotnet-backend-add-validation](../includes/mobile-services-dotnet-backend-add-validation.md)]


## <a name="update-client-validation"></a>Aggiornare il client

Ora che il servizio mobile è configurato per convalidare dati e inviare risposte di errore per una lunghezza di testo non valida, è necessario aggiornare l'app per gestire le risposte di errore risultanti dalla convalida. L'errore verrà rilevato come `MobileServiceInvalidOperationException` dalla chiamata dell'app client a `IMobileServiceTable<TodoItem].InsertAsync()`.

1. Nella finestra Esplora soluzioni di Visual Studio, passare al progetto client e aprire il file MainPage.xaml.cs. Aggiungere le istruzioni **using** seguenti al file:

        using Windows.UI.Popups;
        using Newtonsoft.Json;
        using Newtonsoft.Json.Linq;

2. In MainPage.xaml.cs sostituire il metodo **InsertTodoItem** esistente con quanto segue:

        private async void InsertTodoItem(TodoItem todoItem)
        {
            // This code inserts a new TodoItem into the database. When the operation completes
            // and Mobile Services has assigned an Id, the item is added to the CollectionView
            MobileServiceInvalidOperationException invalidOpException = null;
            try
            {
                await todoTable.InsertAsync(todoItem);
                ite
	ms.Add(todoItem);
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

## <a name="test-length-validation"></a>Testare la convalida della lunghezza

1. In Esplora soluzioni in Visual Studio fare clic con il pulsante destro del mouse sul progetto di app client e quindi scegliere **Imposta come progetto di avvio**. Quindi, premere il tasto **F5** per avviare l'app che ospita il servizio localmente in IIS Express.

2. Immettere per un nuovo elemento Todo un testo di lunghezza superiore a 10 caratteri, quindi fare clic su **Salva**.

    ![][1]

3. Se si immette testo non valido verrà visualizzato un messaggio simile al seguente.

    ![][2]

## <a name="add-timestamp"></a>Aggiungere un campo timestamp per CompleteDate

[AZURE.INCLUDE [mobile-services-dotnet-backend-add-completedate](../includes/mobile-services-dotnet-backend-add-completedate.md)]




## <a name="update-client-timestamp"></a>Aggiornare il client per la visualizzazione del timestamp CompleteDate

Nell'ultimo passaggio si procederà all'aggiornamento del client affinché visualizzi i nuovi dati **CompleteDate**. 


1. In Esplora soluzioni di Visual Studio, nel progetto client todolist, aprire il file MainPage.xaml e sostituire l'elemento **CheckBoxComplete** con le definizioni riportate di seguito. Salvare il file. Il gestore eventi su **CheckBoxComplete** verrà modificato in modo da gestire l'evento `click`. Si aggiungerà inoltre un blocco test accanto alla casella di controllo eseguendone il binding al timestamp di data completo.
	      
        <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" 
          Click="CheckBoxComplete_Clicked" Content="{Binding Text}" Margin="10,5" VerticalAlignment="Center"/>
        <TextBlock Name="textCompleteDate" Text="{Binding CompleteDate}" VerticalAlignment="Center"/>


2. In Esplora soluzioni di Visual Studio, nel progetto client todolist, aprire il file MainPage.xaml.cs, sostituire il gestore eventi `CheckBoxComplete_Checked` con il gestore eventi `CheckBoxComplete_Clicked` che segue. In tal modo sarà visualizzata la data completa dopo aver completato l'elemento.

        private void CheckBoxComplete_Clicked(object sender, RoutedEventArgs e)
        {
            CheckBox cb = (CheckBox)sender;
            TodoItem item = cb.DataContext as TodoItem;
            UpdateCheckedTodoItem(item);
        }


2. Nel file MainPage.xaml.cs sostituire quindi la classe **TodoItem** esistente con la definizione seguente, che include la nuova proprietà **CompleteDate** come tipo nullable.

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
	
    >[AZURE.NOTE] L'attributo <code>DataMemberAttribute</code> comunica al client di eseguire il mapping della nuova proprietà <code>CompleteDate</code> dell'app alla colonna <code>CompleteDate</code> definita nella tabella TodoItem. Grazie a questo attributo, i nomi di proprietà sugli oggetti dell'app possono essere diversi dai nomi di colonna in database SQL.
    

	


4. Nel file MainPage.xaml.cs rimuovere o impostare come commento la funzione della clausola `.Where` nel metodo **RefreshTodoItems** esistente in modo da includere nei risultati gli elementi todo completati.

            // This query filters out completed TodoItems and 
            // items without a timestamp. 
            items = await todoTable
               //.Where(todoItem => todoItem.Complete == false)
               .ToCollectionAsync();


5. In MainPage.xaml.cs aggiornare il metodo **UpdateCheckedTodoItem** come segue, in modo che gli elementi vengano aggiornati dopo un aggiornamento e gli elementi completati non vengano rimossi dall'elenco. Salvare il file.	

        private async void UpdateCheckedTodoItem(TodoItem item)
        {
            // This code takes a freshly completed TodoItem and updates the database.
            await todoTable.UpdateAsync(item);
            //ite
	ms.Remove(item);
            RefreshTodoItems();
        }


6. Nella finestra Esplora soluzioni di Visual Studio fare clic con il pulsante destro del mouse su **Soluzione**, quindi scegliere **Ricompila soluzione** per ricompilare sia il client che il servizio back-end .NET. Verificare che entrambi i progetti vengano compilati senza errori.

    ![][3]
	
7. Premere **F5** per eseguire l'app client e il servizio in locale. Aggiungere alcuni nuovi elementi e selezionarne alcuni per contrassegnarli come completati modo da visualizzare l'aggiornamento del timestamp di **CompleteDate**.

    ![][4]

8. In Esplora soluzioni di Visual Studio fare clic con il pulsante destro del mouse sul progetto del servizio todolist, quindi scegliere **Pubblica**. Pubblicare il servizio back-end .NET in Microsoft Azure usando il file delle impostazioni di pubblicazione che è stato scaricato dal portale di Azure.

9. Aggiornare il file App.xaml.cs file per il progetto client rimuovendo i simboli di commento dalla connessione all'indirizzo del servizio mobile. Testare l'app con il servizio back-end .NET ospitato nell'account Azure.




## <a name="next-steps"> </a>Passaggi successivi

Dopo avere completato anche questa esercitazione, provare a svolgere l'ultima esercitazione della serie relativa ai dati: [Usare il paging per ridefinire le query].

Gli script del server vengono inoltre usati per l'autorizzazione degli utenti e per l'invio di notifiche push. Per altre informazioni, vedere le esercitazioni seguenti:

* [Autorizzazione lato servizio degli utenti]
  <br/>Informazioni su come filtrare i dati in base all'ID di un utente autenticato.

* [Introduzione alle notifiche push] 
  <br/>Informazioni sull'invio di una notifica push di base all'app.

* [Riferimento per i concetti e le procedure di .NET per Servizi mobili]
  <br/>Altre informazioni su come usare Servizi mobili con .NET.

<!-- Anchors. -->
[Aggiungere la convalida della lunghezza della stringa]: #string-length-validation
[Aggiornare il client per il supporto della convalida]: #update-client-validation
[Testare la convalida della lunghezza]: #test-length-validation
[Aggiungere un campo timestamp per CompleteDate]: #add-timestamp
[Aggiornare il client per la visualizzazione del timestamp CompleteDate]: #update-client-timestamp
[Passaggi successivi]: #next-steps

<!-- Images. -->
[1]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/mobile-services-invalid-text-length.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/mobile-services-invalid-text-length-exception-dialog.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/mobile-services-rebuild-solution.png
[4]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/mobile-services-final-local-app-run.png



<!-- URLs. -->
[Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started/#create-new-service
[Autorizzazione lato servizio degli utenti]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts/
[Usare il paging per ridefinire le query]: /it-it/develop/mobile/tutorials/add-paging-to-data-dotnet
[Per iniziare]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
[Introduzione ai dati]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
[Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/
[Introduzione alle notifiche push]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/
[JavaScript e HTML]: /it-it/develop/mobile/tutorials/validate-modify-and-augment-data-js

[Portale di gestione]: https://manage.windowsazure.com/
[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Riferimento per i concetti e le procedure di .NET per Servizi mobili]: /it-it/develop/mobile/how-to-guides/work-with-net-client-library



<!--HONumber=42-->
