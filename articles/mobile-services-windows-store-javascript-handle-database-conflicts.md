<properties linkid="develop-mobile-tutorials-optimistic-concurrent-data-javascript" urlDisplayName="Optimistic concurrency" pageTitle="Handle database write conflicts with optimistic concurrency (Windows Store) | Mobile Dev Center" metaKeywords="" writer="wesmc" description="Learn how to handle database write conflicts on both the server and in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Handling database write conflicts" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# Gestione di conflitti di scrittura nel database

<div class="dev-center-tutorial-selector sublanding">
<a href="/it-it/develop/mobile/tutorials/handle-database-write-conflicts-dotnet/" title="Windows Store C#">Windows Store C#</a>
<a href="/it-it/documentation/articles/mobile-services-windows-store-javascript-handle-database-conflicts/" title="Windows Store JavaScript" class="current">Windows Store JavaScript</a>
<a href="/it-it/develop/mobile/tutorials/handle-database-write-conflicts-wp8/" title="Windows Phone">Windows Phone</a></div>

In questa esercitazione viene descritto come gestire i conflitti che si verificano quando due o più client scrivono nello stesso record di database in un'app di Windows Store. In alcuni casi, due o più client possono scrivere modifiche sullo stesso elemento contemporaneamente. Se il conflitto non viene rilevato, l'ultima scrittura sovrascrive tutti gli aggiornamenti precedenti, anche se non si tratta del risultato desiderato. Servizi mobili di Azure offre supporto per il rilevamento e la risoluzione di tali conflitti. In questo argomento vengono illustrate le procedure per la gestione dei conflitti di scrittura nel database sia nel server che nell'applicazione.

Nel corso dell'esercitazione si aggiungeranno all'app di guida introduttiva funzionalità per la gestione dei conflitti che si verificano durante l'aggiornamento del database TodoItem. In questa esercitazione vengono descritte le operazioni di base seguenti:

1.  [Aggiornamento dell'applicazione per consentire gli aggiornamenti][Aggiornamento dell'applicazione per consentire gli aggiornamenti]
2.  [Abilitazione del rilevamento dei conflitti nell'applicazione][Abilitazione del rilevamento dei conflitti nell'applicazione]
3.  [Test dei conflitti di scrittura del database nell'applicazione][Test dei conflitti di scrittura del database nell'applicazione]
4.  [Gestione automatica della risoluzione dei conflitti tramite script del server][Gestione automatica della risoluzione dei conflitti tramite script del server]

Per completare questa esercitazione, è necessario disporre di:

-   Microsoft Visual Studio 2013 Express per Windows o versione successiva.
-   Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare l'esercitazione, è necessario completare l'esercitazione [Introduzione a Servizi mobili][Introduzione a Servizi mobili], scaricando la versione del linguaggio JavaScript del progetto iniziale.
-   [Account Azure][Account Azure]
-   Pacchetto Azure Mobile Services NuGet 1.1.5 o versione successiva. Per ottenere l'ultima versione, eseguire la procedura seguente:

    1.  In Visual Studio fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni, quindi scegliere **Gestisci pacchetti NuGet**.

    2.  Espandere **Online** e fare clic su **Microsoft and .NET**. Nella casella di testo di ricerca immettere **Azure.MobileServices.WinJS**. Fare clic su **Installa** accanto al pacchetto NuGet **Azure Mobile Services for WinJS**.

        ![][0]

## <a name="uiupdate"></a><span class="short-header">Aggiornamento dell'interfaccia utente</span>Aggiornamento dell'applicazione per consentire gli aggiornamenti

In questa sezione l'interfaccia utente verrà aggiornata in modo da consentire l'aggiornamento del testo di ogni elemento. Il template di binding conterrà una casella di controllo e un controllo TextClass per ogni elemento presente nella tabella di database. Sarà possibile aggiornare il campo di testo di TodoItem. L'applicazione gestirà l'evento `keydown` in modo che l'elemento venga aggiornato premendo il tasto **INVIO**.

