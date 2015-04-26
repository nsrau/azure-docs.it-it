<properties 
	pageTitle="Introduzione ai dati (WP8) - Servizi mobili di Azure" 
	description="Informazioni su come iniziare a usare dati dall'app Servizi mobili di Azure per Windows Phone 8." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/19/2014" 
	ms.author="glenga"/>


# Aggiungere Servizi mobili a un'app esistente

[AZURE.INCLUDE [mobile-services-selector-get-started-data-legacy](../includes/mobile-services-selector-get-started-data-legacy.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>Questo argomento descrive come usare Servizi mobili di Azure per sfruttare i dati in un'app per Windows Phone 8. In questa esercitazione si scaricherà un'app che archivia dati in memoria, si creerà un nuovo servizio mobile e lo si integrerà con l'app e quindi si effettuerà l'accesso al portale di gestione di Azure per visualizzare le modifiche apportate ai dati durante l'esecuzione dell'app.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkID=298628" target="_blank" class="label">guarda l'esercitazione</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-get-started-data-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkID=298628" target="_blank" class="dev-onpage-video"><span class="icon">Riproduci video</span></a> <span class="time">12:54</span></div>
</div>

Questa esercitazione spiega come eseguire le operazioni di base seguenti:

1. [Download del progetto dell'app per Windows Phone 8] 
2. [Creazione del servizio mobile]
3. [Aggiunta di una tabella dati per l'archiviazione]
4. [Aggiornamento dell'app per l'uso di Servizi mobili]
5. [Testare l'app in Servizi mobili]

Questa esercitazione richiede Visual Studio 2012 Express per Windows Phone 8 e [Windows Phone 8 SDK] in esecuzione su Windows 8. Per creare un'app per Windows Phone 8.1 mediante questa esercitazione, è necessario usare Visual Studio 2013 Update 2 o versione successiva.

>[AZURE.NOTE]Per completare l'esercitazione, è necessario un account Azure. Se non si ha un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-it%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-wp8%2F" target="_blank">versione di valutazione gratuita di Azure</a>.

##<a name="download-app"></a>Scaricare il progetto GetStartedWithData

Questa esercitazione è basata sull'[app GetStartedWithData][sito degli esempi di codice di Developer Network], che è un progetto di app di Windows Phone Silverlight 8.  

1. Scaricare il progetto di app di esempio GetStartedWithData dal [sito degli esempi di codice di Developer Network]. 

	>[AZURE.NOTE]Per creare un'app per Windows Phone Silverlight 8.1, nel progetto di app per Windows Phone Silverlight 8 scaricato è sufficiente impostare il sistema operativo di destinazione su Windows Phone 8.1. Per creare un'app di Windows Phone Store, scaricare la [versione dell'app di Windows Phone Store](http://go.microsoft.com/fwlink/p/?LinkId=397372) del progetto di app di esempio GetStartedWithData. 

2. In Visual Studio aprire il progetto scaricato ed esaminare il file MainPage.xaml.cs.

   	Si noti che gli oggetti **TodoItem** aggiunti sono archiviati in un oggetto **ObservableCollection&lt;TodoItem&gt;** in memoria.

3. Premere **F5** per ricompilare il progetto e avviare l'app.

4. Nell'app digitare un testo nella casella di testo e quindi fare clic sul pulsante **Save**.

   	![][0]  

   	Si noti che il testo salvato è visualizzato nell'elenco riportato di seguito.

<h2><a name="create-service"></a>Creare un nuovo servizio mobile nel portale di gestione</h2>

[AZURE.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

<h2><a name="add-table"></a>Aggiungere una nuova tabella al servizio mobile</h2>

[AZURE.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

<h2><a name="update-app"></a>Aggiornare l'app per l'uso del servizio mobile per l'accesso ai dati</h2>

Ora che il servizio mobile è pronto, è possibile aggiornare l'app in modo che gli elementi vengano archiviati in Servizi mobili anziché nella raccolta locale. 

1. In **Esplora soluzioni** in Visual Studio fare clic con il pulsante destro del mouse sul nome del progetto e quindi scegliere **Gestisci pacchetti NuGet**.

2. Nel riquadro sinistro selezionare la categoria **Online**, cercare `WindowsAzure.MobileServices`, fare clic su **Installa** nel pacchetto di **Servizi mobili di Azure**, quindi accettare il contratto di licenza.

  	![][7]

  	La libreria client per i servizi di Servizi mobili verrà aggiunta al progetto.

3. Nel portale di gestione fare clic su **Servizi mobili** e quindi sul servizio mobile appena creato.

4. Fare clic sulla scheda **Dashboard** e prendere nota del valore di **URL sito**, quindi fare clic su **Gestisci chiavi** e prendere nota del valore di **Chiave applicazione**.

   	![][8]

  	Questi valori sono necessari per accedere al servizio mobile dal codice dell'app.

5. In Visual Studio aprire il file App.xaml.cs e aggiungere l'istruzione `using` seguente o rimuovere i relativi simboli di commento:

       	using Microsoft.WindowsAzure.MobileServices;

6. Nello stesso file rimuovere i simboli di commento dal codice che definisce la variabile **MobileService** e specificare l'URL e la chiave dell'applicazione del servizio mobile nel costruttore **MobileServiceClient**, senza modificare l'ordine.

		//public static MobileServiceClient MobileService = new MobileServiceClient( 
        //    "AppUrl", 
        //    "AppKey" 
        //); 

  	Verrà creata una nuova istanza di **MobileServiceClient** usata per accedere al servizio mobile.

6. Nel file MainPage.xaml.cs aggiungere le istruzioni `using` seguenti o rimuovere i relativi simboli di commento:

       	using Microsoft.WindowsAzure.MobileServices;
		using Newtonsoft.Json;

7. Nello stesso file sostituire la definizione della classe **TodoItem** con il codice seguente:

        public class TodoItem
        {
            public string Id { get; set; }

            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }

            [JsonProperty(PropertyName = "complete")]
            public bool Complete { get; set; }
        }

7. Aggiungere i simboli di commento alla riga che definisce la raccolta **items** esistente, quindi rimuovere i simboli di commento dalle righe seguenti:

        private MobileServiceCollection<TodoItem, TodoItem> items;
        private IMobileServiceTable<TodoItem> todoTable = 
			App.MobileService.GetTable<TodoItem>();

   	Questo codice crea una raccolta di binding compatibile con Servizi mobili (**items**) e una classe proxy per la tabella di database SQL **TodoItem** (**todoTable**). 

7. Nel metodo **InsertTodoItem** rimuovere la riga di codice per l'impostazione della proprietà **TodoItem**.**Id**, aggiungere il modificatore **async** al metodo e rimuovere i simboli di commento dalla riga di codice seguente:

        await todoTable.InsertAsync(todoItem);

  	Questo codice consente di inserire un nuovo elemento nella tabella.

8. Nel metodo **RefreshTodoItems** aggiungere il modificatore **async** al metodo, quindi rimuovere i simboli di commento dalla riga di codice seguente:

        items = await todoTable.ToCollectionAsync();

   	In questo modo verrà impostata l'associazione alla raccolta di elementi in todoTable, che contiene tutti gli oggetti TodoItem restituiti dal servizio mobile. 

9. Nel metodo **UpdateCheckedTodoItem** aggiungere il modificatore **async** al metodo, quindi rimuovere i simboli di commento dalla riga di codice seguente:

         await todoTable.UpdateAsync(item);

   	In questo modo un aggiornamento dell'elemento verrà inviato al servizio mobile.

Una volta aggiornata l'app per consentire l'uso di Servizi mobili per l'archiviazione back-end, è possibile verificarne il funzionamento in Servizi mobili.

<h2><a name="test-app"></a>Testare l'app nel nuovo servizio mobile</h2>

1. In Visual Studio premere F5 per eseguire l'app.

2. Come in precedenza, digitare un testo nella casella di testo e quindi fare clic sul pulsante **Save**.

   	Un nuovo elemento verrà inviato come inserimento al servizio mobile.

3. Nel [portale di gestione] fare clic su **Servizi mobili** e quindi sul servizio mobile.

4. Fare clic sulla scheda **Dati** e quindi su **Sfoglia**.

   	![][9]
  
   	Si noti che la tabella **TodoItem** ora contiene dati, con valori ID generati da Servizi mobili, e che alla tabella sono state aggiunte automaticamente colonne per garantire la corrispondenza con la classe TodoItem nell'app.

L'esercitazione **Introduzione ai dati** per Windows Phone 8 è terminata.

## <a name="next-steps"> </a>Passaggi successivi

In questa esercitazione vengono illustrate le nozioni di base per consentire a un'app per Windows Phone 8 di usare dati in Servizi mobili. In seguito, è consigliabile eseguire l'esercitazione seguente, basata sull'app GetStartedWithData creata in questa esercitazione:

* [Usare script per la convalida e la modifica di dati]
  <br/>Altre informazioni sull'uso di script del server in Servizi mobili per convalidare e modificare i dati inviati dall'app.

* [Usare il paging per ridefinire le query]
  <br/>Informazioni su come usare il paging nelle query per controllare la quantità di dati gestiti in un'unica richiesta.

Una volta completata la serie relativa ai dati, è possibile provare a eseguire una delle esercitazioni per Windows Phone 8 seguenti:

* [Introduzione all'autenticazione] 
  <br/>Informazioni sull'autenticazione degli utenti dell'app.

* [Introduzione alle notifiche push] 
  <br/>Informazioni sull'invio di una notifica push di base all'app con Servizi mobili.
 
<!-- Anchors. -->
[Download del progetto dell'app per Windows Phone 8]: #download-app
[Creazione del servizio mobile]: #create-service
[Aggiunta di una tabella dati per l'archiviazione]: #add-table
[Aggiornamento dell'app per l'uso di Servizi mobili]: #update-app
[Testare l'app in Servizi mobili]: #test-app
[Passaggi successivi]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-phone-get-started-data/mobile-quickstart-startup-wp8.png






[7]: ./media/mobile-services-windows-phone-get-started-data/mobile-add-nuget-package-wp.png
[8]: ./media/mobile-services-windows-phone-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-windows-phone-get-started-data/mobile-todoitem-data-browse.png



<!-- URLs. -->
[Usare script per la convalida e la modifica di dati]: /it-it/develop/mobile/tutorials/validate-modify-and-augment-data-wp8
[Usare il paging per ridefinire le query]: /it-it/develop/mobile/tutorials/add-paging-to-data-wp8
[Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started-wp8
[Introduzione ai dati]: /it-it/develop/mobile/tutorials/get-started-with-data-wp8
[Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-wp8
[Introduzione alle notifiche push]: /it-it/develop/mobile/tutorials/get-started-with-push-wp8

[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Portale di gestione]: https://manage.windowsazure.com/
[Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268374
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268375
[Sito degli esempi di codice di Developer Network]:  http://go.microsoft.com/fwlink/p/?LinkId=271146



<!--HONumber=42-->
