<properties 
	pageTitle="Usare gli script del server per convalidare e modificare i dati (Windows Store) | Mobile Dev Center" 
	description="Informazioni su come usare script del server per convalidare, modificare e aumentare i dati per l'app per Windows Store con Servizi mobili di Azure." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/26/2014" 
	ms.author="glenga"/>

# Convalidare e modificare i dati in Servizi mobili mediante script del server

[AZURE.INCLUDE [mobile-services-selector-validate-modify-data](../includes/mobile-services-selector-validate-modify-data.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Questo argomento descrive come usare gli script del server in Servizi mobili di Azure. Gli script del server vengono registrati in un servizio mobile e possono essere usati per eseguire numerose operazioni sui dati inseriti e aggiornati, incluse la convalida e la modifica dei dati. In questa esercitazione si procederà alla definizione e alla registrazione di script del server per la convalida e la modifica dei dati. Poiché il comportamento degli script sul lato server spesso influisce sul client, si procederà quindi all'aggiornamento dell'app di Windows Store per implementare questi nuovi comportamenti.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Validate-and-Modify-Data-with-Server-Scripts-in-Windows-Azure-Mobile-Services" target="_blank" class="label">guarda l'esercitazione</a> <a style="background-image: url('/media/devcenter/mobile/videos/validate-data-windows-store-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Validate-and-Modify-Data-with-Server-Scripts-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Riproduci video</span></a> <span class="time">09:54</span></div>
</div>

Questa esercitazione spiega come eseguire le operazioni di base seguenti:

1. [Aggiungere la convalida della lunghezza della stringa]
2. [Aggiornare il client per il supporto della convalida]
3. [Aggiungere un timestamp all'inserimento]
4. [Aggiornare il client per la visualizzazione del timestamp]

Questa esercitazione è basata sulle procedure e sull'app di esempio creata nell'esercitazione precedente [Introduzione ai dati]. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione ai dati].  

## <a name="string-length-validation"></a>Aggiungere la convalida

È sempre consigliabile convalidare la lunghezza dei dati inviati dagli utenti. A questo scopo, è innanzitutto necessario registrare uno script che convalida la lunghezza dei dati di stringa inviati al servizio mobile e rifiuta le stringhe troppo lunghe, in questo caso lunghe più di 10 caratteri.

1. Accedere al [portale di gestione di Azure], fare clic su **Servizi mobili** e quindi sull'app. 

   	![][0]

2. Fare clic sulla scheda **Dati** e quindi sulla tabella **TodoItem**.

   	![][1]

3. Fare clic su **Script**, quindi selezionare l'operazione **Inserisci**.

   	![][2]

4. Sostituire lo script esistente con la funzione seguente e quindi fare clic su **Salva**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                request.execute();
            }
        }

    Questo script verifica la lunghezza della proprietà **TodoItem.text** e invia una risposta di errore quando la lunghezza supera i 10 caratteri. In caso contrario, viene chiamato il metodo **execute** per completare l'operazione di inserimento.

    > [AZURE.TIP] È possibile rimuovere uno script registrato nella scheda **Script** facendo clic su **Cancella** e quindi su **Salva**.

## <a name="update-client-validation"></a>Aggiornare il client

Ora che il servizio mobile convalida dati e invia risposte di errore, è necessario aggiornare l'app .NET affinché sia in grado di gestire le risposte di errore dalla convalida.

1. In Visual Studio 2012 Express per Windows 8 aprire il progetto modificato dopo avere completato l'esercitazione [Introduzione ai dati].

2. Premere **F5** per eseguire l'app, digitare un testo più lungo di 10 caratteri in **Insert a TodoItem**, quindi fare clic su **Save**.

   	Si noti che l'app genera un'eccezione non gestita **MobileServiceInvalidOperationException** a seguito della risposta 400 (Richiesta non valida) restituita dal servizio mobile.	

6. 	Aprire il file del progetto MainPage.xaml.cs e aggiungere l'istruzione **using** seguente:

        using Windows.UI.Popups;

7. Sostituire il metodo **InsertTodoItem** esistente con quanto segue:

        private async void InsertTodoItem(TodoItem todoItem)
        {
            // This code inserts a new TodoItem into the database.
            // When the operation completes and Mobile Services has
            // assigned an Id, the item is added to the collection.
            try
            {
                await todoTable.InsertAsync(todoItem);
                ite
	ms.Add(todoItem);
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

   	Questa versione del metodo include la gestione degli errori per l'eccezione **MobileServiceInvalidOperationException** che visualizza la risposta di errore in un popup.

## <a name="add-timestamp"></a>Aggiungere un timestamp

Nelle attività precedenti è stata eseguita la convalida di un'operazione di inserimento che è stata quindi accettata o rifiutata. Ora, si procederà all'aggiornamento dei dati inseriti usando uno script server che aggiunge una proprietà timestamp all'oggetto prima dell'inserimento.


> [AZURE.NOTE] La proprietà timestamp **createdAt** illustrata in questo esempio è ora ridondante. Servizi mobili crea automaticamente una proprietà di sistema **__createdAt** per ogni tabella. È possibile usare questa proprietà di sistema nell'applicazione semplicemente aggiungendo il membro seguente alla classe TodoItem  
> 
`````
[JsonProperty(PropertyName = "__createdAt")]
public DateTime createdAt { set; get; }
`````


1. Nella scheda **Script** del [portale di gestione] sostituire lo script **Insert** corrente con la funzione seguente, quindi fare clic su **Salva**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                item.createdAt = new Date();
                request.execute();
            }
        }

    Questa funzione estende lo script insert precedente aggiungendo una nuova proprietà timestamp **createdAt** all'oggetto prima che questo venga inserito dalla chiamata al metodo **request**.**execute**. 

    > [AZURE.IMPORTANT] È necessario abilitare lo schema dinamico la prima volta che viene eseguito lo script insert. Quando è abilitato lo schema dinamico, Servizi mobili aggiunge automaticamente la colonna **createdAt** alla tabella **TodoItem** alla prima esecuzione. Lo schema dinamico è abilitato per impostazione predefinita per un nuovo servizio mobile e deve essere disabilitato prima che l'app venga pubblicata in Windows Store.

2. In Visual Studio premere **F5** per eseguire l'app, digitare un testo più breve di 10 caratteri in **Insert a TodoItem**, quindi fare clic su **Salva**.

   	Si noti che il nuovo timestamp non viene visualizzato nell'interfaccia utente dell'app.

3. Tornare al portale di gestione e fare clic sulla scheda **Sfoglia** nella tabella **todoitem**.
   
   	È ora presente una colonna **createdAt** che contiene un valore timestamp per il nuovo elemento inserito.
  
È ora necessario aggiornare l'app di Windows Store affinché visualizzi questa nuova colonna.

## <a name="update-client-timestamp"></a>Aggiornare di nuovo il client

Il client del servizio mobile ignorerà i dati delle risposte che non è in grado di serializzare nelle proprietà del tipo definito. Nell'ultimo passaggio si procederà all'aggiornamento del client affinché visualizzi questi nuovi dati.

1. In Visual Studio aprire il file MainPage.xaml.cs e sostituire la classe **TodoItem** esistente con la definizione seguente:

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
  
    > [AZURE.NOTE] L'attributo `DataMemberAttribute` comunica al client di eseguire il mapping della nuova proprietà `CreatedAt` dell'app alla colonna `createdAt` definita nella tabella TodoItem che presenta una convenzione per l'uso di maiuscole e minuscole diversa. Grazie a questo attributo, i nomi di proprietà sugli oggetti dell'app possono essere diversi dai nomi di colonna in database SQL. Senza questo attributo, viene generato un errore dovuto alle differenze di convenzione per l'uso di maiuscole e minuscole.

5. Aggiungere l'elemento XAML seguente sotto l'elemento **CheckBoxComplete** nel file MainPage.xaml:
	      
        <TextBlock Name="WhenCreated" Text="{Binding CreatedAt}" VerticalAlignment="Center"/>

   	La nuova proprietà **CreatedAt** verrà visualizzata in una casella di testo. 
	
6. Premere **F5** per eseguire l'app. 

   	Si noti che il timestamp è visualizzato solo per gli elementi inseriti dopo l'aggiornamento dello script insert.

7. Sostituire il metodo **RefreshTodoItems** esistente con il codice seguente:

        private async void RefreshTodoItems()
        {
            // This query filters out completed TodoItems and 
            // items without a timestamp. 
            items = await todoTable
               .Where(todoItem => todoItem.Complete == false
                   && todoItem.CreatedAt != null)
               .ToCollectionAsync();

            ListIte
	ms.ItemsSource = items;
        }

   	Questo metodo aggiorna la query affinché filtri anche gli elementi che non dispongono di un valore timestamp.
	
8. Premere **F5** per eseguire l'app.

   	Si noti che tutti gli elementi creati senza un valore timestamp non vengono più visualizzati nell'interfaccia utente.

L'esercitazione relativa all'uso dei dati è stata completata.

## <a name="next-steps"> </a>Passaggi successivi

Dopo avere completato anche questa esercitazione, provare a svolgere l'ultima esercitazione della serie relativa ai dati: [Usare il paging per ridefinire le query].

Gli script del server vengono inoltre usati per l'autorizzazione degli utenti e per l'invio di notifiche push. Per altre informazioni, vedere le esercitazioni seguenti:

* [Autorizzare gli utenti con gli script]
  <br/>Informazioni su come filtrare i dati in base all'ID di un utente autenticato.

* [Introduzione alle notifiche push] 
  <br/>Informazioni sull'invio di una notifica push di base all'app.

* [Informazioni di riferimento sugli script del server di Servizi mobili]
  <br/>Altre informazioni sulla registrazione e l'uso di script del server.

* [Riferimento per i concetti e le procedure di .NET per Servizi mobili]
  <br/>Altre informazioni su come usare Servizi mobili con .NET.

<!-- Anchors. -->
[Aggiungere la convalida della lunghezza della stringa]: #string-length-validation
[Aggiornare il client per il supporto della convalida]: #update-client-validation
[Aggiungere un timestamp all'inserimento]: #add-timestamp
[Aggiornare il client per la visualizzazione del timestamp]: #update-client-timestamp
[Passaggi successivi]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/mobile-insert-script-users.png


<!-- URLs. -->
[Informazioni di riferimento sugli script del server di Servizi mobili]: http://go.microsoft.com/fwlink/?LinkId=262293
[Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started/#create-new-service
[Autorizzare gli utenti con gli script]: /it-it/develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[Usare il paging per ridefinire le query]: /it-it/develop/mobile/tutorials/add-paging-to-data-dotnet
[Introduzione ai dati]: /it-it/develop/mobile/tutorials/get-started-with-data-dotnet
[Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-dotnet
[Introduzione alle notifiche push]: /it-it/develop/mobile/tutorials/get-started-with-push-dotnet
[JavaScript e HTML]: /it-it/develop/mobile/tutorials/validate-modify-and-augment-data-js

[Portale di gestione]: https://manage.windowsazure.com/
[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Riferimento per i concetti e le procedure di .NET per Servizi mobili]: /it-it/develop/mobile/how-to-guides/work-with-net-client-library



<!--HONumber=42-->
