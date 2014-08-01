<properties linkid="develop-mobile-tutorials-get-started-with-data-dotnet-vs2013" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Store app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

Introduzione ai dati in Servizi mobili
======================================

[Windows Store C\#](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet "Windows Store C#")[Windows Store JavaScript](/en-us/develop/mobile/tutorials/get-started-with-data-js "Windows Store JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-data-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-data-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-data-android "Android")[HTML](/en-us/develop/mobile/tutorials/get-started-with-data-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-android "Xamarin.Android")

[Back-end .NET](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/ "Back-end .NET") | [Back-end JavaScript](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet/ "Back-end JavaScript")

In questo argomento viene illustrato come utilizzare Servizi mobili di Azure per sfruttare i dati in un'app di Windows Store. In questa esercitazione si scaricherà un progetto di Visual Studio 2013 per un'app che archivia dati in memoria, si creerà un nuovo servizio mobile e lo si integrerà con l'app e quindi si effettuerà l'accesso al portale di gestione di Azure per visualizzare le modifiche apportate ai dati durante l'esecuzione dell'app.

**Nota**

Per completare questa esercitazione, è necessario disporre di Visual Studio 2013, che facilita la connessione dell'app di Windows Store a Servizi mobili. Per completare la stessa procedura di base in Visual Studio 2012, eseguire le operazioni descritte nell'argomento [Introduzione ai dati in Servizi mobili tramite Visual Studio 2012](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012/).

In questa esercitazione vengono descritte le operazioni di base seguenti:

1.  [Download del progetto dell'app di Windows Store](#download-app)
2.  [Creazione del servizio mobile da Visual Studio](#create-service)
3.  [Aggiunta di una tabella dati per l'archiviazione e aggiornamento dell'app](#add-table)
4.  [Esecuzione del test dell'app in Servizi mobili](#test-app)

**Nota**

Per completare l'esercitazione, è necessario un account Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=AE564AB28).

Download del progettoDownload del progetto GetStartedWithData
-------------------------------------------------------------

Questa esercitazione è basata sull'[app GetStartedWithMobileServices](http://go.microsoft.com/fwlink/p/?LinkId=328660), ovvero un progetto di app di Windows Store disponibile in Visual Studio 2013. L'interfaccia utente di questa app è identica a quella dell'app generata dalla guida introduttiva di Servizi mobili, ma in questo caso gli elementi aggiunti vengono archiviati nella memoria locale.

1.  Scaricare la versione in C\# dell'app di esempio GetStartedWithMobileServices dal [sito degli esempi di codice di Developer Network](http://go.microsoft.com/fwlink/p/?LinkId=328660).

	![][10]

2.  In Visual Studio 2013 aprire il progetto scaricato ed esaminare il file MainPage.xaml.cs.

	Si noti che gli oggetti **TodoItem** aggiunti sono archiviati in un oggetto **ObservableCollection<TodoItem>** in memoria.

3.  Premere **F5** per ricompilare il progetto e avviare l'app.

4.  Nell'app digitare un testo in **Insert a TodoItem**, quindi fare clic su **Save**.

	![][0]  
	
	Si noti che il testo salvato viene visualizzato nella seconda colonna sotto **Query and Update Data**.

Creazione del servizio mobileCreazione di un nuovo servizio mobile da Visual Studio
-----------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service-vs2013](../includes/mobile-services-create-new-service-vs2013.md)]

1.  In Esplora soluzioni aprire il file di codice App.xaml.cs e notare il nuovo campo static aggiunto alla classe \*\*App\*\*, simile all'esempio seguente:

    ``` {data-morhtml="true"}
    public static Microsoft.WindowsAzure.MobileServices.MobileServiceClient 
                todolistClient = new Microsoft.WindowsAzure.MobileServices.MobileServiceClient(
                    "https://todolist.azure-mobile.net/",
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");
            
    ```

    Questo codice consente di accedere al nuovo servizio mobile nell'app tramite un'istanza della [classe MobileServiceClient](http://go.microsoft.com/fwlink/p/?LinkId=302030). Per creare il client, è necessario specificare l'URI e la chiave dell'applicazione del nuovo servizio mobile. Questo campo static è disponibile in tutte le pagine dell'app.

Aggiunta di una nuova tabellaAggiunta di una nuova tabella al servizio mobile e aggiornamento dell'app
------------------------------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-table-vs2013](../includes/mobile-services-create-new-table-vs2013.md)]

1.  Nel file MainPage.xaml.cs aggiungere o rimuovere i simboli di commento dalle istruzioni using seguenti:

         using Microsoft.WindowsAzure.MobileServices;
         using Newtonsoft.Json;

2.  Nello stesso file sostituire la definizione della classe TodoItem con il codice seguente:

         public class TodoItem
         {
             public string Id { get; set; }
            
             [JsonProperty(PropertyName = "text")]
             public string Text { get; set; }
            
             [JsonProperty(PropertyName = "complete")] 
             public bool Complete { get; set; }
         }

    L'**attributo JsonProperty** viene utilizzato per definire il mapping tra nomi di proprietà nel tipo di client e i nomi di colonna nella tabella dati sottostante.

3.  Impostare come commento la riga in cui viene definita la raccolta di elementi esistenti, quindi rimuovere i simboli di commento o aggiungere le righe seguenti e sostituire *<yourClient\>* con il campo `MobileServiceClient` aggiunto al file App.xaml.cs durante la connessione del progetto al servizio mobile:

         private MobileServiceCollection<TodoItem data-morhtml="true", TodoItem> items;
         private IMobileServiceTable<TodoItem> todoTable = 
             App.<yourClient>.GetTable<TodoItem>();

    Questo codice consente di creare una raccolta di associazione compatibile con Servizi mobili (elementi) e una classe proxy per la tabella del database (todoTable).

4.  Nel metodo **InsertTodoItem** rimuovere la riga di codice per l'impostazione della proprietà **TodoItem.Id**, aggiungere il modificatore **async** al metodo e rimuovere i simboli di commento dalla riga di codice seguente:

         await todoTable.InsertAsync(todoItem);

    Questo codice consente di inserire un nuovo elemento nella tabella.

    **Nota**

    Le nuove tabelle create includono le colonne Id, \_\_createdAt, \_\_updatedAt, e \_\_version. Quando è abilitato lo schema dinamico, in Servizi mobili vengono generate automaticamente nuove colonne basate sull'oggetto JSON nella richiesta di inserimento o di aggiornamento. Per ulteriori informazioni, vedere [Schema dinamico](http://msdn.microsoft.com/en-us/library/windowsazure/jj193175.aspx).

5.  Nel metodo **RefreshTodoItems** aggiungere il modificatore **async** al metodo, quindi rimuovere i simboli di commento dalla riga di codice seguente:

         items = await todoTable.ToCollectionAsync();

    In questo modo verrà impostata l'associazione alla raccolta di elementi in `todoTable`, che contiene tutti gli oggetti **TodoItem** restituiti dal servizio mobile.

6.  Nel metodo **UpdateCheckedTodoItem** aggiungere il modificatore **async** al metodo, quindi rimuovere i simboli di commento dalla riga di codice seguente:

         await todoTable.UpdateAsync(item);

    In questo modo un aggiornamento dell'elemento verrà inviato al servizio mobile.

Una volta aggiornata l'app per consentire l'utilizzo di Servizi mobili per l'archiviazione back-end, è possibile verificarne il funzionamento in Servizi mobili.

Esecuzione del test dell'appEsecuzione del test dell'app nel nuovo servizio mobile
----------------------------------------------------------------------------------

1.  In Visual Studio premere F5 per eseguire l'app.

2.  Come in precedenza, digitare un testo in **Insert a TodoItem**, quindi fare clic su **Save**.

	Un nuovo elemento verrà inviato come inserimento al servizio mobile.

3.  Nel [portale di gestione](https://manage.windowsazure.com/) fare clic su **Mobile Services** e quindi sul servizio mobile.

4.  Fare clic sulla scheda **Data** e quindi su **Browse**.

	![][9]
          
	Si noti che la tabella **TodoItem** ora contiene dati, con valori ID generati da Servizi mobili, e che alla tabella sono state aggiunte automaticamente colonne per garantire la corrispondenza con la classe TodoItem nell'app.

5.  Nell'app selezionare uno degli elementi nell'elenco, quindi tornare alla scheda Browse nel portale e fare clic su **Refresh**.

	Si noti che il valore di complete è cambiato da **false** a **true**.

6.  Nel file del progetto MainPage.xaml.cs sostituire il metodo **RefreshTodoItems** esistente con il codice seguente che consente di filtrare gli elementi completati:

         private async void RefreshTodoItems()
         {                       
             // This query filters out completed TodoItems. 
             items = await todoTable
                .Where(todoItem => todoItem.Complete == false)
                .ToCollectionAsync();

             ListItems.ItemsSource = items;            
         }

7.  Nell'app selezionare un altro elemento dell'elenco e fare clic sul pulsante **Refresh**.

	Si noti che l'elemento selezionato non è più presente nell'elenco. Ogni aggiornamento comporta un round trip al servizio mobile, che ora restituisce i dati filtrati.

L'esercitazione **Introduzione ai dati** è terminata.

Passaggi successivi
-------------------

In questa esercitazione vengono illustrate le nozioni di base per consentire a un'app di Windows Store di utilizzare dati in Servizi mobili. In seguito, è consigliabile eseguire una delle esercitazioni seguenti, basate sull'app GetStartedWithData creata in questa esercitazione:

-   [Utilizzo di script per la convalida e la modifica di dati](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet)
    <br/>Ulteriori informazioni sull'utilizzo di script server in Servizi mobili per convalidare e modificare i dati inviati dall'app.

-   [Utilizzo del paging per ridefinire le query](/en-us/develop/mobile/tutorials/add-paging-to-data-dotnet)
    <br/>Ulteriori informazioni su come utilizzare il paging nelle query per controllare la quantità di dati gestiti in un'unica richiesta.

Una volta completata la serie relativa ai dati, provare a eseguire una delle esercitazioni seguenti:

-   [Introduzione all'autenticazione](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet)
    <br/>Informazioni sull'autenticazione degli utenti dell'app.

-   [Introduzione alle notifiche push](../mobile-services-windows-store-dotnet-get-started-push/)
    <br/>Informazioni sull'invio di una notifica push di base all'app.

-   [Riferimento per i concetti e le procedure di Servizi mobili con .NET](/en-us/develop/mobile/how-to-guides/work-with-net-client-library)
    <br/>Ulteriori informazioni su come utilizzare Servizi mobili con .NET.