1.  In Visual Studio aprire la versione del linguaggio JavaScript del progetto TodoList scaricato nell'esercitazione [Introduzione a Servizi mobili][Introduzione a Servizi mobili].
2.  In Esplora soluzioni di Visual Studio aprire il file default.html e sostituire la definizione del tag div `TemplateItem` con il tag div seguente, quindi salvare la modifica. Viene aggiunto un controllo TextBox per consentire la modifica del testo di un file TodoItem.

        <div id="TemplateItem" data-win-control="WinJS.Binding.Template">
          <div style="display: -ms-grid; -ms-grid-columns: auto 1fr">
            <input class="itemCheckbox" type="checkbox" data-win-bind="checked: complete; dataContext: this" />
              <div style="-ms-grid-column: 2; -ms-grid-row-align: center; margin-left: 5px">
                <input id="modifytextbox" class="text win-interactive" data-win-bind="value: text; dataContext: this" />
              </div>
          </div>
        </div>

3.  In Esplora soluzioni di Visual Studio espandere la cartella **js**. Aprire il file default.js e sostituire la funzione `updateTodoItem` con la definizione seguente che non rimuoverà gli elementi aggiornati dall'interfaccia utente.

        var updateTodoItem = function (todoItem) {
          // This code takes a freshly completed TodoItem and updates the database. 
          todoTable.update(todoItem);
          };

4.  Nel file default.js aggiungere il gestore di eventi seguente per l'evento `keydown` in modo che l'elemento venga aggiornato premendo il tasto **INVIO**.

        listItems.onkeydown = function (eventArgs) {
          if (eventArgs.key == "Enter") {
            var todoItem = eventArgs.target.dataContext.backingData;
            todoItem.text = eventArgs.target.value;
            updateTodoItem(todoItem);
            }
          };

Le modifiche del testo verranno scritte di nuovo in ciascun elemento quando si preme il tasto **INVIO**.

## <a name="enableOC"></a><span class="short-header">Abilitazione della concorrenza ottimistica</span>Abilitazione del rilevamento dei conflitti nell'applicazione

Servizi mobili di Azure supporta il controllo della concorrenza ottimistica tenendo traccia delle modifiche apportate a ogni elemento tramite la colonna di proprietà di sistema `__version` aggiunta a ogni tabella. In questa sezione si abiliterà il rilevamento dei conflitti di scrittura nell'applicazione attraverso la proprietà di sistema `__version`. Dopo aver abilitato la proprietà di sistema in todoTable, l'applicazione riceverà un'eccezione `MobileServicePreconditionFailedException` durante un tentativo di aggiornamento se il record è stato modificato dopo l'ultima query. L'app potrà quindi scegliere se eseguire il commit della modifica nel database o lasciare intatta l'ultima modifica al database. Per ulteriori informazioni sulle proprietà di sistema relative a Servizi mobili, vedere le [proprietà di sistema][proprietà di sistema].

1.  Nel file default.js, sotto la dichiarazione della variabile `todoTable`, aggiungere il codice in modo da includere la proprietà di sistema \*\*\_\_version\*\* che abilita il supporto per il rilevamento dei conflitti di scrittura.

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

## <a name="test-app"></a><span class="short-header">Test dell'app</span>Test dei conflitti di scrittura del database nell'applicazione

In questa sezione verrà creato il pacchetto di un'app di Windows Store per installare l'app in un secondo computer o in una macchina virtuale. L'app verrà quindi eseguita in entrambi i computer, generando un conflitto per testare il codice. Entrambe le istanze dell'app proveranno ad aggiornare la proprietà `text` dello stesso elemento e verrà chiesto l'intervento dell'utente per risolvere il conflitto.

1.  Creare un pacchetto dell'app di Windows Store da installare in un secondo computer o in una macchina virtuale. A questo scopo fare clic su **Progetto**-\>**Store**-\>**Crea pacchetti dell'applicazione** in Visual Studio.

    ![][1]

2.  Nella schermata Crea i pacchetti fare clic su **No** in quanto il pacchetto non verrà caricato in Windows Store. Quindi fare clic su **Next**.

    ![][2]

3.  Nella schermata Seleziona e configura pacchetti accettare le impostazioni predefinite e fare clic su **Crea**.

    ![][3]

