<properties linkid="develop-mobile-tutorials-optimistic-concurrent-data-javascript" urlDisplayName="Optimistic concurrency" pageTitle="Handle database write conflicts with optimistic concurrency (Windows Store) | Mobile Dev Center" metaKeywords="" writer="wesmc" description="Learn how to handle database write conflicts on both the server and in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Handling database write conflicts" />

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

-   Microsoft Visual Studio 2013 Express per Windows o versione successiva.
-   Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare l'esercitazione, è necessario completare l'esercitazione [Introduzione a Servizi mobili](/en-us/develop/mobile/tutorials/get-started), scaricando la versione del linguaggio JavaScript del progetto iniziale.
-   [Account Azure](http://www.windowsazure.com/en-us/pricing/free-trial/)
-   Pacchetto Azure Mobile Services NuGet 1.1.5 o versione successiva. Per ottenere l'ultima versione, eseguire la procedura seguente:
    1.  In Visual Studio fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni, quindi scegliere **Gestisci pacchetti Nuget**.

    2.  Espandere **Online** e fare clic su **Microsoft and .NET**. Nella casella di testo di ricerca immettere **Azure.MobileServices.WinJS**. Fare clic su **Installa** accanto al pacchetto NuGet **Azure Mobile Services for WinJS**.

        ![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/mobile-manage-nuget-packages-dialog.png)

Aggiornamento dell'interfaccia utenteAggiornamento dell'applicazione per consentire gli aggiornamenti
-----------------------------------------------------------------------------------------------------

In questa sezione l'interfaccia utente verrà aggiornata in modo da consentire l'aggiornamento del testo di ogni elemento. Il template di binding conterrà una casella di controllo e un controllo TextClass per ogni elemento presente nella tabella di database. Sarà possibile aggiornare il campo di testo di TodoItem. L'applicazione gestirà l'evento `KeyDown` in modo che l'elemento venga aggiornato premendo il tasto **INVIO**.

1.  In Visual Studio aprire la versione del linguaggio JavaScript del progetto TodoList scaricato nell'esercitazione [Introduzione a Servizi mobili](/en-us/develop/mobile/tutorials/get-started).
2.  In Esplora soluzioni di Visual Studio aprire default.html e sostituire la definizione del tag div `TemplateItem` con il tag div mostrato di seguito, quindi salvare la modifica. Viene aggiunto un controllo TextBox per consentire la modifica del testo di un file TodoItem.

         <div id="TemplateItem" data-win-control="WinJS.Binding.Template">
           <div style="display: -ms-grid; -ms-grid-columns: auto 1fr">
             <input class="itemCheckbox" type="checkbox" data-win-bind="checked: complete; dataContext: this" />
               <div style="-ms-grid-column: 2; -ms-grid-row-align: center; margin-left: 5px">
                 <input id="modifytextbox" class="text win-interactive" data-win-bind="value: text; dataContext: this" />
               </div>
           </div>
         </div>

3.  In Esplora soluzioni di Visual Studio espandere la cartella **js**. Aprire il file default.js e sostituire la funzione `updateTodoItem` con la definizione riportata di seguito che non rimuoverà gli elementi aggiornati dall'interfaccia utente.

         var updateTodoItem = function (todoItem) {
           // This code takes a freshly completed TodoItem and updates the database. 
           todoTable.update(todoItem);
           };

4.  Nel file default.js aggiungere il gestore di eventi seguente per l'evento `KeyDown` in modo che l'elemento venga aggiornato premendo il tasto **INVIO**.

         listItems.onkeydown = function (eventArgs) {
           if (eventArgs.key == "Enter") {
             var todoItem = eventArgs.target.dataContext.backingData;
             todoItem.text = eventArgs.target.value;
             updateTodoItem(todoItem);
             }
           };

Le modifiche del testo verranno scritte di nuovo in ciascun elemento quando si preme il tasto **INVIO**.

Abilitazione della concorrenza ottimisticaAbilitazione del rilevamento dei conflitti nell'applicazione
------------------------------------------------------------------------------------------------------

Servizi mobili di Azure supporta il controllo della concorrenza ottimistica tenendo traccia delle modifiche apportate a ogni elemento mediante la colonna delle proprietà di sistema `__version` aggiunta a ogni tabella. In questa sezione si abiliterà il rilevamento dei conflitti di scrittura nell'applicazione attraverso la proprietà di sistema `__version`. Dopo aver attivato la proprietà di sistema in todoTable, l'applicazione riceverà un'eccezione `MobileServicePreconditionFailedException` durante i tentativi di aggiornamento se il record è stato modificato dopo l'ultima query. L'app potrà quindi scegliere se eseguire il commit della modifica nel database o lasciare intatta l'ultima modifica al database. Per ulteriori informazioni sulle proprietà di sistema relative a Servizi mobili, vedere le [proprietà di sistema](http://go.microsoft.com/fwlink/?LinkId=331143).

1.  Nel file default.js, sotto la dichiarazione della variabile `todoTable`, aggiungere il codice in modo da includere la proprietà di sistema **\_\_version** che abilita il supporto del rilevamento dei conflitti di scrittura.

         var todoTable = client.getTable('TodoItem');
         todoTable.systemProperties |= WindowsAzure.MobileServiceTable.SystemProperties.Version;

2.  Aggiungendo la proprietà di sistema `Version` alle proprietà di sistema della tabella, l'applicazione riceverà un'eccezione `MobileServicePreconditionFailedException` in fase di aggiornamento se il record è stato modificato dopo l'ultima query. L'eccezione verrà rilevata in JavaScript con una funzione di errore. L'errore include l'ultima versione dell'elemento del server utilizzato per risolvere i conflitti. In default.js aggiornare la funzione `updateTodoItem` per rilevare l'errore e chiamare una funzione `resolveDatabaseConflict`.

         var updateTodoItem = function (todoItem) {
           // This code takes a freshly completed TodoItem and updates the database. 
           // If the server version of the record has been updated, we get the updated
           // record from the Precondition Failed error in order to resolve the conflict.
           var serverItem = null;
           todoTable.update(todoItem).then(null, function (error) {
             if (error.message == "Precondition Failed") {
               serverItem = error.serverInstance;
             }
             else {
               var msgDialog =
                 new Windows.UI.Popups.MessageDialog(error.request.responseText,"Update Failed");
                 msgDialog.showAsync();
             }
           }).done(function () {
             if (serverItem != null)
               resolveDatabaseConflict(todoItem, serverItem);
           });
         };

3.  In default.js aggiungere la definizione per la funzione `resolveDatabaseConflict()` a cui viene fatto riferimento nella funzione `updateTodoItem`. Si noti che per risolvere il conflitto, prima di aggiornare l'elemento nel database la versione dell'elemento locale viene impostata sulla versione aggiornata dal server. In caso contrario, il conflitto continuerà a verificarsi.

         var resolveDatabaseConflict = function (localItem, serverItem) {
           var content = "This record has been changed as follows on the server already..\n\n" +
               "id : " + serverItem.id + "\n" +
               "text : " + serverItem.text + "\n" +
               "complete : " + serverItem.complete + "\n\n" +
               "Do you want to overwrite the server instance with your data?";
           var msgDialog = new Windows.UI.Popups.MessageDialog(content, "Resolve Database Conflict");
           msgDialog.commands.append(new Windows.UI.Popups.UICommand("Yes"));
           msgDialog.commands.append(new Windows.UI.Popups.UICommand("No"));
           msgDialog.showAsync().done(function (command) {
               if (command.label == "Yes") {
                   localItem.__version = serverItem.__version;
                   updateTodoItem(localItem);
                }
            });
          }

Test dell'appTest dei conflitti di scrittura del database nell'applicazione
---------------------------------------------------------------------------

In questa sezione verrà creato il pacchetto di un'app di Windows Store per installare l'app in un secondo computer o in una macchina virtuale. L'app verrà quindi eseguita in entrambi i computer, generando un conflitto per testare il codice. Entrambe le istanze dell'app tenteranno di aggiornare la proprietà `text` dello stesso elemento e verrà chiesto l'intervento dell'utente per risolvere il conflitto.

1.  Creare un pacchetto dell'app di Windows Store da installare in un secondo computer o in una macchina virtuale. A questo scopo fare clic su **Progetto**-\>**Store**-\>**Crea pacchetti dell'applicazione** in Visual Studio.

    ![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-create-app-package1.png)

2.  Nella schermata Crea i pacchetti fare clic su **No** in quanto il pacchetto non verrà caricato in Windows Store. Fare quindi clic su **Avanti**.

    ![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-create-app-package2.png)

3.  Nella schermata Seleziona e configura pacchetti accettare le impostazioni predefinite e fare clic su **Crea**.

    ![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-create-app-package3.png)

4.  Nella schermata Creazione pacchetti completata fare clic sul collegamento **Percorso di output** per aprire il percorso del pacchetto.

    ![][11]

5.  Copiare nel secondo computer la cartella del pacchetto, "todolist\_1.0.0.0\_AnyCPU\_Debug\_Test". In tale computer aprire la cartella del pacchetto e fare clic con il pulsante destro del mouse sullo script di PowerShell **Add-AppDevPackage.ps1**, quindi scegliere **Esegui con PowerShell** come illustrato di seguito. Attenersi alle istruzioni visualizzate per installare l'app.

    ![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-install-app-package.png)

6.  Eseguire l'istanza 1 dell'app in Visual Studio facendo clic su **Debug**-\>**Avvia debug**. Nella schermata Start del secondo computer fare clic sulla freccia in giù per visualizzare le app in base al nome. Fare quindi clic sull'app **todolist** per eseguire l'istanza 2 dell'app.

    Istanza 1 dell'app 

	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1.png)

    Istanza 2 dell'app 

	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1.png)

