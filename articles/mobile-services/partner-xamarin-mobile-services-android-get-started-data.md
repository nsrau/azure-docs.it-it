<properties 
	pageTitle="Aggiungere Servizi mobili a un'app esistente (Xamarin.Android) - Servizi mobili di Azure" 
	description="Informazioni su come archiviare e accedere ai dati dall'app Servizi mobili di Azure per Xamarin.Android." 
	documentationCenter="xamarin" 
	authors="ggailey777" 
	manager="dwrede" 
	services="mobile-services" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-android" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/24/2015" 
	ms.author="ggailey777"/>

# Aggiungere Servizi mobili a un'app esistente

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data.md)] 

Questo argomento illustra come usare Servizi mobili di Azure per sfruttare i dati in un'app per Xamarin.Android. In questa esercitazione si scaricherà un'app che archivia dati in memoria, si creerà un nuovo servizio mobile e lo si integrerà con l'app e quindi si effettuerà l'accesso al portale di gestione di Azure per visualizzare le modifiche apportate ai dati durante l'esecuzione dell'app.

> [AZURE.NOTE]In questa esercitazione viene descritto come è possibile utilizzare Servizi mobili di Azure per archiviare e recuperare i dati da un'app per Xamarin.Android e vengono pertanto riproposte molte delle procedure già completate nella guida introduttiva a Servizi mobili. Se si tratta della prima esperienza con Servizi mobili, è consigliabile iniziare dall'esercitazione [Introduzione a Servizi mobili](/develop/mobile/tutorials/get-started-xamarin-android).

Questa esercitazione descrive le operazioni di base seguenti:

