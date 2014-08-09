<properties linkid="develop-mobile-tutorials-dotnet-backend-validate-modify-and-augment-data-wp8" urlDisplayName="Validate and Modify Data" pageTitle="Use the .Net backend to validate and modify data (Windows Phone 8) | Mobile Dev Center" metaKeywords="" description="Learn how to validate, modify, and augment data for your Windows Phone app with .Net backend Windows Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using the .Net backend" authors="wesmc" solutions="" manager="" editor="" />

Convalida e modifica dei dati in Servizi mobili mediante il back-end .NET
=========================================================================

[Windows Store C\#](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/ "Windows Store C#") [Windows Store JavaScript](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/ "Windows Store JavaScript") [Windows Phone](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-validate-modify-data/ "Windows Phone") [iOS](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-ios "iOS") [Android](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-android "Android") [HTML](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios "Xamarin.iOS") [Xamarin.Android](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android "Xamarin.Android")
[Back-end .NET](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-validate-modify-data/ "Back-end .NET") | [Back-end JavaScript](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-wp8 "Back-end JavaScript")

In questo argomento viene illustrato come utilizzare il codice in Servizi mobili di Azure back-end .NET per convalidare e modificare dati. Il servizio back-end .NET è un servizio HTTP creato con il framework API Web. Se si ha familiarità con la classe `ApiController` definita nel framework API Web, la classe `TableController` fornita da Servizi mobili risulterà molto intuitiva. `TableController` deriva dalla classe `ApiController` e fornisce funzionalità aggiuntive per l'interfaccia con una tabella di database. Può essere utilizzato per eseguire numerose operazioni sui dati inseriti e aggiornati, incluse la convalida e la modifica dei dati, descritte in questa esercitazione.

In questa esercitazione vengono descritte le operazioni di base seguenti:

1.  [Aggiunta della convalida della lunghezza della stringa](#string-length-validation)
2.  [Aggiornamento del client per il supporto della convalida](#update-client-validation)
3.  [Test della convalida della lunghezza](#test-length-validation)
4.  [Aggiunta di un timestamp per CompleteDate](#add-timestamp)
5.  [Aggiornamento del client per la visualizzazione del timestamp CompleteDate](#update-client-timestamp)

Questa esercitazione è basata sulle procedure e sull'app di esempio dell'esercitazione precedente [Introduzione a Servizi mobili](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/) o [Introduzione ai dati](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/). Prima di iniziare questa esercitazione, è necessario completare l'esercitazione [Introduzione a Servizi mobili](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/) o [Introduzione ai dati](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/).

Aggiunta della convalida
------------------------

[WACOM.INCLUDE [mobile-services-dotnet-backend-add-validation](../includes/mobile-services-dotnet-backend-add-validation.md)]

Aggiornamento del client
------------------------

Ora che il servizio mobile è configurato per convalidare dati e inviare risposte di errore per una lunghezza di testo non valida, è necessario aggiornare l'app per gestire le risposte di errore risultanti dalla convalida. L'errore verrà rilevato come eccezione `MobileServiceInvalidOperationException` dalla chiamata dell'app client a `IMobileServiceTable<TodoItem].InsertAsync()`.

1.  Nella finestra Esplora soluzioni di Visual Studio, passare al progetto client e aprire il file MainPage.xaml.cs. Aggiungere l'istruzione using seguente al file.

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
             catch (MobileServiceInvalidOperationException e)
             {
                 invalidOpException = e;
             }
             if (invalidOpException != null)
             {
                 string strJsonContent = await invalidOpException.Response.Content.ReadAsStringAsync();
                 var responseContent = JObject.Parse(strJsonContent);
                 MessageBox.Show(string.Format("{0} (HTTP {1})",
                     (string)responseContent["message"], (int)invalidOpException.Response.StatusCode),
                     invalidOpException.Message, MessageBoxButton.OK);
             }
         }

    Questa versione del metodo include la gestione degli errori per l'eccezione **MobileServiceInvalidOperationException** che visualizza il messaggio di errore serializzato dal contenuto della risposta in una finestra di messaggio.

Test della convalida della lunghezza
------------------------------------

1.  In Visual Studio configurare la destinazione di distribuzione Windows Phone desiderata. Quindi, in Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto di app client e quindi scegliere **Debug**, **Avvia nuova istanza**.

2.  Immettere per un nuovo elemento Todo un testo di lunghezza superiore a 10 caratteri, quindi fare clic su **Salva**.

    ![](./media/mobile-services-dotnet-backend-windows-phone-validate-modify-data/mobile-services-invalid-text-length.png)

3.  Se si immette testo non valido verrà visualizzato un messaggio simile al seguente.

    ![](./media/mobile-services-dotnet-backend-windows-phone-validate-modify-data/mobile-services-invalid-text-length-exception-dialog.png)

Aggiunta di un campo timestamp per CompleteDate
-----------------------------------------------

[WACOM.INCLUDE [mobile-services-dotnet-backend-add-completedate](../includes/mobile-services-dotnet-backend-add-completedate.md)]

Aggiornamento del client per la visualizzazione del timestamp CompleteDate
--------------------------------------------------------------------------

Nell'ultimo passaggio si procederà all'aggiornamento del client affinché visualizzi i nuovi dati **CompleteDate**.

1.  In Esplora soluzioni di Visual Studio, nel progetto client todolist, aprire il file MainPage.xaml e sostituire l'elemento **StackPanel** con la definizione riportata di seguito. Salvare il file. Il gestore eventi su **CheckBoxComplete** verrà modificato in modo da gestire l'evento `click`. Si aggiungerà inoltre un blocco test accanto alla casella di controllo eseguendone il binding al timestamp di data completo.

         <StackPanel Orientation="Horizontal">
           <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}"
             Click="CheckBoxComplete_Clicked" Content="{Binding Text}" Margin="10,5" 
             VerticalAlignment="Center"/>
           <TextBlock Name="textCompleteDate" Text="{Binding CompleteDate}" 
             VerticalAlignment="Center" />
         </StackPanel>

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

    > [WACOM.NOTE] L'attributo `DataMemberAttribute` comunica al client di eseguire il mapping della proprietà `CompleteDate` nell'app alla colonna `CompleteDate` definita nella tabella TodoItem. Grazie a questo attributo, i nomi di proprietà sugli oggetti dell'app possono essere diversi dai nomi di colonna in database SQL.

4.  Nel file MainPage.xaml.cs, rimuovere o commentare la funzione della clausola `.Where` nel metodo **RefreshTodoItems** esistente in modo da includere gli elementi todo completati nei risultati.

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

7.  Premere **F5** per eseguire l'app client e il servizio in locale. Aggiungere alcuni nuovi elementi e selezionarne alcuni per contrassegnarli come completati modo da visualizzare l'aggiornamento del timestamp di **CompleteDate**.

8.  In Esplora soluzioni di Visual Studio fare clic con il pulsante destro del mouse sul progetto del servizio todolist, quindi scegliere **Pubblica**. Pubblicare il servizio back-end .NET in Microsoft Azure utilizzando il file delle impostazioni di pubblicazione che è stato scaricato dal portale di Azure.

9.  Aggiornare il file App.xaml.cs file per il progetto client rimuovendo i simboli di commento dalla connessione all'indirizzo del servizio mobile. Testare l'app sul servizio back-end .NET ospitato nell'account Azure.

Passaggi successivi
-------------------

Dopo avere completato anche questa esercitazione, provare a svolgere l'ultima esercitazione della serie relativa ai dati: [Utilizzo del paging per ridefinire le query](/en-us/develop/mobile/tutorials/add-paging-to-data-dotnet).

Gli script server vengono inoltre utilizzati per l'autorizzazione degli utenti e per l'invio di notifiche push. Per ulteriori informazioni, vedere le esercitazioni seguenti:

-   [Autorizzazione lato servizio degli utenti](it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts/)
    <br/>Informazioni su come filtrare i dati in base all'ID di un utente autenticato.

-   [Introduzione alle notifiche push](it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/)
    <br/>Informazioni sull'invio di una notifica push di base all'app.

-   [Riferimento per i concetti e le procedure di Servizi mobili con .NET](/en-us/develop/mobile/how-to-guides/work-with-net-client-library)
    <br/>Ulteriori informazioni su come utilizzare Servizi mobili con .NET.


<!-- Anchors. -->
[Add string length validation]: #string-length-validation
[Update the client to support validation]: #update-client-validation
[Test length validation]: #test-length-validation
[Add a timestamp for CompleteDate]: #add-timestamp
[Update the client to display the CompleteDate]: #update-client-timestamp
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/mobile-services-dotnet-backend-windows-phone-validate-modify-data/mobile-services-invalid-text-length.png
[2]: ./media/mobile-services-dotnet-backend-windows-phone-validate-modify-data/mobile-services-invalid-text-length-exception-dialog.png



<!-- URLs. -->
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/?LinkId=262293
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started/#create-new-service
[Service-side authorization of users]: it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts/
[Refine queries with paging]: /en-us/develop/mobile/tutorials/add-paging-to-data-dotnet
[Getting Started]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/
[Get started with data]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/
[Get started with authentication]: it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/
[Get started with push notifications]: it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/
[JavaScript and HTML]: /en-us/develop/mobile/tutorials/validate-modify-and-augment-data-js

[Management Portal]: https://manage.windowsazure.com/
[Windows Azure Management Portal]: https://manage.windowsazure.com/
[Mobile Services .NET How-to Conceptual Reference]: /en-us/develop/mobile/how-to-guides/work-with-net-client-library
