<properties urlDisplayName="Optimistic concurrency" pageTitle="Gestire conflitti di scrittura nel database con la concorrenza ottimistica (Windows Store) | Mobile Developer Center" metaKeywords="" description="Learn how to handle database write conflicts on both the server and in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Handling database write conlicts" authors="wesmc" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# Gestione di conflitti di scrittura nel database

<div class="dev-center-tutorial-selector sublanding">
<a href="/it-it/develop/mobile/tutorials/handle-database-write-conflicts-dotnet/" title="Windows Store C#">Windows Store C#</a>
<a href="/it-it/documentation/articles/mobile-services-windows-store-javascript-handle-database-conflicts/" title="Windows Store JavaScript">Windows Store JavaScript</a>
<a href="/it-it/develop/mobile/tutorials/handle-database-write-conflicts-wp8/" title="Windows Phone" class="current">Windows Phone</a>
</div>

In questa esercitazione viene descritto come gestire i conflitti che si verificano quando due o più client scrivono nello stesso record di database in un'app di Windows Phone 8. In alcuni casi, due o più client possono scrivere modifiche sullo stesso elemento contemporaneamente. Se il conflitto non viene rilevato, l'ultima scrittura sovrascrive tutti gli aggiornamenti precedenti, anche se non si tratta del risultato desiderato. Servizi mobili offre supporto per il rilevamento e la risoluzione di tali conflitti. In questo argomento vengono illustrate le procedure per la gestione dei conflitti di scrittura nel database sia nel server che nell'applicazione.

Nel corso dell'esercitazione si aggiungeranno all'app di guida introduttiva funzionalità per la gestione dei conflitti che si verificano durante l'aggiornamento del database TodoItem. In questa esercitazione vengono descritte le operazioni di base seguenti:

