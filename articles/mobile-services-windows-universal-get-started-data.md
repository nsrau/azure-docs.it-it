<properties linkid="develop-mobile-tutorials-get-started-with-data-dotnet-vs2013" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Store app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services (universal Windows apps)" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Introduzione ai dati in Servizi mobili (app Windows universale)

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/" title="Windows Store C#">Windows Store C#</a><a href="/it-it/documentation/articles/mobile-services-windows-store-javascript-get-started-data/" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/it-it/documentation/articles/mobile-services-windows-phone-get-started-data/" title="Windows Phone">Windows Phone</a><a href="/it-it/documentation/articles/mobile-services-ios-get-started-data/" title="iOS">iOS</a><a href="/it-it/documentation/articles/mobile-services-android-get-started-data/" title="Android">Android</a><a href="/it-it/documentation/articles/mobile-services-html-get-started-data/" title="HTML">HTML</a><a href="/it-it/documentation/articles/partner-xamarin-mobile-services-ios-get-started-data/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/it-it/documentation/articles/partner-xamarin-mobile-services-android-get-started-data/" title="Xamarin.Android">Xamarin.Android</a></div>

<!---<div class="dev-center-tutorial-subselector"><a href="/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/" title=".NET backend">.NET backend</a> | <a href="/it-it/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/"  title="JavaScript backend" class="current">JavaScript backend</a></div>-->

In questo argomento viene illustrato come usare Servizi mobili di Azure per sfruttare i dati in un'app Windows 8.1 universale. In questa esercitazione si scaricherà un progetto di Visual Studio per un'app Windows universale che archivia dati in memoria, si creerà un nuovo servizio mobile e lo si integrerà con l'app e quindi si effettuerà l'accesso al portale di gestione di Azure per visualizzare le modifiche apportate ai dati durante l'esecuzione dell'app.

> [WACOM.NOTE] Per completare questa esercitazione, è necessario disporre di Visual Studio 2013 Update 2.

In questa esercitazione vengono descritte le operazioni di base seguenti:

1.  [Download del progetto dell'app di Windows Store][Download del progetto dell'app di Windows Store]
2.  [Creazione del servizio mobile da Visual Studio][Creazione del servizio mobile da Visual Studio]
3.  [Aggiunta di una tabella dati per l'archiviazione e aggiornamento dell'app][Aggiunta di una tabella dati per l'archiviazione e aggiornamento dell'app]
4.  [Test dell'app in Servizi mobili][Test dell'app in Servizi mobili]

> [WACOM.NOTE] Per completare l'esercitazione, è necessario un account Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][versione di valutazione gratuita di Azure].

## <a name="download-app"></a>Scaricare il progetto GetStartedWithData

Questa esercitazione è basata sull'[app GetStartedWithMobileServices][app GetStartedWithMobileServices], ovvero un progetto di app di Windows Store disponibile in Visual Studio 2013. L'interfaccia utente di questa app è identica a quella dell'app generata dalla guida introduttiva di Servizi mobili, ma in questo caso gli elementi aggiunti vengono archiviati nella memoria locale.

1.  Scaricare la versione in C# dell'app di esempio GetStartedWithMobileServices dal [sito degli esempi di codice di Developer Network][app GetStartedWithMobileServices].

    ![][0]

2.  In Visual Studio 2013 aprire il progetto scaricato ed esaminare il file MainPage.xaml.cs.

    Gli oggetti **TodoItem** aggiunti sono archiviati in un oggetto **ObservableCollection\<TodoItem\>** in memoria.

3.  Premere **F5** per ricompilare il progetto e avviare l'app.

4.  Nell'app digitare un testo in **Insert a TodoItem**, quindi fare clic su **Save**.

    ![][1]

    Il testo salvato viene visualizzato nella seconda colonna in **Esegui query e aggiorna dati**.

## <a name="create-service"></a><span class="short-header">Creazione del servizio mobile</span>Creazione di un nuovo servizio mobile da Visual Studio

[WACOM.INCLUDE [mobile-services-create-new-service-vs2013](../includes/mobile-services-create-new-service-vs2013.md)]