4.  Nella schermata Creazione pacchetti completata fare clic sul collegamento **Percorso di output** per aprire il percorso del pacchetto.

    ![][4]

5.  Copiare nel secondo computer la cartella del pacchetto, "todolist\_1.0.0.0\_AnyCPU\_Debug\_Test". In tale computer aprire la cartella del pacchetto e fare clic con il pulsante destro del mouse sullo script di PowerShell **Add-AppDevPackage.ps1**, quindi scegliere **Esegui con PowerShell** come illustrato di seguito. Attenersi alle istruzioni visualizzate per installare l'app.

    ![][5]

6.  Eseguire l'istanza 1 dell'app in Visual Studio facendo clic su **Debug**-\>**Avvia debug**. Nella schermata Start del secondo computer fare clic sulla freccia in giù per visualizzare le app in base al nome. Fare quindi clic sull'app **todolist** per eseguire l'istanza 2 dell'app.

    Istanza 1 dell'app
    ![][6]

    Istanza 2 dell'app
    ![][6]

7.  Nell'istanza 1 dell'app aggiornare il testo dell'ultimo elemento in **Test Write 1**, quindi premere il tasto **INVIO** per aggiornare il database. Nella schermata seguente viene visualizzato un esempio.

    Istanza 1 dell'app
    ![][7]

    Istanza 2 dell'app
    ![][6]

8.  A questo punto l'ultimo elemento dell'istanza 2 dell'app contiene una versione precedente dell'elemento. In tale istanza dell'app, immettere **Test Write 2** per la proprietà `text` dell'ultimo elemento e premere **INVIO** per aggiornare il database con una precedente proprietà `_version`.

    Istanza 1 dell'app
    ![][8]

    Istanza 2 dell'app
    ![][9]

9.  Poiché il valore `__version` usato nel tentativo di aggiornamento non corrisponde al valore `__version` del server, Mobile Services SDK genera un'eccezione `MobileServicePreconditionFailedException` come errore nella funzione `updateTodoItem` che consente all'app di risolvere il conflitto. Per risolvere il conflitto è possibile fare clic su **Yes** per eseguire il commit dei valori dell'istanza 2. In alternativa, fare clic su **No** per rimuovere i valori nell'istanza 2 e mantenere il commit dei valori dell'istanza 1 dell'app.

    Istanza 1 dell'app
    ![][8]

    Istanza 2 dell'app
    ![][10]

## <a name="scriptsexample"></a><span class="short-header">Gestione dei conflitti tramite script</span>Gestione automatica della risoluzione dei conflitti tramite script del server

È possibile rilevare e risolvere i conflitti tramite script del server. Questa scelta è consigliabile quando per risolvere il conflitto è possibile utilizzare logica di script anziché richiedere l'interazione dell'utente. In questa sezione verrà aggiunto uno script sul lato server alla tabella TodoItem dell'applicazione. La logica utilizzata dallo script per risolvere il conflitto è la seguente:

-   Se il campo `complete` di TodoItem è impostato su true, viene considerato completato e non sarà più possibile modificare la proprietà `text`.
-   Se il campo `complete` di TodoItem è ancora impostato su false, verrà eseguito il commit dei tentativi di aggiornamento di `text`.

Di seguito è riportata la procedura per aggiungere e testare lo script di aggiornamento del server.

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure], fare clic su **Mobile Services** e quindi sull'app.

    ![][11]

2.  Fare clic sulla scheda **Data** e quindi sulla tabella **TodoItem**.

    ![][12]

3.  Fare clic su **Script**, quindi selezionare l'operazione **Update**.

    ![][13]

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

5.  Eseguire l'app **todolist** in entrambi i computer. Modificare la proprietà `text` di TodoItem dell'ultimo elemento nell'istanza 2 e premere **INVIO** in modo che l'app aggiorni il database.

    Istanza 1 dell'app
    ![][8]

    Istanza 2 dell'app
    ![][9]

6.  Nell'istanza 1 dell'app immettere un valore diverso per l'ultima proprietà text, quindi premere **INVIO**. L'app tenterà di aggiornare il database con una proprietà `__version` errata.

    Istanza 1 dell'app
    ![][14]

    Istanza 2 dell'app
    ![][15]