1. [Aggiornare l'applicazione per consentire gli aggiornamenti]
2. [Abilitare il rilevamento dei conflitti nell'applicazione]
3. [Testare i conflitti di scrittura del database nell'applicazione]
4. [Gestione automatica della risoluzione dei conflitti tramite script del server]


Per completare questa esercitazione, è necessario disporre di:

+ Microsoft Visual Studio 2012 Express per Windows Phone 8 o versione successiva.
+ [Windows Phone 8 SDK] eseguito in Windows 8. 
+ [Account Azure]
+ Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione a Servizi mobili].
+ Pacchetto NuGet per Servizi mobili di Azure 1.1.0 o versione successiva. Per ottenere l'ultima versione, eseguire la procedura seguente:
	1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni, quindi scegliere **Gestisci pacchetti NuGet**. 

		![][13]

	2. Espandere **Online** e fare clic su **Microsoft e .NET**. Nella casella di testo di ricerca immettere **Azure Mobile Services**. Fare clic su **Installa** accanto al pacchetto NuGet **Azure Mobile Services**.

		![][14]


 

<h2><a name="uiupdate"></a>Aggiornare l'applicazione per consentire gli aggiornamenti</h2>

In questa sezione l'interfaccia utente di TodoList verrà aggiornata in modo da consentire l'aggiornamento del testo di ogni elemento in un controllo ListBox. Il controllo ListBox conterrà un controllo CheckBox e un controllo TextBox per ogni elemento presente nella tabella di database. Sarà possibile aggiornare il campo di testo di TodoItem. L'applicazione gestirà l'evento "LostFocus" del controllo TextBox per aggiornare l'elemento nel database.


1. In Visual Studio aprire il progetto TodoList scaricato nell'esercitazione [Introduzione a Servizi mobili].
2. In Esplora soluzioni di Visual Studio aprire MainPage.xaml, sostituire la definizione di `phone:LongListSelector` con il codice per ListBox riportato di seguito, quindi salvare la modifica.

		<ListBox Grid.Row="4" Grid.ColumnSpan="2" Name="ListItems">
			<ListBox.ItemTemplate>
				<DataTemplate>
					<StackPanel Orientation="Horizontal">
						<CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" Checked="CheckBoxComplete_Checked" Margin="10,5" VerticalAlignment="Center"/>
						<TextBox x:Name="ToDoText" Width="330" Text="{Binding Text, Mode=TwoWay}" AcceptsReturn="False" LostFocus="ToDoText_LostFocus"/>
					</StackPanel>
				</DataTemplate>
			</ListBox.ItemTemplate>
		</ListBox>


2. In Esplora soluzioni di Visual Studio aprire MainPage.xaml.cs e aggiungere la direttiva `using` seguente.

		using System.Threading.Tasks;


3. In Esplora soluzioni di Visual Studio aprire MainPage.xaml.cs. Aggiungere a MainPage il gestore eventi per l'evento `LostFocus` di TextBox, come illustrato di seguito.


        private async void ToDoText_LostFocus(object sender, RoutedEventArgs e)
        {
            TextBox tb = (TextBox)sender;
            TodoItem item = tb.DataContext as TodoItem;
            //let's see if the text changed
            if (item.Text != tb.Text)
            {
                item.Text = tb.Text;
                await UpdateToDoItem(item);
            }
        }

4. In MainPage.xaml.cs, add the definition for the MainPage `UpdateToDoItem()` method referenced in the event handler as shown below.

        private async Task UpdateToDoItem(TodoItem item)
        {
            try
            {
                //update at the remote table
                await todoTable.UpdateAsync(item);
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message, "Update Failed", MessageBoxButton.OK);
            }
        }

Ora, quando il controllo TextBox perde lo stato attivo, l'applicazione scrive nel database le modifiche del testo apportate a ogni elemento.

<h2><a name="enableOC"></a>Abilitare il rilevamento dei conflitti nell'applicazione</h2>

In alcuni casi, due o più client possono scrivere modifiche sullo stesso elemento contemporaneamente. Se il conflitto non viene rilevato, l'ultima scrittura sovrascrive tutti gli aggiornamenti precedenti, anche se non si tratta del risultato desiderato. Il [controllo della concorrenza ottimistica] presuppone che per ogni transazione sia possibile eseguire il commit, quindi non procede al blocco delle risorse. Prima di effettuare il commit di una transazione, il controllo della concorrenza ottimistica verifica che i dati non siano stati modificati da un'altra transazione. Se i dati sono stati modificati, verrà eseguito il rollback di tale transazione. Servizi mobili di Azure supporta il controllo della concorrenza ottimistica tenendo traccia delle modifiche apportate a ogni elemento, usando la colonna di proprietà di sistema `__version` aggiunta a ogni tabella. In questa sezione si abiliterà il rilevamento dei conflitti di scrittura nell'applicazione attraverso la proprietà di sistema `__version`. Durante i tentativi di aggiornamento, se un record è stato modificato dopo l'ultima query, l'applicazione riceverà un'eccezione `MobileServicePreconditionFailedException`. Sarà quindi possibile scegliere se eseguire il commit della modifica nel database o lasciare intatta l'ultima modifica al database. Per altre informazioni sulle proprietà di sistema relative a Servizi mobili, vedere le [proprietà di sistema].

1. In MainPage.xaml.cs aggiornare la definizione della classe **TodoItem** con il codice seguente in modo da includere la proprietà di sistema **__version** che abilita il supporto per il rilevamento dei conflitti di scrittura:

		public class TodoItem
		{
			public string Id { get; set; }            
			[JsonProperty(PropertyName = "text")]
			public string Text { get; set; }            
			[JsonProperty(PropertyName = "complete")]
			public bool Complete { get; set; }            
			[JsonProperty(PropertyName = "__version")]
			public string Version { set; get; }
		}

	<div class="dev-callout"><strong>Nota</strong>
	<p>Le applicazioni che utilizzano tabelle non tipizzate attivano la concorrenza ottimistica impostando il flag Version per SystemProperties della tabella, come mostrato di seguito.</p>
	<pre><code>//Enable optimistic concurrency by retrieving __version
todoTable.SystemProperties |= MobileServiceSystemProperties.Version;
</code></pre>
	</div>


2. Aggiungendo la proprietà `Version` alla classe `TodoItem`, in fase di aggiornamento, se un record è stato modificato dopo l'ultima query l'applicazione riceverà un'eccezione `MobileServicePreconditionFailedException`. Questa eccezione include l'ultima versione dell'elemento dal server. In MainPage.xaml.cs aggiungere il codice seguente per gestire l'eccezione nel metodo `UpdateToDoItem()`.

        private async Task UpdateToDoItem(TodoItem item)
        {
            Exception exception = null;
            try
            {
                //update at the remote table
                await todoTable.UpdateAsync(item);
            }
            catch (MobileServicePreconditionFailedException<TodoItem> writeException)
            {
                exception = writeException;
            }
            catch (Exception ex)
            {
                exception = ex;
            }
            if (exception != null)
            {
                if (exception is MobileServicePreconditionFailedException<TodoItem>)
                {
                    //conflict detected, the item has changed since the last query
                    await ResolveConflict(item, ((MobileServicePreconditionFailedException<TodoItem>)exception).Item);
                }
                else
                    MessageBox.Show(exception.Message, "Update Failed", MessageBoxButton.OK);
            }
        }


3. In MainPage.xaml.cs aggiungere la definizione per il metodo `ResolveConflict()` a cui viene fatto riferimento in `UpdateToDoItem()`. Si noti che, per risolvere il conflitto, prima di eseguire il commit della decisione dell'utente la versione dell'elemento locale viene impostata sulla versione aggiornata dal server; in caso contrario, il conflitto continuerà a verificarsi.


        private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)		
        {
            // Ask user to choose between the local text value or leaving the 
			// server's updated text value
            MessageBoxResult mbRes = MessageBox.Show(String.Format("The item has already been updated on the server.\n\n" +
                                                                   "Server value: {0} \n" +
                                                                   "Local value: {1}\n\n" +
                                                                   "Press OK to update the server with the local value.\n\n" +
                                                                   "Press CANCEL to keep the server value.", serverItem.Text, localItem.Text), 
                                                                   "CONFLICT DETECTED ", MessageBoxButton.OKCancel);
            // OK : After examining the updated text from the server, overwrite it
            //      with the changes made in this client.
            if (mbRes == MessageBoxResult.OK)
            {
                // Update the version of the item to the latest version
                // to resolve the conflict. Otherwise the exception
                // will be thrown again for the attempted update.
                localItem.Version = serverItem.Version;
                // Recursively updating just in case another conflict 
				// occurs while the user is deciding.
                await this.UpdateToDoItem(localItem);
            }
            // CANCEL : After examining the updated text from the server, leave 
			// the server item intact and refresh this client's query discarding 
			// the proposed changes.
            if (mbRes == MessageBoxResult.Cancel)
            {
                RefreshTodoItems();
            }
        }



