<properties 
	pageTitle="Gestione di conflitti di scrittura con concorrenza ottimistica (Windows Store) | Microsoft Azure" 
	description="Informazioni su come gestire conflitti di scrittura del database sia nel server che nell'applicazione per Windows Store." 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/18/2015" 
	ms.author="wesmc"/>

# Gestione di conflitti di scrittura nel database



##Panoramica

In questa esercitazione viene descritto come gestire i conflitti che si verificano quando due o più client scrivono nello stesso record di database in un'app di Windows Store. In alcuni casi, due o più client possono scrivere modifiche sullo stesso elemento contemporaneamente. Se il conflitto non viene rilevato, l'ultima scrittura sovrascrive tutti gli aggiornamenti precedenti, anche se non si tratta del risultato desiderato. Servizi mobili di Azure offre supporto per il rilevamento e la risoluzione di tali conflitti. In questo argomento vengono illustrate le procedure per la gestione dei conflitti di scrittura nel database sia nel server che nell'applicazione.

Nel corso dell'esercitazione si aggiungeranno all'app di guida introduttiva funzionalità per la gestione dei conflitti che possono verificarsi durante l'aggiornamento del database TodoItem.


##Prerequisiti

Per completare questa esercitazione, è necessario disporre di:

+ Microsoft Visual Studio 2013 o versione successiva.
+ Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione a Servizi mobili]. 
+ [Account Azure]
+ Pacchetto NuGet per Servizi mobili di Azure 1.1.0 o versione successiva. Per ottenere l'ultima versione, eseguire la procedura seguente:
	1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni, quindi scegliere **Gestisci pacchetti Nuget**. 

		![][19]

	2. Espandere **Online** e fare clic su **Microsoft and .NET**. Nella casella di testo di ricerca immettere **Azure Mobile Services**. Fare clic su **Installa** accanto al pacchetto NuGet **Azure Mobile Services**.

		![][20]


 

##Aggiornamento dell'applicazione per consentire gli aggiornamenti

In questa sezione l'interfaccia utente di TodoList verrà aggiornata in modo da consentire l'aggiornamento del testo di ogni elemento in un controllo ListBox. Il controllo ListBox conterrà un controllo CheckBox e un controllo TextBox per ogni elemento presente nella tabella di database. Sarà possibile aggiornare il campo di testo di TodoItem. L'applicazione gestirà l'evento `LostFocus` del controllo TextBox per aggiornare l'elemento nel database.


1. In Visual Studio aprire il progetto TodoList scaricato nell'esercitazione [Introduzione a Servizi mobili].
2. In Esplora soluzioni di Visual Studio aprire MainPage.xaml, sostituire la definizione di `ListView` con il codice per `ListView` seguente e quindi salvare la modifica.

		<ListView Name="ListItems" Margin="62,10,0,0" Grid.Row="1">
			<ListView.ItemTemplate>
				<DataTemplate>
					<StackPanel Orientation="Horizontal">
						<CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" Checked="CheckBoxComplete_Checked" Margin="10,5" VerticalAlignment="Center"/>
						<TextBox x:Name="ToDoText" Height="25" Width="300" Margin="10" Text="{Binding Text, Mode=TwoWay}" AcceptsReturn="False" LostFocus="ToDoText_LostFocus"/>
					</StackPanel>
				</DataTemplate>
			</ListView.ItemTemplate>
		</ListView>


4. In Esplora soluzioni di Visual Studio aprire MainPage.cs nel progetto condiviso. Aggiungere a MainPage il gestore per l'evento `LostFocus` del controllo TextBox, come illustrato di seguito.


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

4. In MainPage.cs per il progetto condiviso aggiungere la definizione per il metodo `UpdateToDoItem()` MainPage a cui viene fatto riferimento nel gestore eventi, come illustrato di seguito.

        private async Task UpdateToDoItem(TodoItem item)
        {
            Exception exception = null;			
            try
            {
                //update at the remote table
                await todoTable.UpdateAsync(item);
            }
            catch (Exception ex)
            {
                exception = ex;
            }			
            if (exception != null)
            {
                await new MessageDialog(exception.Message, "Update Failed").ShowAsync();
            }
        }