7.  Si noti che nell'app non sono state rilevate eccezioni, in quanto lo script del server ha risolto il conflitto consentendo l'aggiornamento, poiché l'elemento non è contrassegnato come completato. Per verificare che l'aggiornamento abbia avuto esito positivo, fare clic su **Refresh** nell'istanza 2 per ripetere la query sul database.

    Istanza 1 dell'app
    ![][16]

    Istanza 2 dell'app
    ![][16]

8.  Nell'istanza 1 fare clic sulla casella di controllo per completare l'ultimo elemento Todo.

    Istanza 1 dell'app
    ![][17]

    Istanza 2 dell'app
    ![][16]

9.  Nell'istanza 2, se si tenta di aggiornare il testo dell'ultima TodoItem e si preme **INVIO** si genera un conflitto perché il testo è stato aggiornato impostando il campo completo su true. Lo script ha risolto il conflitto rifiutando l'aggiornamento, in quanto l'elemento era già contrassegnato come completato. Lo script ha fornito un messaggio nella risposta.

    Istanza 1 dell'app
    ![][18]

    Istanza 2 dell'app
    ![][19]

## <a name="next-steps"> </a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per abilitare la gestione dei conflitti di scrittura in un'app di Windows Store quando si utilizzano i dati in Servizi mobili. In seguito, è consigliabile eseguire una delle esercitazioni seguenti della serie relativa ai dati:

-   [Utilizzo di script per la convalida e la modifica di dati][Utilizzo di script per la convalida e la modifica di dati]
    Ulteriori informazioni sull'utilizzo di script del server in Servizi mobili per convalidare e modificare i dati inviati dall'app.

-   [Utilizzo del paging per ridefinire le query][Utilizzo del paging per ridefinire le query]
    Ulteriori informazioni su come utilizzare il paging nelle query per controllare la quantità di dati gestiti in un'unica richiesta.

Una volta completata la serie relativa ai dati, è possibile provare a eseguire una delle esercitazioni per Windows Store seguenti:

-   [Introduzione all'autenticazione][Introduzione all'autenticazione]
    Informazioni sull'autenticazione degli utenti dell'app.

-   [Introduzione alle notifiche push][Introduzione alle notifiche push]
    Informazioni sull'invio di una notifica push di base all'app con Servizi mobili.



  [Aggiornamento dell'applicazione per consentire gli aggiornamenti]: #uiupdate
  [Abilitazione del rilevamento dei conflitti nell'applicazione]: #enableOC
  [Test dei conflitti di scrittura del database nell'applicazione]: #test-app
  [Gestione automatica della risoluzione dei conflitti tramite script del server]: #scriptsexample
  [Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started
  [Account Azure]: http://www.windowsazure.com/it-it/pricing/free-trial/
  [0]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/mobile-manage-nuget-packages-dialog.png
  [proprietà di sistema]: http://go.microsoft.com/fwlink/?LinkId=331143
  [1]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-create-app-package1.png
  [2]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-create-app-package2.png
  [3]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-create-app-package3.png
  [4]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-create-app-package4.png
  [5]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-install-app-package.png
  [6]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1.png
  [7]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1-write1.png
  [8]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1-write2.png
  [9]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app2-write2.png
  [10]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app2-write2-conflict.png
  [portale di gestione di Azure]: https://manage.windowsazure.com/
  [11]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/mobile-services-selection.png
  [12]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/mobile-portal-data-tables.png
  [13]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/mobile-insert-script-users.png
  [14]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1-write3.png
  [15]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app2-write3.png
  [16]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-write3.png
  [17]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-checkbox.png
  [18]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-2-items.png
  [19]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-already-complete.png
  [Utilizzo di script per la convalida e la modifica di dati]: /it-it/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/
  [Utilizzo del paging per ridefinire le query]: /it-it/documentation/articles/mobile-services-windows-store-javascript-add-paging-data/
  [Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
  [Introduzione alle notifiche push]: /it-it/documentation/articles/mobile-services-windows-store-javascript-get-started-push/
