<properties linkid="develop-mobile-tutorials-optimistic-concurrent-data-dotnet" urlDisplayName="Optimistic concurrency" pageTitle="Handle database write conflicts with optimistic concurrency (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to handle database write conflicts on both the server and in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Handling database write conflicts" authors="" />

Gestione di conflitti di scrittura nel database
===============================================

[Windows Store C\#](/en-us/develop/mobile/tutorials/handle-database-write-conflicts-dotnet/ "Windows Store C#") [Windows Store JavaScript](/en-us/develop/mobile/tutorials/handle-database-write-conflicts-javascript/ "Windows Store JavaScript") [Windows Phone](/en-us/develop/mobile/tutorials/handle-database-write-conflicts-wp8/ "Windows Phone")

In questa esercitazione viene descritto come gestire i conflitti che si verificano quando due o più client scrivono nello stesso record di database in un'app di Windows Store. In alcuni casi, due o più client possono scrivere modifiche sullo stesso elemento contemporaneamente. Se il conflitto non viene rilevato, l'ultima scrittura sovrascrive tutti gli aggiornamenti precedenti, anche se non si tratta del risultato desiderato. Servizi mobili di Azure offre supporto per il rilevamento e la risoluzione di tali conflitti. In questo argomento vengono illustrate le procedure per la gestione dei conflitti di scrittura nel database sia nel server che nell'applicazione.

Nel corso dell'esercitazione si aggiungeranno all'app di guida introduttiva funzionalità per la gestione dei conflitti che si verificano durante l'aggiornamento del database TodoItem. In questa esercitazione vengono descritte le operazioni di base seguenti:

1.  [Aggiornamento dell'applicazione per consentire gli aggiornamenti](#uiupdate)
2.  [Abilitazione del rilevamento dei conflitti nell'applicazione](#enableOC)
3.  [Test dei conflitti di scrittura del database nell'applicazione](#test-app)
4.  [Gestione automatica della risoluzione dei conflitti tramite script del server](#scriptsexample)

Per completare questa esercitazione, è necessario disporre di:

-   Microsoft Visual Studio 2012 Express per Windows o versione successiva.
-   Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione a Servizi mobili](/en-us/develop/mobile/tutorials/get-started).
-   [Account Azure](http://www.windowsazure.com/it-it/pricing/free-trial/)
-   Pacchetto NuGet per Servizi mobili di Azure 1.1.0 o versione successiva. Per ottenere l'ultima versione, eseguire la procedura seguente:
    1.  In Visual Studio fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni, quindi scegliere **Gestisci pacchetti Nuget**.

        ![](./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-manage-nuget-packages-VS.png)

    2.  Espandere **Online** e fare clic su **Microsoft and .NET**. Nella casella di testo di ricerca immettere **Azure Mobile Services**. Fare clic su **Installa** accanto al pacchetto NuGet **Azure Mobile Services**.

        ![](./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-manage-nuget-packages-dialog.png)

Aggiornamento dell'interfaccia utenteAggiornamento dell'applicazione per consentire gli aggiornamenti
-----------------------------------------------------------------------------------------------------

In questa sezione l'interfaccia utente di TodoList verrà aggiornata in modo da consentire l'aggiornamento del testo di ogni elemento in un controllo ListBox. Il controllo ListBox conterrà un controllo CheckBox e un controllo TextBox per ogni elemento presente nella tabella di database. Sarà possibile aggiornare il campo di testo di TodoItem. L'applicazione gestirà l'evento `LostFocus` del controllo TextBox per aggiornare l'elemento nel database.

1.  In Visual Studio aprire il progetto TodoList scaricato nell'esercitazione [Introduzione a Servizi mobili](/en-us/develop/mobile/tutorials/get-started).
2.  In Esplora soluzioni di Visual Studio aprire MainPage.xaml, sostituire la definizione di `ListView` con il codice per `ListView` illustrato di seguito e quindi salvare la modifica.

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

3.  In MainPage.xaml.cs aggiungere la direttiva `using` nella parte superiore della pagina.

         using System.Threading.Tasks;

4.  In Esplora soluzioni di Visual Studio aprire MainPage.xaml.cs. Aggiungere a MainPage il gestore eventi per l'evento `LostFocus` di TextBox, come illustrato di seguito.

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

5.  In MainPage.xaml.cs aggiungere la definizione per il metodo `UpdateToDoItem()` di MainPage a cui viene fatto riferimento nel gestore eventi, come illustrato di seguito.

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

Abilitazione della concorrenza ottimisticaAbilitazione del rilevamento dei conflitti nell'applicazione
------------------------------------------------------------------------------------------------------

In alcuni casi, due o più client possono scrivere modifiche sullo stesso elemento contemporaneamente. Se il conflitto non viene rilevato, l'ultima scrittura sovrascrive tutti gli aggiornamenti precedenti, anche se non si tratta del risultato desiderato. Il [controllo della concorrenza ottimistica](http://go.microsoft.com/fwlink/?LinkId=330935) presuppone che per ogni transazione sia possibile eseguire il commit, quindi non procede al blocco delle risorse. Prima di effettuare il commit di una transazione, il controllo della concorrenza ottimistica verifica che i dati non siano stati modificati da un'altra transazione. Se i dati sono stati modificati, verrà eseguito il rollback di tale transazione. Servizi mobili di Azure supporta il controllo della concorrenza ottimistica tenendo traccia delle modifiche apportate a ogni elemento, utilizzando la colonna di proprietà di sistema `__version` aggiunta a ogni tabella. In questa sezione si abiliterà il rilevamento dei conflitti di scrittura nell'applicazione attraverso la proprietà di sistema `__version`. Durante i tentativi di aggiornamento, se un record è stato modificato dopo l'ultima query, l'applicazione riceverà un'eccezione `MobileServicePreconditionFailedException`. Sarà quindi possibile scegliere se eseguire il commit della modifica nel database o lasciare intatta l'ultima modifica al database. Per ulteriori informazioni sulle proprietà di sistema relative a Servizi mobili, vedere le [proprietà di sistema](http://go.microsoft.com/fwlink/?LinkId=331143).

1.  In MainPage.xaml.cs aggiornare la definizione della classe **TodoItem** con il codice seguente in modo da includere la proprietà di sistema **\_\_version**, che abilita il supporto per il rilevamento dei conflitti di scrittura.

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

    **Nota**

    Le applicazioni che utilizzano tabelle non tipizzate attivano la concorrenza ottimistica impostando il flag Version per SystemProperties della tabella, come mostrato di seguito.

    ``` {}
    //Enable optimistic concurrency by retrieving __version
    todoTable.SystemProperties |= MobileServiceSystemProperties.Version;
    ```

2.  Aggiungendo la proprietà `Version` alla classe `TodoItem`, in fase di aggiornamento, se un record è stato modificato dopo l'ultima query l'applicazione riceverà un'eccezione `MobileServicePreconditionFailedException`. Questa eccezione include l'ultima versione dell'elemento dal server. In MainPage.xaml.cs aggiungere il codice seguente per gestire l'eccezione nel metodo `UpdateToDoItem()`.

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

3.  In MainPage.xaml.cs aggiungere la definizione per il metodo `ResolveConflict()` a cui viene fatto riferimento in `UpdateToDoItem()`. Si noti che, per risolvere il conflitto, prima di eseguire il commit della decisione dell'utente la versione dell'elemento locale viene impostata sulla versione aggiornata dal server. In caso contrario, il conflitto continuerà a verificarsi.

        private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)
        {
            //Ask user to choose the resolution between versions
            MessageDialog msgDialog = new MessageDialog(String.Format("Server Text: \"{0}\" \nLocal Text: \"{1}\"\n", 
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

Test dell'appTest dei conflitti di scrittura del database nell'applicazione
---------------------------------------------------------------------------

In questa sezione verrà creato il pacchetto di un'app di Windows Store per installare l'app in un secondo computer o in una macchina virtuale. L'app verrà quindi eseguita in entrambi i computer, generando un conflitto per testare il codice. Entrambe le istanze dell'app tenteranno di aggiornare la proprietà `text` dello stesso elemento e verrà chiesto l'intervento dell'utente per risolvere il conflitto.

1.  Creare un pacchetto dell'app di Windows Store da installare in un secondo computer o in una macchina virtuale. A questo scopo fare clic su **Progetto**-\>**Store**-\>**Crea pacchetti dell'applicazione** in Visual Studio.

    ![](./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package1.png)

2.  Nella schermata Crea i pacchetti fare clic su **No** in quanto il pacchetto non verrà caricato in Windows Store. Fare quindi clic su **Avanti**.

    ![](./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package2.png)

3.  Nella schermata Seleziona e configura pacchetti accettare le impostazioni predefinite e fare clic su **Crea**.

    ![](./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package3.png)

4.  Nella schermata Creazione pacchetti completata fare clic sul collegamento **Percorso di output** per aprire il percorso del pacchetto.

   	![][11]

5.  Copiare nel secondo computer la cartella del pacchetto, "todolist\_1.0.0.0\_AnyCPU\_Debug\_Test". In tale computer aprire la cartella del pacchetto e fare clic con il pulsante destro del mouse sullo script di PowerShell **Add-AppDevPackage.ps1**, quindi scegliere **Esegui con PowerShell** come illustrato di seguito. Attenersi alle istruzioni visualizzate per installare l'app.

    ![](./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-install-app-package.png)

6.  Eseguire l'istanza 1 dell'app in Visual Studio facendo clic su **Debug**-\>**Avvia debug**. Nella schermata Start del secondo computer fare clic sulla freccia in giù per visualizzare le app in base al nome. Fare quindi clic sull'app **todolist** per eseguire l'istanza 2 dell'app.

	Istanza 1 dell'app	
	![](./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1.png)

    Istanza 2 dell'app	
    ![](./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1.png)

7.  Nell'istanza 1 dell'app aggiornare il testo dell'ultimo elemento in **Test Write 1**, quindi fare clic su un'altra casella di testo in modo che il gestore dell'evento `LostFocus` aggiorni il database. Nella schermata seguente viene visualizzato un esempio.

    Istanza 1 dell'app	
    ![](./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1-write1.png)

    Istanza 2 dell'app	
    ![](./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1.png)

8.  A questo punto, l'elemento corrispondente nell'istanza 2 contiene una versione precedente dell'elemento. In tale istanza dell'app immettere **Test Write 2** per la proprietà `text`. Fare quindi clic su un'altra casella di testo in modo che il gestore dell'evento `LostFocus` tenti di aggiornare il database con la proprietà `_version` precedente.

    Istanza 1 dell'app	
    ![](./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1-write2.png)

    Istanza 2 dell'app	
    ![](./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app2-write2.png)

9.  Poiché il valore `__version` utilizzato nel tentativo di aggiornamento non corrisponde al valore `__version` del server, Mobile Services SDK genera un'eccezione `MobileServicePreconditionFailedException` che consente all'app di risolvere il conflitto. Per risolvere il conflitto è possibile fare clic su **Commit Local Text** per eseguire il commit dei valori dell'istanza 2. In alternativa, fare clic su **Leave Server Text** per rimuovere i valori nell'istanza 2 e mantenere il commit dei valori dell'istanza 1 dell'app.

    Istanza 1 dell'app	
    ![](./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1-write2.png)

    Istanza 2 dell'app	
    ![](./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app2-write2-conflict.png)

Gestione dei conflitti tramite scriptGestione automatica della risoluzione dei conflitti tramite script del server
------------------------------------------------------------------------------------------------------------------

È possibile rilevare e risolvere i conflitti tramite script del server. Questa scelta è consigliabile quando per risolvere il conflitto è possibile utilizzare logica di script anziché richiedere l'interazione dell'utente. In questa sezione verrà aggiunto uno script sul lato server alla tabella TodoItem dell'applicazione. La logica utilizzata dallo script per risolvere il conflitto è la seguente:

-   Se il campo ` complete` di TodoItem è impostato su true, viene considerato completato e non sarà più possibile modificare la proprietà `text`.
-   Se il campo ` complete` di TodoItem è ancora impostato su false, verrà eseguito il commit dei tentativi di aggiornamento di `text`.

Di seguito è riportata la procedura per aggiungere e testare lo script di aggiornamento del server.

1.  Accedere al [portale di gestione di Azure](https://manage.windowsazure.com/), fare clic su **Mobile Services** e quindi sull'app.

   	![][7]

2.  Fare clic sulla scheda **Data** e quindi sulla tabella **TodoItem**.

   	![][8]

3.  Fare clic su **Script**, quindi selezionare l'operazione **Update**.

   	![][9]

4.  Sostituire lo script esistente con la funzione seguente e quindi fare clic su **Save**.

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

5.  Eseguire l'app **todolist** in entrambi i computer. Modificare la proprietà `text` dell'ultimo elemento nell'istanza 2 e quindi fare clic su un'altra casella di testo in modo che il gestore dell'evento `LostFocus` aggiorni il database.

    Istanza 1 dell'app	
   ![](./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1-write2.png)

    Istanza 2 dell'app	
    ![](./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app2-write2.png)

6.  Nell'istanza 1 dell'app immettere un valore diverso per l'ultima proprietà text. Fare quindi clic su un'altra casella di testo in modo che il gestore dell'evento `LostFocus` tenti di aggiornare il database con una proprietà `_version` errata.

    Istanza 1 dell'app	
    ![](./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1-write3.png)

    Istanza 2 dell'app	
   ![](./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app2-write3.png)

7.  Si noti che nell'app non sono state rilevate eccezioni, in quanto lo script del server ha risolto il conflitto consentendo l'aggiornamento, poiché l'elemento non è contrassegnato come completato. Per verificare che l'aggiornamento abbia avuto esito positivo, fare clic su **Refresh** nell'istanza 2 per ripetere la query sul database.

    Istanza 1 dell'app	
    ![](./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-write3.png)

    Istanza 2 dell'app	
    ![](./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-write3.png)

8.  Nell'istanza 1 fare clic sulla casella di controllo per completare l'ultimo elemento Todo.

    Istanza 1 dell'app	
    ![](./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-checkbox.png)

    Istanza 2 dell'app	
    ![](./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-write3.png)

9.  Nell'istanza 2 provare ad aggiornare il testo dell'ultimo TodoItem e attivare l'evento `LostFocus`. Lo script ha risolto il conflitto rifiutando l'aggiornamento, in quanto l'elemento era già contrassegnato come completato.

    Istanza 1 dell'app	
    ![](./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-2-items.png)

    Istanza 2 dell'app	
    ![](./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-already-complete.png)

Passaggi successivi
-------------------

In questa esercitazione sono state illustrate le procedure per abilitare la gestione dei conflitti di scrittura in un'app di Windows Store quando si utilizzano i dati in Servizi mobili. In seguito, è consigliabile eseguire una delle esercitazioni seguenti della serie relativa ai dati:

-   [Utilizzo di script per la convalida e la modifica di dati](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet)
    Ulteriori informazioni sull'utilizzo di script del server in Servizi mobili per convalidare e modificare i dati inviati dall'app.

-   [Utilizzo del paging per ridefinire le query](/en-us/develop/mobile/tutorials/add-paging-to-data-dotnet)
    Ulteriori informazioni su come utilizzare il paging nelle query per controllare la quantità di dati gestiti in un'unica richiesta.

Una volta completata la serie relativa ai dati, è possibile provare a eseguire una delle esercitazioni per Windows Store seguenti:

-   [Introduzione all'autenticazione](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet)
    Informazioni sull'autenticazione degli utenti dell'app.

-   [Introduzione alle notifiche push](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet)
    Informazioni sull'invio di una notifica push di base all'app con Servizi mobili.

<!-- Images. -->
[7]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-services-selection.png
[8]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-portal-data-tables.png
[9]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-insert-script-users.png
[10]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package3.png
[11]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package4.png