Ora, quando il controllo TextBox perde lo stato attivo, l'applicazione scrive nel database le modifiche del testo apportate a ogni elemento.

##Abilitare il rilevamento dei conflitti nell'applicazione

In alcuni casi, due o più client possono scrivere modifiche sullo stesso elemento contemporaneamente. Se il conflitto non viene rilevato, l'ultima scrittura sovrascrive tutti gli aggiornamenti precedenti, anche se non si tratta del risultato desiderato. Il [controllo della concorrenza ottimistica] presuppone che per ogni transazione sia possibile eseguire il commit, quindi non procede al blocco delle risorse. Prima di effettuare il commit di una transazione, il controllo della concorrenza ottimistica verifica che i dati non siano stati modificati da un'altra transazione. Se i dati sono stati modificati, verrà eseguito il rollback di tale transazione. Servizi mobili di Azure supporta il controllo della concorrenza ottimistica tenendo traccia delle modifiche apportate a ogni elemento tramite la colonna di proprietà di sistema `__version` aggiunta a ogni tabella. In questa sezione si abiliterà il rilevamento dei conflitti di scrittura nell'applicazione tramite la proprietà di sistema `__version`. Durante un tentativo di aggiornamento, se il record è stato modificato dopo l'ultima query, l'applicazione riceverà un'eccezione `MobileServicePreconditionFailedException`. Sarà quindi possibile scegliere se eseguire il commit della modifica nel database o lasciare intatta l'ultima modifica al database. Per ulteriori informazioni sulle proprietà di sistema relative a Servizi mobili, vedere le [proprietà di sistema].

1. Aprire TodoItem.cs nel progetto condiviso e aggiornare la definizione della classe `TodoItem` con il codice seguente per includere la proprietà di sistema `__version` che abilita il supporto per il rilevamento dei conflitti di scrittura.

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

	> [AZURE.NOTE]Le applicazioni che utilizzano tabelle non tipizzate attivano la concorrenza ottimistica impostando il flag Version per SystemProperties della tabella, come mostrato di seguito.
	>
	>````` 
	//Enable optimistic concurrency by retrieving __version
todoTable.SystemProperties |= MobileServiceSystemProperties.Version;
`````


2. Aggiungendo la proprietà `Version` alla classe `TodoItem`, se il record è stato modificato dopo l'ultima query, l'applicazione riceverà un'eccezione `MobileServicePreconditionFailedException` durante un aggiornamento. Questa eccezione include l'ultima versione dell'elemento dal server. In MainPage.cs per il progetto condiviso aggiungere il codice seguente per gestire l'eccezione nel metodo `UpdateToDoItem()`.

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
                if (exception is MobileServicePreconditionFailedException)
                {
                    //Conflict detected, the item has changed since the last query
                    //Resolve the conflict between the local and server item
                    await ResolveConflict(item, ((MobileServicePreconditionFailedException<TodoItem>) exception).Item);
                }
                else
                {
                    await new MessageDialog(exception.Message, "Update Failed").ShowAsync();
                }
            }
        }


3. In MainPage.cs aggiungere la definizione per il metodo `ResolveConflict()` a cui viene fatto riferimento in `UpdateToDoItem()`. Si noti che, per risolvere il conflitto, prima di eseguire il commit della decisione dell'utente la versione dell'elemento locale viene impostata sulla versione aggiornata dal server. In caso contrario, il conflitto continuerà a verificarsi.


        private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)
        {
            //Ask user to choose the resolution between versions
            MessageDialog msgDialog = new MessageDialog(String.Format("Server Text: "{0}" \nLocal Text: "{1}"\n", 
                                                        serverItem.Text, localItem.Text), 
                                                        "CONFLICT DETECTED - Select a resolution:");
            UICommand localBtn = new UICommand("Commit Local Text");
            UICommand ServerBtn = new UICommand("Leave Server Text");
            msgDialog.Commands.Add(localBtn);
            msgDialog.Commands.Add(ServerBtn);			
            localBtn.Invoked = async (IUICommand command) =>
            {
                // To resolve the conflict, update the version of the 
                // item being committed. Otherwise, you will keep
                // catching a MobileServicePreConditionFailedException.
                localItem.Version = serverItem.Version;				
                // Updating recursively here just in case another 
                // change happened while the user was making a decision
                await UpdateToDoItem(localItem);
            };			
            ServerBtn.Invoked = async (IUICommand command) =>
            {
				RefreshTodoItems();
            };			
            await msgDialog.ShowAsync();
        }