<h2><a name="test-app"></a>Testare i conflitti di scrittura del database nell'applicazione</h2>

In questa sezione si eseguirà il test del codice che gestisce i conflitti di scrittura eseguendo l'app in due emulatori di Windows Phone 8 diversi (WVGA e WVGA 512M). Entrambe le app client tenteranno di aggiornare la proprietà `text` dello stesso elemento e verrà chiesto l'intervento dell'utente per risolvere il conflitto.


1. In Visual Studio, dalla casella a discesa, assicurarsi che come destinazione distribuzione sia selezionato **Emulator WVGA 512MB**, come illustrato nella schermata seguente.

	![][0]

2. Nel menu in Visual Studio fare clic su **COMPILA**, quindi su **Distribuisci soluzione**. Se l'emulatore non era precedentemente in esecuzione, occorreranno alcuni minuti per il caricamento del sistema operativo Windows Phone 8 da parte dell'emulatore. Nella parte inferiore della finestra di output verificare la buona riuscita della compilazione e della distribuzione dell'emulatore Windows Phone 8.

	![][2]

3. In Visual Studio modificare la casella a discesa relativa alla destinazione distribuzione in **Emulator WVGA**.

	![][1]

4. Nel menu in Visual Studio fare clic su **COMPILA**, quindi su **Distribuisci soluzione**. Nella parte inferiore della finestra di output verificare la buona riuscita della compilazione e della distribuzione dell'emulatore Windows Phone 8.

   	![][2]
  
