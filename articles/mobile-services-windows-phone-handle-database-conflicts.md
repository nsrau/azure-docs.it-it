<properties linkid="develop-mobile-tutorials-optimistic-concurrent-data-wp8" urlDisplayName="Optimistic concurrency" pageTitle="Handle database write conflicts with optimistic concurrency (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to handle database write conflicts on both the server and in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Handling database write conlicts" authors="" />

Gestione di conflitti di scrittura nel database
===============================================

[Windows Store C\#](/en-us/develop/mobile/tutorials/handle-database-write-conflicts-dotnet/ "Windows Store C#") [Windows Store JavaScript](/en-us/develop/mobile/tutorials/handle-database-write-conflicts-javascript/ "Windows Store JavaScript") [Windows Phone](/en-us/develop/mobile/tutorials/handle-database-write-conflicts-wp8/ "Windows Phone")

In questa esercitazione viene descritto come gestire i conflitti che si verificano quando due o più client scrivono nello stesso record di database in un'app per Windows Phone 8. In alcuni casi, due o più client possono scrivere modifiche sullo stesso elemento contemporaneamente. Se il conflitto non viene rilevato, l'ultima scrittura sovrascrive tutti gli aggiornamenti precedenti, anche se non si tratta del risultato desiderato. Servizi mobili offre supporto per il rilevamento e la risoluzione di tali conflitti. In questo argomento vengono illustrate le procedure per la gestione dei conflitti di scrittura nel database sia nel server che nell'applicazione.

Nel corso dell'esercitazione si aggiungeranno all'app di guida introduttiva funzionalità per la gestione dei conflitti che si verificano durante l'aggiornamento del database TodoItem. In questa esercitazione vengono descritte le operazioni di base seguenti:

1.  [Aggiornamento dell'applicazione per consentire gli aggiornamenti](#uiupdate)
2.  [Abilitazione del rilevamento dei conflitti nell'applicazione](#enableOC)
3.  [Test dei conflitti di scrittura del database nell'applicazione](#test-app)
4.  [Gestione automatica della risoluzione dei conflitti tramite script del server](#scriptsexample)

Per completare questa esercitazione, è necessario disporre di:

-   Microsoft Visual Studio 2012 Express per Windows Phone 8 o versione successiva.
-   [Windows Phone 8 SDK](http://go.microsoft.com/fwlink/p/?LinkID=268374) eseguito in Windows 8.
-   [Account Azure](http://www.windowsazure.com/it-it/pricing/free-trial/)
-   Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione a Servizi mobili](/en-us/develop/mobile/tutorials/get-started-wp8).
-   Pacchetto NuGet per Servizi mobili di Azure 1.1.0 o versione successiva. Per ottenere l'ultima versione, eseguire la procedura seguente:
    1.  In Visual Studio fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni, quindi scegliere **Gestisci pacchetti Nuget**.

        ![](./media/mobile-services-windows-phone-handle-database-conflicts/mobile-manage-nuget-packages-VS.png)

    2.  Espandere **Online** e fare clic su **Microsoft and .NET**. Nella casella di testo di ricerca immettere **Azure Mobile Services**. Fare clic su **Installa** accanto al pacchetto NuGet **Azure Mobile Services**.

        ![](./media/mobile-services-windows-phone-handle-database-conflicts/mobile-manage-nuget-packages-dialog.png)

Aggiornamento dell'interfaccia utenteAggiornamento dell'applicazione per consentire gli aggiornamenti
-----------------------------------------------------------------------------------------------------

In questa sezione l'interfaccia utente di TodoList verrà aggiornata in modo da consentire l'aggiornamento del testo di ogni elemento in un controllo ListBox. Il controllo ListBox conterrà un controllo CheckBox e un controllo TextBox per ogni elemento presente nella tabella di database. Sarà possibile aggiornare il campo di testo di TodoItem. L'applicazione gestirà l'evento `LostFocus` del controllo TextBox per aggiornare l'elemento nel database.

1.  In Visual Studio aprire il progetto TodoList scaricato nell'esercitazione [Introduzione a Servizi mobili](/en-us/develop/mobile/tutorials/get-started-wp8).
2.  In Esplora soluzioni di Visual Studio aprire MainPage.xaml, sostituire la definizione di `phone:LongListSelector` con il codice per ListBox riportato di seguito, quindi salvare la modifica.

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

3.  In Esplora soluzioni di Visual Studio aprire MainPage.xaml.cs e aggiungere la direttiva `using` seguente.

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

Abilitazione della concorrenza ottimisticaAbilitazione del rilevamento dei conflitti nell'applicazione
------------------------------------------------------------------------------------------------------

In alcuni casi, due o più client possono scrivere modifiche sullo stesso elemento contemporaneamente. Se il conflitto non viene rilevato, l'ultima scrittura sovrascrive tutti gli aggiornamenti precedenti, anche se non si tratta del risultato desiderato. Il [controllo della concorrenza ottimistica](http://go.microsoft.com/fwlink/?LinkId=330935) presuppone che per ogni transazione sia possibile eseguire il commit, quindi non procede al blocco delle risorse. Prima di effettuare il commit di una transazione, il controllo della concorrenza ottimistica verifica che i dati non siano stati modificati da un'altra transazione. Se i dati sono stati modificati, verrà eseguito il rollback di tale transazione. Servizi mobili di Azure supporta il controllo della concorrenza ottimistica tenendo traccia delle modifiche apportate a ogni elemento, utilizzando la colonna di proprietà di sistema `__version` aggiunta a ogni tabella. In questa sezione si abiliterà il rilevamento dei conflitti di scrittura nell'applicazione attraverso la proprietà di sistema `__version`. Durante i tentativi di aggiornamento, se un record è stato modificato dopo l'ultima query, l'applicazione riceverà un'eccezione `MobileServicePreconditionFailedException`. Sarà quindi possibile scegliere se eseguire il commit della modifica nel database o lasciare intatta l'ultima modifica al database. Per ulteriori informazioni sulle proprietà di sistema relative a Servizi mobili, vedere le [proprietà di sistema](http://go.microsoft.com/fwlink/?LinkId=331143).

1.  In MainPage.xaml.cs aggiornare la definizione della classe **TodoItem** con il codice seguente in modo da includere la proprietà di sistema **\_\_version**, che abilita il supporto per il rilevamento dei conflitti di scrittura:

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

    <pre><code>//Enable optimistic concurrency by retrieving __version
    todoTable.SystemProperties |= MobileServiceSystemProperties.Version;
    </code></pre>
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
                 if (exception is MobileServicePreconditionFailedException<TodoItem>)
                 {
                     //conflict detected, the item has changed since the last query
                     await ResolveConflict(item, ((MobileServicePreconditionFailedException<TodoItem>)exception).Item);
                 }
                 else
                     MessageBox.Show(exception.Message, "Update Failed", MessageBoxButton.OK);
             }
         }

3.  In MainPage.xaml.cs aggiungere la definizione per il metodo `ResolveConflict()` a cui viene fatto riferimento in `UpdateToDoItem()`. Si noti che, per risolvere il conflitto, prima di eseguire il commit della decisione dell'utente la versione dell'elemento locale viene impostata sulla versione aggiornata dal server. In caso contrario, il conflitto continuerà a verificarsi.

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

Test dell'appTest dei conflitti di scrittura del database nell'applicazione
---------------------------------------------------------------------------

In questa sezione si eseguirà il test del codice che gestisce i conflitti di scrittura eseguendo l'app in due emulatori di Windows Phone 8 diversi (WVGA e WVGA 512M). Entrambe le app client tenteranno di aggiornare la proprietà `text` dello stesso elemento e verrà chiesto l'intervento dell'utente per risolvere il conflitto.

1.  In Visual Studio, dalla casella a discesa, assicurarsi che come destinazione distribuzione sia selezionato **Emulator WVGA 512MB**, come illustrato nella schermata seguente.

    ![](./media/mobile-services-windows-phone-handle-database-conflicts/mobile-EmulatorWVGA512MB.png)

2.  Nel menu in Visual Studio fare clic su **COMPILA**, quindi su **Distribuisci soluzione**. Se l'emulatore non era precedentemente in esecuzione, occorreranno alcuni minuti per il caricamento del sistema operativo Windows Phone 8 da parte dell'emulatore. Nella parte inferiore della finestra di output verificare la buona riuscita della compilazione e della distribuzione dell'emulatore Windows Phone 8.

    ![](./media/mobile-services-windows-phone-handle-database-conflicts/mobile-build-deploy-wp8.png)

3.  In Visual Studio modificare la casella a discesa relativa alla destinazione distribuzione in **Emulator WVGA**.

    ![](./media/mobile-services-windows-phone-handle-database-conflicts/mobile-EmulatorWVGA.png)

4.  Nel menu in Visual Studio fare clic su **COMPILA**, quindi su **Distribuisci soluzione**. Nella parte inferiore della finestra di output verificare la buona riuscita della compilazione e della distribuzione dell'emulatore Windows Phone 8.

    ![][2]

5.  Disporre i due emulatori in modo che siano affiancati durante l'esecuzione. È possibile simulare conflitti da scritture simultanee tra le app client in esecuzione negli emulatori. Scorrere da sinistra a destra in entrambi emulatori per visualizzare l'elenco delle applicazioni installate. Scorrere fino alla fine di ogni lista e fare clic sull'app **todolist**.

    ![](./media/mobile-services-windows-phone-handle-database-conflicts/mobile-start-apps-oc-wp8.png)

6.  Nell'emulatore sul lato sinistro aggiornare il valore `text` dell'ultimo elemento in **Test Write 1**, quindi fare clic su un'altra casella di testo in modo che il gestore dell'evento `LostFocus` aggiorni il database. Nella schermata seguente viene visualizzato un esempio.

    ![](./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-write1-wp8.png)

7.  A questo punto, l'elemento corrispondente nell'emulatore sul lato destro conterrà una versione precedente e un valore text non aggiornato. Nell'emulatore a destra immettere **Test Write 2** come proprietà text. Fare quindi clic su un'altra casella di testo in modo che il gestore dell'evento `LostFocus` nell'emulatore a destra tenti di aggiornare il database con la proprietà version precedente.

    ![](./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-write2-wp8.png)

8.  Poiché il valore version utilizzato nel tentativo di aggiornamento non corrisponde al valore version del server, Mobile Services SDK genera un'eccezione `MobileServicePreconditionFailedException` che consente all'app di risolvere il conflitto. Per risolvere il conflitto, è possibile fare clic su **ok** per eseguire il commit dei valori dall'app a destra. In alternativa è possibile fare clic su **cancel** per ignorare i valori nell'app a destra e lasciare invariati i valori dell'app a sinistra.

    ![](./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-exception-wp8.png)

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

5.  Modificare il testo di TodoItem per l'ultimo elemento nell'app dell'emulatore a sinistra. Fare clic su un'altra casella di testo in modo che il gestore dell'evento `LostFocus` aggiorni il database.

    ![](./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-write1-wp8.png)

6.  Nell'emulatore a destra immettere un valore diverso per la proprietà text dell'ultimo elemento TodoItem. Fare quindi clic su un'altra casella di testo in modo che il gestore dell'evento `LostFocus` nell'emulatore a destra tenti di aggiornare il database con la proprietà version precedente.

    ![](./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-write2-wp8.png)

7.  Si noti che nell'app non sono state rilevate eccezioni, in quanto lo script del server ha risolto il conflitto consentendo l'aggiornamento, poiché l'elemento non è contrassegnato come completato. Per verificare che l'aggiornamento abbia avuto esito positivo, fare clic su **Refresh** nell'app dell'emulatore a sinistra per ripetere la query sul database.

    ![](./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-insync-wp8.png)

8.  Nell'app dell'emulatore a sinistra fare clic sulla casella di controllo per completare l'ultimo elemento TodoItem.

    ![](./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-complete-checkbox-wp8.png)

9.  Nell'app dell'emulatore a destra tentare di aggiornare lo stesso testo di TodoItem attivando il trigger dell'evento `LostFocus`. Lo script ha risolto il conflitto rifiutando l'aggiornamento, in quanto l'elemento era già contrassegnato come completato.

    ![](./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-already-completed-wp8.png)

Passaggi successivi
-------------------

In questa esercitazione sono state illustrate le procedure per abilitare la gestione dei conflitti di scrittura in un'app Windows Phone 8 quando si utilizzano i dati in Servizi mobili. In seguito, è consigliabile eseguire una delle esercitazioni seguenti della serie relativa ai dati:

-   [Utilizzo di script per la convalida e la modifica di dati](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-wp8)
    <br/>Ulteriori informazioni sull'utilizzo di script del server in Servizi mobili per convalidare e modificare i dati inviati dall'app.

-   [Utilizzo del paging per ridefinire le query](/en-us/develop/mobile/tutorials/add-paging-to-data-wp8)
    <br/>Ulteriori informazioni su come utilizzare il paging nelle query per controllare la quantità di dati gestiti in un'unica richiesta.

Una volta completata la serie relativa ai dati, è possibile provare a eseguire una delle esercitazioni per Windows Phone 8 seguenti:

-   [Introduzione all'autenticazione](/en-us/develop/mobile/tutorials/get-started-with-users-wp8)
    <br/>Informazioni sull'autenticazione degli utenti dell'app.

-   [Introduzione alle notifiche push](/en-us/develop/mobile/tutorials/get-started-with-push-wp8)
    <br/>Informazioni sull'invio di una notifica push di base all'app con Servizi mobili.


<!-- Anchors. -->
[Update the application to allow updates]: #uiupdate
[Enable Conflict Detection in your application]: #enableOC
[Test database write conflicts in the application]: #test-app
[Automatically handling conflict resolution in server scripts]: #scriptsexample
[Next Steps]:#next-steps

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
[Optimistic Concurrency Control]: http://go.microsoft.com/fwlink/?LinkId=330935
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started/#create-new-service
[Azure Account]: http://www.windowsazure.com/it-it/pricing/free-trial/
[Validate and modify data with scripts]: /en-us/develop/mobile/tutorials/validate-modify-and-augment-data-wp8
[Refine queries with paging]: /en-us/develop/mobile/tutorials/add-paging-to-data-wp8
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started-wp8
[Get started with data]: ./mobile-services-get-started-with-data-wp8.md
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-wp8
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-wp8

[Azure Management Portal]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268374
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268375
[Developer Code Samples site]:  http://go.microsoft.com/fwlink/p/?LinkId=271146
[System Properties]: http://go.microsoft.com/fwlink/?LinkId=331143