##Test dei conflitti di scrittura del database nell'applicazione

In questa sezione verrà creato il pacchetto di un'app di Windows Store per installare l'app in un secondo computer o in una macchina virtuale. L'app verrà quindi eseguita in entrambi i computer, generando un conflitto per testare il codice. Entrambe le istanze dell'app proveranno ad aggiornare la proprietà `text` dello stesso elemento e sarà necessario l'intervento dell'utente per risolvere il conflitto.


1. Creare un pacchetto dell'app di Windows Store da installare in un secondo computer o in una macchina virtuale. A questo scopo fare clic su **Progetto**->**Store**->**Crea pacchetti dell'applicazione** in Visual Studio.

	![][0]

2. Nella schermata Crea i pacchetti fare clic su **No** in quanto il pacchetto non verrà caricato in Windows Store. Quindi fare clic su **Avanti**.

	![][1]

3. Nella schermata Seleziona e configura pacchetti accettare le impostazioni predefinite e fare clic su **Crea**.

	![][10]

4. Nella schermata Creazione pacchetti completata fare clic sul collegamento **Percorso di output** per aprire il percorso del pacchetto.

   	![][11]

5. Copiare nel secondo computer la cartella del pacchetto, "todolist\_1.0.0.0\_AnyCPU\_Debug\_Test". In tale computer aprire la cartella del pacchetto e fare clic con il pulsante destro del mouse sullo script di PowerShell **Add-AppDevPackage.ps1**, quindi scegliere **Esegui con PowerShell** come illustrato di seguito. Attenersi alle istruzioni visualizzate per installare l'app.

	![][12]
  
5. Eseguire l'istanza 1 dell'app in Visual Studio facendo clic su **Debug**->**Avvia debug**. Nella schermata Start del secondo computer fare clic sulla freccia in giù per visualizzare le app in base al nome. Fare quindi clic sull'app **todolist** per eseguire l'istanza 2 dell'app.

	Istanza 1 dell'app ![][2]

	Istanza 2 dell'app ![][2]


6. Nell'istanza 1 dell'app aggiornare il testo dell'ultimo elemento in **Test Write 1** e quindi fare clic su un'altra casella di testo in modo che il gestore dell'evento `LostFocus` aggiorni il database. Nella schermata seguente viene visualizzato un esempio.
	
	Istanza 1 dell'app ![][3]

	Istanza 2 dell'app ![][2]

7. A questo punto, l'elemento corrispondente nell'istanza 2 contiene una versione precedente dell'elemento. In tale istanza dell'app immettere **Test Write 2** per la proprietà `text`. Fare quindi clic su un'altra casella di testo in modo che il gestore dell'evento `LostFocus` provi ad aggiornare il database con la proprietà `_version` precedente.

	Istanza 1 dell'app ![][4]

	Istanza 2 dell'app ![][5]

8. Poiché il valore `__version` usato nel tentativo di aggiornamento non corrisponde al valore `__version` del server, Mobile Services SDK genera un'eccezione `MobileServicePreconditionFailedException` che consente all'app di risolvere il conflitto. A tale scopo, è possibile fare clic su **Commit Local Text** per eseguire il commit dei valori dell'istanza 2. In alternativa, fare clic su **Leave Server Text** per rimuovere i valori nell'istanza 2 e mantenere il commit dei valori dell'istanza 1 dell'app.

	Istanza 1 dell'app ![][4]

	Istanza 2 dell'app ![][6]



##Gestione automatica della risoluzione dei conflitti tramite script del server

È possibile rilevare e risolvere i conflitti tramite script del server. Questa scelta è consigliabile quando per risolvere il conflitto è possibile utilizzare logica di script anziché richiedere l'interazione dell'utente. In questa sezione verrà aggiunto uno script sul lato server alla tabella TodoItem dell'applicazione. La logica utilizzata dallo script per risolvere il conflitto è la seguente:

