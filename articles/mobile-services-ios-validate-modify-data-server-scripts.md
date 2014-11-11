<properties linkid="develop-mobile-tutorials-validate-modify-and-augment-data-ios" urlDisplayName="Validate Data" pageTitle="Use server scripts to validate and modify data (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to validate and modify data sent using server scripts from your iOS app." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="krisragh" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh" />

# Convalida e modifica dei dati in Servizi mobili mediante script del server

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts" title="Windows Store C#">Windows Store C#</a><a href="/it-it/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/it-it/documentation/articles/mobile-services-windows-phone-validate-modify-data-server-scripts" title="Windows Phone">Windows Phone</a><a href="/it-it/documentation/articles/mobile-services-ios-validate-modify-data-server-scripts" title="iOS" class="current">iOS</a><a href="/it-it/documentation/articles/mobile-services-android-validate-modify-data-server-scripts" title="Android">Android</a><a href="/it-it/documentation/articles/mobile-services-html-validate-modify-data-server-scripts" title="HTML" class="current">HTML</a><a href="/it-it/documentation/articles/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/it-it/documentation/articles/partner-xamarin-mobile-services-android-validate-modify-data-server-scripts" title="Xamarin.Android" class="current">Xamarin.Android</a></div>

In questo argomento viene illustrato come utilizzare gli script del server in Servizi mobili di Azure. Gli script del server vengono registrati in un servizio mobile e possono essere utilizzati per eseguire numerose operazioni sui dati inseriti e aggiornati, incluse la convalida e la modifica dei dati. In questa esercitazione si procederà alla definizione e alla registrazione di script del server per la convalida e la modifica dei dati. Poiché il comportamento degli script sul lato server spesso influisce sul client, si procederà quindi all'aggiornamento dell'app per iOS per implementare questi nuovi comportamenti.

In questa esercitazione vengono descritte le operazioni di base seguenti:

1.  [Aggiunta della convalida della lunghezza della stringa][Aggiunta della convalida della lunghezza della stringa]
2.  [Aggiornamento del client per il supporto della convalida][Aggiornamento del client per il supporto della convalida]

Questa esercitazione è basata sulle procedure e sull'app di esempio creata nell'esercitazione precedente [Introduzione ai dati][Introduzione ai dati]. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione ai dati][Introduzione ai dati].

## <a name="string-length-validation"></a>Aggiunta della convalida

È sempre consigliabile convalidare la lunghezza dei dati inviati dagli utenti. A questo scopo, è innanzitutto necessario registrare uno script che convalida la lunghezza dei dati di stringa inviati al servizio mobile e rifiuta le stringhe troppo lunghe, in questo caso lunghe più di 10 caratteri.

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure], fare clic su **Mobile Services** e quindi sull'app.

    ![][0]

2.  Fare clic sulla scheda **Data** e quindi sulla tabella **TodoItem**.

    ![][1]

3.  Fare clic su **Script** e quindi selezionare l'operazione **Insert**.

    ![][2]

4.  Sostituire lo script esistente con la funzione seguente e quindi fare clic su **Save**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be 10 characters or less.');
            } else {
                request.execute();
            }
        }

    Questo script verifica la lunghezza della proprietà **text** e invia una risposta di errore quando la lunghezza supera i 10 caratteri. In caso contrario, viene chiamato il metodo **execute** per completare l'operazione di inserimento.

    <div class="dev-callout"> 
<b>Nota</b> 
<p>&Egrave; possibile rimuovere uno script registrato nella scheda <strong>Script</strong> facendo clic su <strong>Clear</strong> e quindi su <strong>Save</strong>.</p></div>

## <a name="update-client-validation"></a>Aggiornamento del client

Ora che il servizio mobile convalida dati e invia risposte di errore, è necessario aggiornare l'app .NET affinché sia in grado di gestire le risposte di errore dalla convalida.

1.  In Xcode aprire il progetto modificato dopo aver completato l'esercitazione [Introduzione ai dati][Introduzione ai dati].

2.  Premere il pulsante **Run** (Comando + R) per compilare il progetto e avviare l'app, quindi immettere un testo più lungo di 10 caratteri nella casella di testo e fare clic sull'icona con il segno più (**+**).

    Si noti che l'app genera un errore non gestito a seguito della risposta 400 (Richiesta non valida) restituita dal servizio mobile.

3.  Nel file QSTodoService.m individuare la riga di codice seguente nel metodo **addItem**:

        [self logErrorIfNotNil:error]; 

    Dopo questa riga, sostituire la parte rimanente del blocco di completamento con il codice seguente:

        BOOL goodRequest = !((error) && (error.code == MSErrorMessageErrorCode));

        // detect text validation error from service.
        if (goodRequest) // The service responded appropriately
        {
            NSUInteger index = [items count];
            [(NSMutableArray *)items insertObject:result atIndex:index];

            // Let the caller know that we finished
            completion(index);
        }
        else{

            // if there's an error that came from the service
            // log it, and popup up the returned string.
            if (error && error.code == MSErrorMessageErrorCode) {
                NSLog(@"ERROR %@", error);
                UIAlertView *av =
                [[UIAlertView alloc]
                 initWithTitle:@"Request Failed"
                 message:error.localizedDescription
                 delegate:nil
                 cancelButtonTitle:@"OK"
                 otherButtonTitles:nil
                 ];
                [av show];
            }
        }

    L'errore verrà registrato nella finestra di output e visualizzato all'utente.