7.  Nell'istanza 1 dell'app aggiornare il testo dell'ultimo elemento in **Test Write 1**, quindi premere il tasto **INVIO** per aggiornare il database. Nella schermata seguente viene visualizzato un esempio.

    Istanza 1 dell'app 

	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1-write1.png)

    Istanza 2 dell'app 

	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1.png)

8.  A questo punto l'ultimo elemento dell'istanza 2 dell'app contiene una versione precedente dell'elemento. In tale istanza dell'app, immettere **Test Write 2** per la proprietà `text` dell'ultimo elemento e premere **INVIO** per aggiornare il database con una precedente proprietà `_version`.

    Istanza 1 dell'app 

	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1-write2.png)

    Istanza 2 dell'app 

	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app2-write2.png)

9.  Poiché il valore `__version` utilizzato nel tentativo di aggiornamento non corrisponde al valore `__version` del server, Mobile Services SDK genera nella funzione `updateTodoItem` un'eccezione `MobileServicePreconditionFailedException` come errore che consente all'app di risolvere il conflitto. Per risolvere il conflitto è possibile fare clic su **Yes** per eseguire il commit dei valori dell'istanza 2. In alternativa, fare clic su **No** per rimuovere i valori nell'istanza 2 e mantenere il commit dei valori dell'istanza 1 dell'app.

    Istanza 1 dell'app 

	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1-write2.png)

    Istanza 2 dell'app 

	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app2-write2-conflict.png)