+  Se il campo ` complete` di TodoItem è impostato su true, viene considerato completato e non sarà più possibile modificare la proprietà `text`.
+  Se il campo ` complete` di TodoItem è ancora impostato su false, verrà eseguito il commit dei tentativi di aggiornamento di `text`.

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
5. Eseguire l'app **todolist** in entrambi i computer. Modificare la proprietà `text` per l'ultimo TodoItem nell'istanza 2. Fare quindi clic su un'altra casella di testo in modo che il gestore dell'evento `LostFocus` aggiorni il database.

	Istanza 1 dell'app ![][4]

	Istanza 2 dell'app ![][5]

6. Nell'istanza 1 dell'app immettere un valore diverso per l'ultima proprietà text. Fare quindi clic su un'altra casella di testo in modo che il gestore dell'evento `LostFocus` provi ad aggiornare il database con una proprietà `__version` errata.

	Istanza 1 dell'app ![][13]

	Istanza 2 dell'app ![][14]

7. Si noti che nell'app non sono state rilevate eccezioni, in quanto lo script del server ha risolto il conflitto consentendo l'aggiornamento, poiché l'elemento non è contrassegnato come completato. Per verificare che l'aggiornamento abbia avuto esito positivo, fare clic su **Refresh** nell'istanza 2 per ripetere la query sul database.

	Istanza 1 dell'app ![][15]

	Istanza 2 dell'app ![][15]

8. Nell'istanza 1 fare clic sulla casella di controllo per completare l'ultimo elemento Todo.

	Istanza 1 dell'app ![][16]

	Istanza 2 dell'app ![][15]

9. Nell'istanza 2 provare ad aggiornare il testo dell'ultimo TodoItem e attivare l'evento `LostFocus`. Lo script ha risolto il conflitto rifiutando l'aggiornamento, in quanto l'elemento era già contrassegnato come completato.

	Istanza 1 dell'app ![][17]

	Istanza 2 dell'app ![][18]

##Passaggi successivi

In questa esercitazione sono state illustrate le procedure per abilitare la gestione dei conflitti di scrittura in un'app di Windows Store quando si utilizzano i dati in Servizi mobili. In seguito, è consigliabile eseguire una delle esercitazioni di Windows Store seguenti:

* [Aggiungere l'autenticazione all'app] <br/>Informazioni sull'autenticazione degli utenti dell'app.

* [Aggiungere notifiche push all'app] <br/>Informazioni sull'invio di una notifica push di base all'app con Servizi mobili.
 


<!-- Images. -->
[0]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package1.png
[1]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package2.png
[2]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1.png
[3]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1-write1.png
[4]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1-write2.png
[5]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app2-write2.png
[6]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app2-write2-conflict.png
[7]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-services-selection.png
[8]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-portal-data-tables.png
[9]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-insert-script-users.png
[10]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package3.png
[11]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package4.png
[12]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-install-app-package.png
[13]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1-write3.png
[14]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app2-write3.png
[15]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-write3.png
[16]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-checkbox.png
[17]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-2-items.png
[18]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-already-complete.png
[19]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-manage-nuget-packages-VS.png
[20]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-manage-nuget-packages-dialog.png

<!-- URLs. -->
[controllo della concorrenza ottimistica]: http://go.microsoft.com/fwlink/?LinkId=330935
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Account Azure]: http://www.windowsazure.com/pricing/free-trial/
[Validate and modify data with scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Refine queries with paging]: /develop/mobile/tutorials/add-paging-to-data-dotnet
[Introduzione a Servizi mobili]: /develop/mobile/tutorials/get-started
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-dotnet
[Aggiungere l'autenticazione all'app]: /develop/mobile/tutorials/get-started-with-users-dotnet
[Aggiungere notifiche push all'app]: /develop/mobile/tutorials/get-started-with-push-dotnet

[portale di gestione di Azure]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268374
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268375
[Developer Code Samples site]: http://go.microsoft.com/fwlink/p/?LinkId=271146
[proprietà di sistema]: http://go.microsoft.com/fwlink/?LinkId=331143
 

<!---HONumber=August15_HO8-->