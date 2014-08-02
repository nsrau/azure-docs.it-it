<properties linkid="develop-mobile-tutorials-validate-modify-and-augment-data-wp8" urlDisplayName="Validate Data" pageTitle="Use server scripts to validate data (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to validate and modify data sent using server scripts from your Windows Phone 8 app." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="glenga" solutions="" manager="" editor="" />

Convalida e modifica dei dati in Servizi mobili mediante script del server
==========================================================================

[Windows Store C\#](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet "Windows Store C#") [Windows Store JavaScript](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-js "Windows Store JavaScript") [Windows Phone](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-wp8 "Windows Phone") [iOS](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-ios "iOS") [Android](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-android "Android") [HTML](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-html "HTML") [Xamarin.iOS](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios "Xamarin.iOS") [Xamarin.Android](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android "Xamarin.Android")

[Back-end .NET](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-validate-modify-data/ "Back-end .NET") | [Back-end JavaScript](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-wp8 "Back-end JavaScript")

In questo argomento viene illustrato come utilizzare gli script del server in Servizi mobili di Azure. Gli script del server vengono registrati in un servizio mobile e possono essere utilizzati per eseguire numerose operazioni sui dati inseriti e aggiornati, incluse la convalida e la modifica dei dati. In questa esercitazione si procederà alla definizione e alla registrazione di script del server per la convalida e la modifica dei dati. Poiché il comportamento degli script sul lato server spesso influisce sul client, si procederà quindi all'aggiornamento dell'app per Windows Phone 8 per implementare questi nuovi comportamenti.

[guarda l'esercitazione](http://go.microsoft.com/fwlink/?LinkId=298629) [Riproduci video](http://go.microsoft.com/fwlink/?LinkId=298629) 11:36

In questa esercitazione vengono descritte le operazioni di base seguenti:

1.  [Aggiunta della convalida della lunghezza della stringa](#string-length-validation)
2.  [Aggiornamento del client per il supporto della convalida](#update-client-validation)
3.  [Aggiunta di un timestamp all'inserimento](#add-timestamp)
4.  [Aggiornamento del client per la visualizzazione del timestamp](#update-client-timestamp)

Questa esercitazione è basata sulle procedure e sull'app di esempio creata nell'esercitazione precedente [Introduzione ai dati](/en-us/develop/mobile/tutorials/get-started-with-data-wp8). Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione ai dati](/en-us/develop/mobile/tutorials/get-started-with-data-wp8).

Aggiunta della convalida
------------------------

È sempre consigliabile convalidare la lunghezza dei dati inviati dagli utenti. A questo scopo, è innanzitutto necessario registrare uno script che convalida la lunghezza dei dati di stringa inviati al servizio mobile e rifiuta le stringhe troppo lunghe, in questo caso lunghe più di 10 caratteri.

1.  Accedere al [portale di gestione di Azure](https://manage.windowsazure.com/), fare clic su **Mobile Services** e quindi sull'app.

    ![](./media/mobile-services-windows-phone-validate-modify-data-server-scripts/mobile-services-selection.png)

2.  Fare clic sulla scheda **Data** e quindi sulla tabella **TodoItem**.

    ![](./media/mobile-services-windows-phone-validate-modify-data-server-scripts/mobile-portal-data-tables.png)

3.  Fare clic su **Script** e quindi selezionare l'operazione **Insert**.

    ![](./media/mobile-services-windows-phone-validate-modify-data-server-scripts/mobile-insert-script-users.png)

4.  Sostituire lo script esistente con la funzione seguente e quindi fare clic su **Save**.

         function insert(item, user, request) {
             if (item.text.length > 10) {
                 request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
             } else {
                 request.execute();
             }
         }

    Questo script verifica la lunghezza della proprietà **TodoItem.text** e invia una risposta di errore quando la lunghezza supera i 10 caratteri. In caso contrario, viene chiamato il metodo **execute** per completare l'operazione di inserimento.

    **Nota**

    È possibile rimuovere uno script registrato nella scheda **Script** facendo clic su **Clear** e quindi su **Save**.

Aggiornamento del client
------------------------

Ora che il servizio mobile convalida dati e invia risposte di errore, è necessario aggiornare l'app .NET affinché sia in grado di gestire le risposte di errore dalla convalida.

1.  In Visual Studio 2012 Express per Windows Phone aprire il progetto modificato dopo avere completato l'esercitazione [Introduzione ai dati](/en-us/develop/mobile/tutorials/get-started-with-data-wp8).

2.  Premere **F5** per eseguire l'app, digitare un testo più lungo di 10 caratteri nella casella di testo, quindi fare clic su **Salva**.

   	Si noti che l'app genera un'eccezione non gestita **MobileServiceInvalidOperationException** a seguito della risposta 400 (Richiesta non valida) restituita dal servizio mobile.

3.  Aprire il file MainPage.xaml.cs e sostituire il metodo **InsertTodoItem** esistente con quanto segue:

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
                    MessageBox.Show(e.Message,
                    string.Format("{0} (HTTP {1})",
                    e.Response.ReasonPhrase,
                    (int)e.Response.StatusCode), 
                    MessageBoxButton.OK);
                }
            }

   	Questa versione del metodo include la gestione degli errori per l'eccezione **MobileServiceInvalidOperationException** che visualizza la risposta di errore in una finestra di messaggio.

Aggiunta di un timestamp
------------------------

Nelle attività precedenti si è proceduto alla convalida di un'operazione di inserimento che è accettata o rifiutata. Ora, si procederà all'aggiornamento dei dati inseriti utilizzando uno script server che aggiunge una proprietà timestamp all'oggetto prima dell'inserimento.

**Nota**

La proprietà timestamp **createdAt** illustrata in questo esempio è ora ridondante. Servizi mobili crea automaticamente una proprietà di sistema **\_\_createdAt** per ogni tabella. È possibile utilizzare questa proprietà di sistema nell'applicazione semplicemente aggiungendo il membro seguente alla classe TodoItem

``` {}
[JsonProperty(PropertyName = "__createdAt")]
public DateTime createdAt { set; get; }
```

1.  Nella scheda **Scripts** del [portale di gestione](https://manage.windowsazure.com/) sostituire lo script **Insert** corrente con la funzione seguente, quindi fare clic su **Save**.

         function insert(item, user, request) {
             if (item.text.length > 10) {
                 request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
             } else {
                 item.createdAt = new Date();
                 request.execute();
             }
         }

    Questa funzione aumenta lo script insert precedente tramite l'aggiunta di una nuova proprietà timestamp **createdAt** all'oggetto prima che questo venga inserito dalla chiamata al metodo **request**.**execute**.

    **Nota**

    È necessario abilitare lo schema dinamico la prima volta che viene eseguito lo script insert. Quando è abilitato lo schema dinamico, Servizi mobili aggiunge automaticamente la colonna **createdAt** alla tabella **TodoItem** alla prima esecuzione. Lo schema dinamico è abilitato per impostazione predefinita per un nuovo servizio mobile e deve essere disabilitato prima che l'app venga pubblicata in Windows Phone Store.

2.  In Visual Studio premere **F5** per eseguire l'app, digitare un testo più breve di 10 caratteri nella casella di testo, quindi fare clic su **Salva**.

   	Si noti che il nuovo timestamp non viene visualizzato nell'interfaccia utente dell'app.

3.  Tornare al portale di gestione e fare clic sulla scheda **Browse** nella tabella **todoitem**.

   	Verrà visualizzata una colonna **createdAt** che contiene un valore timestamp per il nuovo elemento inserito.

È ora necessario aggiornare l'app per Windows Phone affinché visualizzi questa nuova colonna.

Nuovo aggiornamento del client
------------------------------

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

    **Nota**

    L'attributo **DataMemberAttribute** comunica al client di eseguire il mapping della proprietà **CreatedAt** nell'app alla colonna **createdAt** definita nella tabella TodoItem che presenta una convenzione per l'utilizzo di maiuscole e minuscole diversa. Grazie a questo attributo, i nomi di proprietà sugli oggetti dell'app possono essere diversi dai nomi di colonna in database SQL. Senza questo attributo, viene generato un errore dovuto alle differenze di convenzione per l'utilizzo di maiuscole e minuscole.

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

Passaggi successivi
-------------------

Dopo avere completato anche questa esercitazione, provare a svolgere l'ultima esercitazione della serie relativa ai dati:

Gli script server vengono inoltre utilizzati per l'autorizzazione degli utenti e per l'invio di notifiche push. Per ulteriori informazioni, vedere le esercitazioni seguenti: [Utilizzo del paging per ridefinire le query](/en-us/develop/mobile/tutorials/add-paging-to-data-wp8).

-   [Autorizzazione di utenti con script](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-wp8)
  <br/>Informazioni su come filtrare i dati in base all'ID di un utente autenticato.

-   [Introduzione alle notifiche push](/en-us/develop/mobile/tutorials/get-started-with-push-wp8)
  <br/>Informazioni sull'invio di una notifica push di base all'app.

-   [Riferimento per gli script del server di Servizi mobili](http://go.microsoft.com/fwlink/?LinkId=262293)
  <br/>Ulteriori informazioni sulla registrazione e l'utilizzo di script del server.