5. Disporre i due emulatori in modo che siano affiancati durante l'esecuzione. È possibile simulare conflitti da scritture simultanee tra le app client in esecuzione negli emulatori. Scorrere da sinistra a destra in entrambi emulatori per visualizzare l'elenco delle applicazioni installate. Scorrere fino alla fine di ogni elenco e fare clic sull'app **todolist**.

	![][3]

6. Nell'emulatore sul lato sinistro aggiornare il valore `text` dell'ultimo elemento in **Test Write 1**, quindi fare clic su un'altra casella di testo in modo che il gestore eventi "LostFocus" aggiorni il database. Nella schermata seguente viene visualizzato un esempio. 

	![][4]

7. A questo punto, l'elemento corrispondente nell'emulatore sul lato destro conterrà una versione precedente e un valore text non aggiornato. Nell'emulatore a destra immettere **Test Write 2** come proprietà text. Fare quindi clic su un'altra casella di testo in modo che il gestore eventi "LostFocus" nell'emulatore a destra tenti di aggiornare il database con la proprietà version precedente.

	![][5]

8. Poiché il valore version usato nel tentativo di aggiornamento non corrisponde al valore version del server, Mobile Services SDK genera un'eccezione `MobileServicePreconditionFailedException` che consente all'app di risolvere il conflitto. Per risolvere il conflitto, è possibile fare clic su **ok** per eseguire il commit dei valori dall'app a destra. In alternativa è possibile fare clic su **cancel** per ignorare i valori nell'app a destra e lasciare invariati i valori dell'app a sinistra. 

	![][6]



<h2><a name="scriptsexample"></a>Gestione automatica della risoluzione dei conflitti tramite script del server</h2>

È possibile rilevare e risolvere i conflitti tramite script del server. Questa scelta è consigliabile quando per risolvere il conflitto è possibile usare logica di script anziché richiedere l'interazione dell'utente. In questa sezione verrà aggiunto uno script sul lato server alla tabella TodoItem dell'applicazione. La logica usata dallo script per risolvere il conflitto è la seguente:

+  Se il campo ` complete` di TodoItem è impostato su true, viene considerato completato e non sarà più possibile modificare la proprietà `text`.
+  Se il campo ` complete` è ancora impostato su false, verrà eseguito il commit dei tentativi di aggiornamento di `text`.

Di seguito è riportata la procedura per aggiungere e testare lo script di aggiornamento del server.

1. Accedere al [portale di gestione di Azure], fare clic su **Servizi mobili** e quindi sull'app. 

   	![][7]

2. Fare clic sulla scheda **Dati** e quindi sulla tabella **TodoItem**.

   	![][8]

3. Fare clic su **Script**, quindi selezionare l'operazione **Update**.

   	![][9]

4. Sostituire lo script esistente con la funzione seguente e quindi fare clic su **Salva**.

		function update(item, user, request) { 
			request.execute({ 
				conflict: function (serverRecord) {
					// Only committing changes if the item is not completed.
					if (serverRecord.complete === false) {
						//write the updated item to the table
						request.execute();
					}
					else
					{
						request.respond(statusCodes.FORBIDDEN, 'The item is already completed.');
					}
				}
			}); 
		}   
5. Modificare il testo di TodoItem per l'ultimo elemento nell'app dell'emulatore a sinistra. Fare clic su un'altra casella di testo in modo che il gestore eventi "LostFocus" aggiorni il database.

	![][4]

6. Nell'emulatore a destra immettere un valore diverso per la proprietà text dell'ultimo elemento TodoItem. Fare quindi clic su un'altra casella di testo in modo che il gestore eventi "LostFocus" nell'emulatore a destra tenti di aggiornare il database con la proprietà version precedente.

	![][5]