Gestione dei conflitti tramite scriptGestione automatica della risoluzione dei conflitti tramite script del server
------------------------------------------------------------------------------------------------------------------

È possibile rilevare e risolvere i conflitti tramite script del server. Questa scelta è consigliabile quando per risolvere il conflitto è possibile utilizzare logica di script anziché richiedere l'interazione dell'utente. In questa sezione verrà aggiunto uno script sul lato server alla tabella TodoItem dell'applicazione. La logica utilizzata dallo script per risolvere il conflitto è la seguente:

-   Se il campo \` complete` di TodoItem è impostato su true, viene considerato completato e non sarà più possibile modificare la proprietà `text\`.
-   Se il campo \` complete` di TodoItem è ancora impostato su false, verrà eseguito il commit dei tentativi di aggiornamento di `text\`.

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

5.  Eseguire l'app **todolist** in entrambi i computer. Modificare la proprietà `text` dell'ultimo elemento nell'istanza 2 e premere **INVIO** in modo che l'app aggiorni il database.

    Istanza 1 dell'app 

	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1-write2.png)

    Istanza 2 dell'app 

	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app2-write2.png)

6.  Nell'istanza 1 dell'app immettere un valore diverso per l'ultima proprietà text, quindi premere **INVIO**. L'app tenterà di aggiornare il database con una proprietà `__version` errata.

    Istanza 1 dell'app 

	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1-write3.png)

    Istanza 2 dell'app 

	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app2-write3.png)

7.  Si noti che nell'app non sono state rilevate eccezioni, in quanto lo script del server ha risolto il conflitto consentendo l'aggiornamento, poiché l'elemento non è contrassegnato come completato. Per verificare che l'aggiornamento abbia avuto esito positivo, fare clic su **Refresh** nell'istanza 2 per ripetere la query sul database.

    Istanza 1 dell'app 

	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-write3.png)

    Istanza 2 dell'app 

	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-write3.png)

8.  Nell'istanza 1 fare clic sulla casella di controllo per completare l'ultimo elemento Todo.

    Istanza 1 dell'app 

	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-checkbox.png)

    Istanza 2 dell'app 

	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-write3.png)

9.  Nell'istanza 2, se si tenta di aggiornare il testo dell'ultima TodoItem e si preme **INVIO** si genera un conflitto perché il testo è stato aggiornato impostando il campo completo su true. Lo script ha risolto il conflitto rifiutando l'aggiornamento, in quanto l'elemento era già contrassegnato come completato. Lo script ha fornito un messaggio nella risposta.

    Istanza 1 dell'app 

	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-2-items.png)

    Istanza 2 dell'app 

	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-already-complete.png)

Passaggi successivi
-------------------

In questa esercitazione sono state illustrate le procedure per abilitare la gestione dei conflitti di scrittura in un'app di Windows Store quando si utilizzano i dati in Servizi mobili. In seguito, è consigliabile eseguire una delle esercitazioni seguenti della serie relativa ai dati:

-   [Utilizzo di script per la convalida e la modifica di dati](/en-us/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/)
    <br/>Ulteriori informazioni sull'utilizzo di script del server in Servizi mobili per convalidare e modificare i dati inviati dall'app.

-   [Utilizzo del paging per ridefinire le query](/en-us/documentation/articles/mobile-services-windows-store-javascript-add-paging-data/)
    <br/>Ulteriori informazioni su come utilizzare il paging nelle query per controllare la quantità di dati gestiti in un'unica richiesta.

Una volta completata la serie relativa ai dati, è possibile provare a eseguire una delle esercitazioni per Windows Store seguenti:

-   [Introduzione all'autenticazione](/en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-users/)
    <br/>Informazioni sull'autenticazione degli utenti dell'app.

-   [Introduzione alle notifiche push](/en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-push/)
    <br/>Informazioni sull'invio di una notifica push di base all'app con Servizi mobili.


<!-- Anchors. -->
[Update the application to allow updates]: #uiupdate
[Enable Conflict Detection in your application]: #enableOC
[Test database write conflicts in the application]: #test-app
[Automatically handling conflict resolution in server scripts]: #scriptsexample
[Next Steps]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-create-app-package1.png
[1]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-create-app-package2.png
[2]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1.png 
[3]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1-write1.png
[4]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1-write2.png
[5]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app2-write2.png
[6]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app2-write2-conflict.png
[7]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/mobile-services-selection.png
[8]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/mobile-portal-data-tables.png
[9]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/mobile-insert-script-users.png
[10]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-create-app-package3.png
[11]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-create-app-package4.png
[12]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-install-app-package.png
[13]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1-write3.png
[14]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app2-write3.png
[15]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-write3.png
[16]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-checkbox.png
[17]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-2-items.png
[18]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-already-complete.png
[19]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/mobile-manage-nuget-packages-VS.png
[20]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/mobile-manage-nuget-packages-dialog.png

<!-- URLs. -->
[Optimistic Concurrency Control]: http://go.microsoft.com/fwlink/?LinkId=330935
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started/#create-new-service
[Windows Azure Account]: http://www.windowsazure.com/en-us/pricing/free-trial/
[Validate and modify data with scripts]: /en-us/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/
[Refine queries with paging]: /en-us/documentation/articles/mobile-services-windows-store-javascript-add-paging-data/
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started
[Get started with authentication]: /en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
[Get started with push notifications]: /en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-push/

[Windows Azure Management Portal]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268374
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268375
[Developer Code Samples site]:  http://go.microsoft.com/fwlink/p/?LinkId=271146
[System Properties]: http://go.microsoft.com/fwlink/?LinkId=331143