4.  Ricompilare e avviare l'app.

    ![][3]

    Si noti che l'errore viene gestito e che il messaggio di errore viene visualizzato all'utente.

<!--## <a name="add-timestamp"></a>Add a timestamp  The previous tasks validated an insert and either accepted or rejected it. Now, you will update inserted data by using a server script that adds a timestamp property to the object before it gets inserted.  1. In the **Scripts** tab in the [Management Portal], replace the current **Insert** script with the following function, and then click **Save**.          function insert(item, user, request) {             if (item.text.length > 10) {                 request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');             } else {                 item.createdAt = new Date();                 request.execute();             }         }      This function augments the previous insert script by adding a new **createdAt** timestamp property to the object before it gets inserted by the call to **request**.**execute**.       <div class="dev-callout"><b>Note</b>     <p>Dynamic schema must be enabled the first time that this insert script runs. With dynamic schema enabled, Mobile Services automatically adds the <strong>createdAt</strong> column to the <strong>TodoItem</strong> table on the first execution. Dynamic schema is enabled by default for a new mobile service, and it should be disabled before the app is published.</p>     </div>  2. In Visual Studio, press the **F5** key to run the app, then type text (shorter than 10 characters) in **Insert a TodoItem** and click **Save**.      Notice that the new timestamp does not appear in the app UI.  3. Back in the Management Portal, click the **Browse** tab in the **todoitem** table.         Notice that there is now a **createdAt** column, and the new inserted item has a timestamp value.    Next, you need to update the iOS app to display this new column.  ## <a name="update-client-timestamp"></a>Update the client again  The Mobile Service client will ignore any data in a response that it cannot serialize into properties on the defined type. The final step is to update the client to display this new data.  1. In Visual Studio, open the file MainPage.xaml.cs, then replace the existing **TodoItem** class with the following definition:          public class TodoItem         {             public int Id { get; set; }                        [DataMember(Name="text")]             public string Text { get; set; }              [DataMember(Name="complete")]             public bool Complete { get; set; }                          [DataMember(Name="createdAt")]             public DateTime? CreatedAt { get; set; }         }          This new class definition includes the new timestamp property, as a nullable DateTime type.        <div class="dev-callout"><b>Note</b>     <p>The <strong>DataMemberAttribute</strong> tells the client to map the new <strong>CreatedAt</strong> property in the app to the <strong>createdAt</strong> column defined in the TodoItem table, which has a different casing. By using this attribute, your app can have property names on objects that differ from column names in the SQL Database. Without this attribute, an error would occur because of the casing differences.</p>     </div>  5. Add the following XAML element just below the **CheckBoxComplete** element in the MainPage.xaml file:                    <TextBlock Name="WhenCreated" Text="{Binding CreatedAt}" VerticalAlignment="Center"/>      This displays the new **CreatedAt** property in a text box.       6. Press the **F5** key to run the app.      Notice that the timestamp is only displayed for items inserted after you updated the insert script.  7. Replace the existing **RefreshTodoItems** method with the following code:           private void RefreshTodoItems()         {              // This query filters out completed TodoItems and              // items without a timestamp.              items = todoTable                .Where(todoItem => todoItem.Complete == false                    && todoItem.CreatedAt != null)                .ToCollectionView();              ListItems.ItemsSource = items;         }      This method updates the query to also filter out items that do not have a timestamp value.      8. Press the **F5** key to run the app.      Notice that all items created without timestamp value disappear from the UI.  You have completed this working with data tutorial.-->

## <a name="next-steps"> </a>Passaggi successivi

Dopo avere completato anche questa esercitazione, provare a svolgere l'ultima esercitazione della serie relativa ai dati: [Utilizzo del paging per ridefinire le query][Utilizzo del paging per ridefinire le query].

Gli script server vengono inoltre utilizzati per l'autorizzazione degli utenti e per l'invio di notifiche push. Per ulteriori informazioni, vedere le esercitazioni seguenti:

-   [Autorizzazione di utenti con script][Autorizzazione di utenti con script]
    Informazioni su come filtrare i dati in base all'ID di un utente autenticato.

-   [Introduzione alle notifiche push][Introduzione alle notifiche push]
    Informazioni sull'invio di una notifica push di base all'app.

-   [Riferimento per gli script del server di Servizi mobili][Riferimento per gli script del server di Servizi mobili]
    Ulteriori informazioni sulla registrazione e l'utilizzo di script del server.



  [Aggiunta della convalida della lunghezza della stringa]: #string-length-validation
  [Aggiornamento del client per il supporto della convalida]: #update-client-validation
  [Introduzione ai dati]: /it-it/develop/mobile/tutorials/get-started-with-data-ios
  [portale di gestione di Azure]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-services-selection.png
  [1]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-portal-data-tables.png
  [2]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-insert-script-users.png
  [3]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-quickstart-data-error-ios.png
  [Utilizzo del paging per ridefinire le query]: /it-it/develop/mobile/tutorials/add-paging-to-data-ios
  [Autorizzazione di utenti con script]: /it-it/develop/mobile/tutorials/authorize-users-in-scripts-ios
  [Introduzione alle notifiche push]: /it-it/develop/mobile/tutorials/get-started-with-push-ios
  [Riferimento per gli script del server di Servizi mobili]: http://go.microsoft.com/fwlink/?LinkId=262293