7. Si noti che nell'app non sono state rilevate eccezioni, in quanto lo script del server ha risolto il conflitto consentendo l'aggiornamento, poiché l'elemento non è contrassegnato come completato. Per verificare che l'aggiornamento abbia avuto esito positivo, fare clic su **Refresh** nell'app dell'emulatore a sinistra per ripetere la query sul database.

	![][10]

8. Nell'app dell'emulatore a sinistra fare clic sulla casella di controllo per completare l'ultimo elemento TodoItem.

	![][11]

9. Nell'app dell'emulatore a destra tentare di aggiornare lo stesso testo di TodoItem attivando il trigger dell'evento "LostFocus". Lo script ha risolto il conflitto rifiutando l'aggiornamento, in quanto l'elemento era già contrassegnato come completato. 

	![][12]


## <a name="next-steps"> </a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per abilitare la gestione dei conflitti di scrittura in un'app di Windows Phone 8 quando si usano i dati in Servizi mobili. In seguito, è consigliabile eseguire una delle esercitazioni seguenti della serie relativa ai dati:

* [Convalidare e modificare dati mediante script]
  <br/>Altre informazioni sull'uso di script del server in Servizi mobili per convalidare e modificare i dati inviati dall'app.

* [Usare il paging per ridefinire le query]
  <br/>Altre informazioni su come usare il paging nelle query per controllare la quantità di dati gestiti in un'unica richiesta.

Una volta completata la serie relativa ai dati, è possibile provare a eseguire una delle esercitazioni per Windows Phone 8 seguenti:

* [Introduzione all'autenticazione] 
  <br/>Informazioni sull'autenticazione degli utenti dell'app.

* [Introduzione alle notifiche push] 
  <br/>Informazioni sull'invio di una notifica push di base all'app con Servizi mobili.
 
<!-- Anchors. -->
[Aggiornare l'applicazione per consentire gli aggiornamenti]: #uiupdate
[Abilitare il rilevamento dei conflitti nell'applicazione]: #enableOC
[Testare i conflitti di scrittura del database nell'applicazione]: #test-app
[Gestione automatica della risoluzione dei conflitti tramite script del server]: #scriptsexample
[Passaggi successivi]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-EmulatorWVGA512MB.png
[1]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-EmulatorWVGA.png
[2]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-build-deploy-wp8.png
[3]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-start-apps-oc-wp8.png
[4]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-write1-wp8.png
[5]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-write2-wp8.png
[6]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-exception-wp8.png
[7]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-services-selection.png
[8]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-portal-data-tables.png
[9]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-insert-script-users.png
[10]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-insync-wp8.png
[11]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-complete-checkbox-wp8.png
[12]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-already-completed-wp8.png
[13]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-manage-nuget-packages-VS.png
[14]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-manage-nuget-packages-dialog.png


<!-- URLs. -->
[Controllo della concorrenza ottimistica]: http://go.microsoft.com/fwlink/?LinkId=330935
[Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started/#create-new-service
[Account Azure]: http://www.windowsazure.com/it-it/pricing/free-trial/
[Convalidare e modificare dati mediante script]: /it-it/develop/mobile/tutorials/validate-modify-and-augment-data-wp8
[Usare il paging per ridefinire le query]: /it-it/develop/mobile/tutorials/add-paging-to-data-wp8
[Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started-wp8
[Introduzione ai dati]: ./mobile-services-get-started-with-data-wp8.md
[Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-wp8
[Introduzione alle notifiche push]: /it-it/develop/mobile/tutorials/get-started-with-push-wp8

[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Portale di gestione]: https://manage.windowsazure.com/
[Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268374
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268375
[Sito di esempi di codice per sviluppatori]:  http://go.microsoft.com/fwlink/p/?LinkId=271146
[Proprietà di sistema]: http://go.microsoft.com/fwlink/?LinkId=331143
