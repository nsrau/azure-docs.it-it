<properties 
	pageTitle="Aggiungere Servizi mobili a un'app esistente (WP8) | Servizi mobili di Azure" 
	description="Informazioni su come iniziare a usare dati dall'app di Servizi mobili di Azure per Windows Phone 8." 
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
	ms.date="05/28/2015" 
	ms.author="glenga"/>


# Aggiungere Servizi mobili a un'app esistente

[AZURE.INCLUDE [mobile-services-selector-get-started-data-legacy](../../includes/mobile-services-selector-get-started-data-legacy.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>Questo argomento descrive come usare Servizi mobili di Azure per sfruttare i dati in un'app per Windows Phone 8. In questa esercitazione si scaricherà un'app che archivia dati in memoria, si creerà un nuovo servizio mobile e lo si integrerà con l'app e quindi si effettuerà l'accesso al portale di gestione di Azure per visualizzare le modifiche apportate ai dati durante l'esecuzione dell'app.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkID=298628" target="_blank" class="label">video di esercitazione</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-get-started-data-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkID=298628" target="_blank" class="dev-onpage-video"><span class="icon">Riproduci video</span></a> <span class="time">12:54</span></div>
</div>

##Prerequisiti 

+ Visual Studio 2012 Express per Windows Phone 8 e [Windows Phone 8 SDK] in esecuzione su Windows 8. Per creare un'app per Windows Phone 8.1 mediante questa esercitazione, è necessario usare Visual Studio 2013 Update 2 o versione successiva. 

+ Un account Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Farticles%2Fdocumentation%2Fmobile-services-windows-phone-get-started-data%2F).

##<a name="download-app"></a>Scaricare il progetto GetStartedWithData

Questa esercitazione è basata sull'[app GetStartedWithData][Developer Code Samples site] che è un progetto di app per Windows Phone Silverlight 8.

1. Scaricare il progetto di app di esempio GetStartedWithData dal [sito degli esempi di codice di Developer Network]. 

	>[AZURE.NOTE]Per creare un'app per Windows Phone Silverlight 8.1, nel progetto di app per Windows Phone Silverlight 8 scaricato è sufficiente impostare il sistema operativo di destinazione su Windows Phone 8.1. Per creare un'app di Windows Phone Store, scaricare la [versione dell'app di Windows Phone Store](http://go.microsoft.com/fwlink/p/?LinkId=397372) del progetto di app di esempio GetStartedWithData.

2. In Visual Studio aprire il progetto scaricato ed esaminare il file MainPage.xaml.cs.

   	Si noti che gli oggetti **TodoItem** aggiunti sono archiviati in un oggetto **ObservableCollection&lt;TodoItem&gt;** in memoria.

3. Premere **F5** per ricompilare il progetto e avviare l'app.

4. Nell'app digitare un testo nella casella di testo e quindi fare clic sul pulsante **Save**.

   	![][0]

   	Si noti che il testo salvato è visualizzato nell'elenco riportato di seguito.

##<a name="create-service"></a>Creare un nuovo servizio mobile nel portale di gestione

[AZURE.INCLUDE [mobile-services-create-new-service-data](../../includes/mobile-services-create-new-service-data.md)]

##<a name="add-table"></a>Aggiungere una nuova tabella al servizio mobile

[AZURE.INCLUDE [mobile-services-create-new-service-data-2](../../includes/mobile-services-create-new-service-data-2.md)]

##<a name="update-app"></a>Aggiornare l'app per l'uso del servizio mobile per l'accesso ai dati

Ora che il servizio mobile è pronto, è possibile aggiornare l'app in modo che gli elementi vengano archiviati in Servizi mobili anziché nella raccolta locale.

1. In **Esplora soluzioni** in Visual Studio fare clic con il pulsante destro del mouse sul nome del progetto e quindi scegliere **Gestisci pacchetti NuGet**.

2. Nel riquadro sinistro selezionare la categoria **Online**, cercare `WindowsAzure.MobileServices`, fare clic su **Installa** nel pacchetto di **Servizi mobili di Azure**, quindi accettare il contratto di licenza.

  	![][7]

  	La libreria client per i servizi di Servizi mobili verrà aggiunta al progetto.

3. Nel portale di gestione fare clic su **Servizi mobili** e quindi sul servizio mobile appena creato.

4. Fare clic sulla scheda **Dashboard** e prendere nota del valore di **URL sito**, quindi fare clic su **Gestisci chiavi** e prendere nota del valore di **Chiave applicazione**.

   	![][8]

  	Questi valori sono necessari per accedere al servizio mobile dal codice dell'app.

5. In Visual Studio aprire il file App.xaml.cs e aggiungere l'istruzione `using` seguente o rimuovere il relativo commento:

       	using Microsoft.WindowsAzure.MobileServices;

6. Nello stesso file, rimuovere i simboli di commento seguenti dal codice che definisce la variabile **MobileService** e specificare l'URL e la chiave dell'applicazione indicati nel servizio mobile nel costruttore **MobileServiceClient**, senza modificare l'ordine.

		//public static MobileServiceClient MobileService = new MobileServiceClient( 
        //    "AppUrl", 
        //    "AppKey" 
        //); 

  	Viene creata una nuova istanza di **MobileServiceClient** usata per accedere al servizio mobile.

6. Nel file MainPage.xaml.cs aggiungere le istruzioni `using` seguenti o rimuovere il relativo commento:

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

   	Questo codice consente di creare una raccolta di binding compatibile con Servizi mobili (**items**) e una classe proxy per la tabella di database SQL **TodoItem** (**todoTable**).

7. Nel metodo **InsertTodoItem** rimuovere la riga di codice per l'impostazione della proprietà **TodoItem**.**Id**, aggiungere il modificatore **async** al metodo e rimuovere il commento dalla riga di codice seguente:

        await todoTable.InsertAsync(todoItem);

  	Questo codice consente di inserire un nuovo elemento nella tabella.

8. Nel metodo **RefreshTodoItems** aggiungere il modificatore **async** al metodo, quindi rimuovere i simboli di commento dalla riga di codice seguente:

        items = await todoTable.ToCollectionAsync();

   	In questo modo verrà impostata l'associazione alla raccolta di elementi in todoTable, che contiene tutti gli oggetti TodoItem restituiti dal servizio mobile.

9. Nel metodo **UpdateCheckedTodoItem** aggiungere il modificatore **async** al metodo, quindi rimuovere i simboli di commento dalla riga di codice seguente:

         await todoTable.UpdateAsync(item);

   	In questo modo un aggiornamento dell'elemento verrà inviato al servizio mobile.

Una volta aggiornata l'app per consentire l'utilizzo di Servizi mobili per l'archiviazione back-end, è possibile verificarne il funzionamento in Servizi mobili.

##<a name="test-app"></a>Testare l'app nel nuovo servizio mobile

1. In Visual Studio premere F5 per eseguire l'app.

2. Come in precedenza, digitare un testo nella casella di testo e quindi fare clic sul pulsante **Save**.

   	Un nuovo elemento verrà inviato come inserimento al servizio mobile.

3. Nel [portale di gestione] fare clic su **Servizi mobili** e quindi sul servizio mobile.

4. Fare clic sulla scheda **Dati** e quindi su **Sfoglia**.

   	![][9]
  
   	Si noti che la tabella **TodoItem** ora contiene dati, con valori ID generati da Servizi mobili, e che alla tabella sono state aggiunte automaticamente colonne per garantire la corrispondenza con la classe TodoItem nell'app.

L'esercitazione è terminata.

## <a name="next-steps"> </a>Passaggi successivi

Questa esercitazione ha illustrato le nozioni di base per consentire a un'app per Windows Phone 8 di usare dati in Servizi mobili. Successivamente, è consigliabile leggere uno di questi altri argomenti:

* [Aggiungere l'autenticazione all'app](mobile-services-windows-phone-get-started-users.md) <br/>Informazioni sull'autenticazione degli utenti dell'app.

* [Aggiungere notifiche push all'app](mobile-services-javascript-backend-windows-phone-get-started-push.md) <br/>Informazioni sull'invio di una notifica push di base all'app con Servizi mobili.

* [Informazioni di riferimento sui concetti e sulle procedure di Servizi mobili con C#](mobile-services-windows-dotnet-how-to-use-client-library.md) <br/>Altre informazioni sull'uso di Servizi mobili con .NET.
 
<!-- Anchors. -->
[Download the Windows Phone 8 app project]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-phone-get-started-data/mobile-quickstart-startup-wp8.png
[7]: ./media/mobile-services-windows-phone-get-started-data/mobile-add-nuget-package-wp.png
[8]: ./media/mobile-services-windows-phone-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-windows-phone-get-started-data/mobile-todoitem-data-browse.png

<!-- URLs. -->

[Azure Management Portal]: https://manage.windowsazure.com/
[portale di gestione]: https://manage.windowsazure.com/
[Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268374
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268375
[Developer Code Samples site]: http://go.microsoft.com/fwlink/p/?LinkId=271146
[sito degli esempi di codice di Developer Network]: http://go.microsoft.com/fwlink/p/?LinkId=271146
 

<!---HONumber=July15_HO3-->