1. [Scaricare il progetto di app per Xamarin.Android][GitHub] 
2. [Creare il servizio mobile]
3. [Aggiungere una tabella dati per l'archiviazione]
4. [Aggiornare l'app per l'uso di Servizi mobili]
5. [Testare l'app in Servizi mobili]

> [AZURE.IMPORTANT]Per completare l'esercitazione, è necessario un account Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5"%20target="_blank).

Per completare questa esercitazione, è necessario disporre del [componente Servizi mobili di Azure], di [Xamarin.Android] e di Android SDK 4.2 o versione successiva.

> [AZURE.NOTE]Il progetto GetStartedWithData scaricato richiede Android 4.2 o versione successiva invece con Mobile Services SDK è richiesto solo Android 2.2 o versione successiva.

## <a name="download-app"></a>Scaricare il progetto GetStartedWithData

Questa esercitazione è basata sull'[app GetStartedWithData][GitHub] che è un'app per Xamarin.Android. L'interfaccia utente di questa app è identica a quella dell'app generata dalla guida introduttiva di Servizi mobili per Android, ma in questo caso gli elementi aggiunti vengono archiviati nella memoria locale.

1. Scaricare l'app di esempio `GetStartedWithData` ed estrarre i file nel computer. 

2. In Xamarin Studio fare clic su **File** e scegliere **Open**, passare alla posizione in cui è stato estratto il progetto di esempio GetStartedWithData, quindi selezionare e aprire **XamarinTodoQuickStart.Android.sln**.

3. Individuare e aprire la classe **TodoActivity**.

   	Si noti che sono presenti commenti `// TODO::` che specificano le procedure da eseguire per il funzionamento dell'app con il servizio mobile.

5. Nel menu **Run** fare clic su **Start Without Debugging**. Verrà chiesto di scegliere un emulatore o un dispositivo USB Android collegato.

	> [AZURE.IMPORTANT]È possibile eseguire il progetto utilizzando un telefono Android o l'emulatore di Android. L'esecuzione in un telefono Android richiede il download di un driver USB specifico del telefono.
	> 
	> Per eseguire il progetto nell'emulatore di Android, è necessario definire almeno un Android Virtual Device (AVD). Utilizzare AVD Manager per creare e gestire questi dispositivi.

6. Nell'app digitare un testo significativo, ad esempio _Complete the tutorial_ e quindi fare clic su **Add**.

   	![][13]

   	Si noti che il testo salvato è archiviato in una raccolta in memoria e visualizzato nell'elenco riportato di seguito.

## <a name="create-service"></a>Creare un nuovo servizio mobile nel portale di gestione

[AZURE.INCLUDE [mobile-services-create-new-service-data](../../includes/mobile-services-create-new-service-data.md)]

## <a name="add-table"></a>Aggiungere una nuova tabella al servizio mobile

Prima di poter archiviare i dati dell'app nel nuovo servizio mobile, è necessario creare una nuova tabella.

1. Nel portale di gestione fare clic su **Servizi mobili** e quindi sul servizio mobile appena creato.

2. Fare clic sulla scheda **Dati** e quindi su **+Crea**.

   	![][5]

   	Verrà visualizzata la finestra di dialogo **Crea nuova tabella**.

3. In **Nome tabella** digitare _TodoItem_, quindi fare clic sul segno di spunta.

  	![][6]

  	Verrà creata una nuova tabella di archiviazione **TodoItem** con le autorizzazioni predefinite impostate. Questo significa che qualsiasi utente dell'app potrà accedere alla tabella e modificare i dati.

    > [AZURE.NOTE]Nella Guida introduttiva di Servizi mobili viene usato lo stesso nome di tabella. Tuttavia, ogni tabella viene creata in uno schema specifico per un determinato servizio mobile. In questo modo è possibile evitare conflitti di dati quando più servizi mobili utilizzano lo stesso database.

4. Fare clic sulla nuova tabella **TodoItem** e verificare che non siano presenti righe di dati.

5. Fare clic sulla scheda **Colonne** e verificare che sia presente una sola colonna **id**, che viene creata automaticamente.

  	Questo è il requisito minimo per le tabelle in Servizi mobili.

    > [AZURE.NOTE]Se nel servizio mobile è abilitato lo schema dinamico, vengono automaticamente create nuove colonne quando al servizio mobile vengono inviati oggetti JSON mediante un'operazione di inserimento o aggiornamento.

È ora possibile utilizzare il nuovo servizio mobile come archivio dati per l'app.

## <a name="update-app"></a>Aggiornare l'app per l'uso del servizio mobile per l'accesso ai dati

Ora che il servizio mobile è pronto, è possibile aggiornare l'app in modo che gli elementi vengano archiviati in Servizi mobili anziché nella raccolta locale.

1. Se **Servizi mobili di Azure** non è già elencato nella cartella dei componenti, per accedervi fare clic con il pulsante destro del mouse su **Components**, scegliere **Get More Components** e quindi cercare **Servizi mobili di Azure**.

  	Il componente Mobile Services SDK verrà aggiunto al progetto.

2. Aprire il file **AndroidManifest.xml** e verificare la presenza della riga di autorizzazione seguente:

		<uses-permission android:name="android.permission.INTERNET" />

  	Questo consente all'app di accedere a Servizi mobili in Azure.

3. Nella finestra **Solution** aprire la classe **TodoActivity** e rimuovere i simboli di commento dalla riga di codice seguente:

		using Microsoft.WindowsAzure.MobileServices;
 
4. Si procederà quindi alla rimozione dell'elenco in memoria attualmente utilizzato dall'app in modo da poterlo sostituire con un servizio mobile. Nella classe **TodoActivity** impostare come commento la riga di codice seguente che definisce l'elenco **todoItemList** esistente.

		public List<TodoItem> todoItemList = new ArrayList<TodoItem>();

5. Dopo avere completato il passaggio precedente, il progetto indicherà la presenza di errori di compilazione. Cercare le altre tre posizioni in cui viene usata la variabile `todoItemList` e impostare come commento le sezioni indicate.

6. Ora si procederà all'aggiunta del servizio mobile. Rimuovere quindi i simboli di commento dalle righe di codice seguenti:

        private MobileServiceClient client; // Mobile Service Client references
        private IMobileServiceTable<TodoItem> todoTable; // Mobile Service Table used to access data   

7. Nel portale di gestione fare clic su **Servizi mobili** e quindi sul servizio mobile appena creato.

8. Fare clic sulla scheda **Dashboard** e prendere nota del valore di **URL sito**, quindi fare clic su **Gestisci chiavi** e prendere nota del valore di **Chiave applicazione**.

   	![][8]

  	Questi valori sono necessari per accedere al servizio mobile dal codice dell'app.

9. Nella classe **Constants** rimuovere i simboli di commento dalle variabili membro seguenti:

        public const string ApplicationURL = @"AppUrl";
        public const string ApplicationKey = @"AppKey";
        
10. Sostituire **AppUrl** e **AppKey** nelle variabili precedenti con i valori recuperati dal portale di gestione.

11. Nel metodo **OnCreate** rimuovere i simboli di commento dalle righe di codice seguenti che definiscono la variabile **MobileServiceClient**:

		// Create the Mobile Service Client instance, using the provided
		// Mobile Service URL and key
		client = new MobileServiceClient(
			Constants.ApplicationURL,
			Constants.ApplicationKey).WithFilter(filter);

		// Get the Mobile Service Table instance to use
		todoTable = client.GetTable<TodoItem>();    

  	Verrà creata una nuova istanza di MobileServiceClient utilizzata per accedere al servizio mobile. Verrà inoltre creata l'istanza di MobileServiceTable utilizzata per comunicare con l'archiviazione dati nel servizio mobile.

12. Individuare la classe ProgressFilter verso la fine del file e rimuovere i simboli di commento. Questa classe visualizza un indicatore 'loading' mentre MobileServiceClient esegue le operazioni di rete.

13. Rimuovere i simboli di commento dalle righe seguenti del metodo **CheckItem**:

		try {
			await todoTable.UpdateAsync(item);
			if (item.Complete)
				adapter.Remove(item);
		} catch (Exception e) {
			CreateAndShowDialog(e, "Error");
		}

   	Verrà quindi inviato un aggiornamento dell'elemento al servizio mobile e verranno rimossi gli elementi dall'adattatore.
    
14. Rimuovere i simboli di commento dalle righe seguenti del metodo **AddItem**:
	
		try 
		{
			// Insert the new item
			await todoTable.InsertAsync(item);

			if (!item.Complete) 
				adapter.Add(item);			
		} 
		catch (Exception e) 
		{
			CreateAndShowDialog(e, "Error");
		}   		

  	Verrà creato e inserito un nuovo elemento nella tabella nel servizio mobile remoto.

15. Rimuovere i simboli di commento dalle righe seguenti del metodo **RefreshItemsFromTableAsync**:

		try {
			// Get the items that weren't marked as completed and add them in the adapter
			var list = await todoTable.Where(item => item.Complete == false).ToListAsync ();

			adapter.Clear();

			foreach (TodoItem current in list)
				adapter.Add(current);
		} 
        catch (Exception e) 
        {
			CreateAndShowDialog(e, "Error");
		}

	Verrà interrogato il servizio mobile e verranno restituiti gli oggetti non contrassegnati come completati. Gli elementi verranno aggiunti all'adattatore per l'associazione.
		

Una volta aggiornata l'app per consentire l'utilizzo di Servizi mobili per l'archiviazione back-end, è possibile verificarne il funzionamento in Servizi mobili.

## <a name="test-app"></a>Testare l'app sul nuovo servizio mobile

1. Nel menu **Run** fare clic su **Start Without Debugging** per avviare il progetto. Verrà chiesto di selezionare un'immagine di emulatore o un dispositivo Android USB collegato.

	L'app, compilata tramite Xamarin.Android, verrà eseguita. Quest'app utilizza la libreria client per inviare una query che restituisce gli elementi per il servizio mobile.

5. Come in precedenza, digitare un testo significativo e quindi fare clic su **Add**.

   	Un nuovo elemento verrà inviato come inserimento al servizio mobile.

3. Nel [portale di gestione] fare clic su **Servizi mobili** e quindi sul servizio mobile.

4. Fare clic sulla scheda **Dati** e quindi su **Sfoglia**.

   	![][9]
  
   	Si noti che la tabella **TodoItem** ora contiene dati, con valori ID generati da Servizi mobili, e che alla tabella sono state aggiunte automaticamente colonne per garantire la corrispondenza con la classe TodoItem nell'app.

L'esercitazione **Introduzione ai dati** per Xamarin.Android è terminata.

## Download dell'esempio completato
Scaricare il [progetto di esempio completato]. Assicurarsi di aggiornare le variabili **applicationURL** e **applicationKey** con le proprie impostazioni di Azure.

## <a name="next-steps"> </a>Passaggi successivi

In questa esercitazione sono state illustrate le nozioni di base per consentire a un'app per Xamarin.Android di utilizzare dati in Servizi mobili.

In seguito, è consigliabile eseguire una delle esercitazioni seguenti, basate sull'app GetStartedWithData creata in questa esercitazione:

* [Usare script per la convalida e la modifica di dati] Informazioni sull'uso di script del server in Servizi mobili per convalidare e modificare i dati inviati dall'app.

* [Usare il paging per ridefinire le query] Informazioni su come usare il paging nelle query per controllare la quantità di dati gestiti in un'unica richiesta.

Una volta completata la serie relativa ai dati, provare a eseguire le esercitazioni per Xamarin.Android seguenti:

* [Introduzione all'autenticazione] Informazioni sull'autenticazione degli utenti dell'app.

* [Introduzione alle notifiche push] Informazioni sull'invio di una notifica push di base all'app con Servizi mobili.

<!-- Anchors. -->

[Get the Windows Store app]: #download-app
[Creare il servizio mobile]: #create-service
[Aggiungere una tabella dati per l'archiviazione]: #add-table
[Aggiornare l'app per l'uso di Servizi mobili]: #update-app
[Testare l'app in Servizi mobili]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->





[5]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-data-tab-empty.png
[6]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-create-todoitem-table.png
[8]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-dashboard-tab.png
[9]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-todoitem-data-browse.png
[13]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-quickstart-startup-android.png

<!-- URLs. TODO:: update 'Download the Android app project' download link, 'GitHub', completed project, etc. -->
[Usare script per la convalida e la modifica di dati]: /develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android
[Usare il paging per ridefinire le query]: /develop/mobile/tutorials/add-paging-to-data-xamarin-android
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-android
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-xamarin-android
[Introduzione all'autenticazione]: /develop/mobile/tutorials/get-started-with-users-xamarin-android
[Introduzione alle notifiche push]: /develop/mobile/tutorials/get-started-with-push-xamarin-android

[Azure Management Portal]: https://manage.windowsazure.com/
[portale di gestione]: https://manage.windowsazure.com/
[componente Servizi mobili di Azure]: http://components.xamarin.com/view/azure-mobile-services/
[Download the Android app project]: http://www.google.com/
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331302
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125

[progetto di esempio completato]: http://go.microsoft.com/fwlink/p/?LinkId=331302

<!--HONumber=54--> 