1.  In Esplora soluzioni aprire il file di codice App.xaml.cs e notare il nuovo campo static aggiunto alla classe **App**, simile all'esempio seguente:

        public static Microsoft.WindowsAzure.MobileServices.MobileServiceClient 
                    todolistClient = new Microsoft.WindowsAzure.MobileServices.MobileServiceClient(
                        "https://todolist.azure-mobile.net/",
                        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");
                

    Questo codice consente di accedere al nuovo servizio mobile nell'app tramite un'istanza della [classe MobileServiceClient][classe MobileServiceClient]. Per creare il client, è necessario specificare l'URI e la chiave dell'applicazione del nuovo servizio mobile. Questo campo static è disponibile in tutte le pagine dell'app.

## <a name="add-table"></a><span class="short-header">Aggiunta di una nuova tabella</span>Aggiunta di una nuova tabella al servizio mobile e aggiornamento dell'app

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

    L'**attributo JsonProperty** viene usato per definire il mapping tra nomi di proprietà nel tipo di client e i nomi di colonna nella tabella dati sottostante.

3.  Impostare come commento la riga in cui viene definita la raccolta di elementi esistenti, quindi rimuovere i commenti o aggiungere le seguenti righe e sostituire *\<yourClient\>* con il campo `MobileServiceClient` aggiunto al file App.xaml.cs durante la connessione del progetto al servizio mobile:

        private MobileServiceCollection<TodoItem, TodoItem> items;
        private IMobileServiceTable<TodoItem> todoTable = 
            App.<yourClient>.GetTable<TodoItem>();

    Questo codice consente di creare una raccolta di associazione compatibile con Servizi mobili (elementi) e una classe proxy per la tabella del database (todoTable).

4.  Nel metodo **InsertTodoItem** rimuovere la riga di codice per l'impostazione della proprietà **TodoItem.Id**, aggiungere il modificatore **async** al metodo e rimuovere i simboli di commento dalla riga di codice seguente:

        await todoTable.InsertAsync(todoItem);

    Questo codice consente di inserire un nuovo elemento nella tabella.

    <div class="dev-callout"><strong>Nota</strong><p>Le nuove tabelle create includono le colonne Id, __createdAt, __updatedAt, e __version. Quando &egrave; abilitato lo schema dinamico, in Servizi mobili vengono generate automaticamente nuove colonne basate sull'oggetto JSON nella richiesta di inserimento o di aggiornamento. Per altre informazioni, vedere <a href="http://msdn.microsoft.com/it-it/library/windowsazure/jj193175.aspx">Schema dinamico</a>.</p></div>

5.  Nel metodo **RefreshTodoItems** aggiungere il modificatore **async** al metodo, quindi rimuovere i simboli di commento dalla riga di codice seguente:

        items = await todoTable.ToCollectionAsync();

    In questo modo verrà impostata la raccolta di elementi in `todoTable`, che contiene tutti gli oggetti **TodoItem** restituiti dal servizio mobile.

6.  Nel metodo **UpdateCheckedTodoItem** aggiungere il modificatore **async** al metodo, quindi rimuovere i simboli di commento dalla riga di codice seguente:

        await todoTable.UpdateAsync(item);

    In questo modo un aggiornamento dell'elemento verrà inviato al servizio mobile.

Una volta aggiornata l'app per consentire l'utilizzo di Servizi mobili per l'archiviazione back-end, è possibile verificarne il funzionamento in Servizi mobili.

## <a name="test-app"></a><span class="short-header">Test dell'app</span>Test dell'app nel nuovo servizio mobile

1.  In Visual Studio premere F5 per eseguire l'app.

2.  Come in precedenza, digitare un testo in **Insert a TodoItem**, quindi fare clic su **Save**.

    Un nuovo elemento verrà inviato come inserimento al servizio mobile.

3.  Nel [portale di gestione][portale di gestione] fare clic su **Servizi mobili** e quindi sul servizio mobile.

4.  Fare clic sulla scheda **Data** e quindi su **Browse**.

    ![][2]

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

    L'elemento selezionato non è più presente nell'elenco. Ogni aggiornamento comporta un round trip al servizio mobile, che ora restituisce i dati filtrati.

L'esercitazione **Introduzione ai dati** è terminata.

## <a name="next-steps"> </a>Passaggi successivi

In questa esercitazione vengono illustrate le nozioni di base per consentire a un'app di Windows Store di usare dati in Servizi mobili. In seguito, è consigliabile eseguire una delle esercitazioni seguenti, basate sull'app GetStartedWithData creata in questa esercitazione:

-   [Utilizzo di script per la convalida e la modifica di dati][Utilizzo di script per la convalida e la modifica di dati]

   Altre informazioni sull'utilizzo di script del server in Servizi mobili per convalidare e modificare i dati inviati dall'app.

-   [Utilizzo del paging per ridefinire le query][Utilizzo del paging per ridefinire le query]

   Altre informazioni su come usare il paging nelle query per controllare la quantità di dati gestiti in un'unica richiesta.

Una volta completata la serie relativa ai dati, provare a eseguire una delle esercitazioni seguenti:

-   [Introduzione all'autenticazione][Introduzione all'autenticazione]

   Informazioni sull'autenticazione degli utenti dell'app.

-   [Introduzione alle notifiche push][Introduzione alle notifiche push]

   Informazioni sull'invio di una notifica push di base all'app.

-   [Riferimento per i concetti e le procedure di Servizi mobili con .NET][Riferimento per i concetti e le procedure di Servizi mobili con .NET]

   Altre informazioni su come usare Servizi mobili con .NET.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->

  [Download del progetto dell'app di Windows Store]: #download-app
  [Creazione del servizio mobile da Visual Studio]: #create-service
  [Aggiunta di una tabella dati per l'archiviazione e aggiornamento dell'app]: #add-table
  [Test dell'app in Servizi mobili]: #test-app
  [versione di valutazione gratuita di Azure]: http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=AE564AB28
  [app GetStartedWithMobileServices]: http://go.microsoft.com/fwlink/p/?LinkId=328660
  [0]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-data-sample-download-dotnet-vs12.png
  [1]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-quickstart-startup.png
  [mobile-services-create-new-service-vs2013]: ../includes/mobile-services-create-new-service-vs2013.md
  [classe MobileServiceClient]: http://go.microsoft.com/fwlink/p/?LinkId=302030
  [mobile-services-create-new-table-vs2013]: ../includes/mobile-services-create-new-table-vs2013.md
  [portale di gestione]: https://manage.windowsazure.com/
  [2]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-todoitem-data-browse.png
  [Utilizzo di script per la convalida e la modifica di dati]: /it-it/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
  [Utilizzo del paging per ridefinire le query]: /it-it/develop/mobile/tutorials/add-paging-to-data-dotnet
  [Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-dotnet
  [Introduzione alle notifiche push]: ../mobile-services-windows-store-dotnet-get-started-push/
  [Riferimento per i concetti e le procedure di Servizi mobili con .NET]: /it-it/develop/mobile/how-to-guides/work-with-net-client-library
