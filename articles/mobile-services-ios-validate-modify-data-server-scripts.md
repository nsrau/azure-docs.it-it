<properties urlDisplayName="Validate Data" pageTitle="Usare gli script del server per convalidare e modificare i dati (iOS) | Mobile Developer Center" metaKeywords="" description="Learn how to validate and modify data sent using server scripts from your iOS app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="krisragh" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# Convalidare e modificare i dati in Servizi mobili mediante script del server

[WACOM.INCLUDE [mobile-services-selector-validate-modify-data](../includes/mobile-services-selector-validate-modify-data.md)]

Questo argomento illustra come usare gli script del server in Servizi mobili di Azure. Gli script del server vengono registrati in un servizio mobile e possono essere usati per eseguire numerose operazioni sui dati inseriti e aggiornati, incluse la convalida e la modifica dei dati. In questa esercitazione si procederà alla definizione e alla registrazione di script del server per la convalida e la modifica dei dati. Poiché il comportamento degli script sul lato server spesso influisce sul client, si procederà quindi all'aggiornamento dell'app per iOS per implementare questi nuovi comportamenti.

In questa esercitazione vengono descritte le operazioni di base seguenti:

1. [Aggiungere la convalida della lunghezza della stringa]
2. [Aggiornare il client per il supporto della convalida]


Questa esercitazione si basa sulle procedure e sull'app di esempio dell'esercitazione precedente [Introduzione ai dati]. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione ai dati].  

## <a name="string-length-validation"></a>Add validation

It is always a good practice to validate the length of data that is submitted by users. First, you register a script that validates the length of string data sent to the mobile service and rejects strings that are too long, in this case longer than 10 characters.

1. Log into the [Azure Management Portal], click **Mobile Services**, and then click your app.

   	![][0]

2. Click the **Data** tab, then click the **TodoItem** table.

   	![][1]

3. Click **Script**, then select the **Insert** operation.

   	![][2]

4. Replace the existing script with the following function, and then click **Save**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be 10 characters or less.');
            } else {
                request.execute();
            }
        }

    This script checks the length of the **text** property and sends an error response when the length exceeds 10 characters. Otherwise, the **execute** method is called to complete the insert.

    <div class="dev-callout">
	<b>Note</b>
	<p>You can remove a registered script on the <strong>Script</strong> tab by clicking <strong>Clear</strong> and then <strong>Save</strong>.</p></div>

## <a name="update-client-validation"></a>Update the client

Now that the mobile service is validating data and sending error responses, you need to update your app to be able to handle error responses from validation.

1. In Xcode, open the project that you modified when you completed the tutorial [Get started with data].

2. Press the **Run** button (Command + R) to build the project and start the app, then type text longer than 10 characters in the textbox and click the  plus (**+**) icon.

   	Notice that the app raises an unhandled error as a result of the 400 response (Bad Request) returned by the mobile service.

3. In the QSTodoService.m file, locate the following line of code in the **addItem** method:

        [self logErrorIfNotNil:error];

   	After this line of code, replace the remainder of the completion block with the following code:

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

   	This logs the error to the output window and displays it to the user.

4. Rebuild and start the app.

   	![][4]

  	Notice that error is handled and the error messaged is displayed to the user.

<!--## <a name="add-timestamp"></a>Add a timestamp

The previous tasks validated an insert and either accepted or rejected it. Now, you will update inserted data by using a server script that adds a timestamp property to the object before it gets inserted.

1. In the **Scripts** tab in the [Management Portal], replace the current **Insert** script with the following function, and then click **Save**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                item.createdAt = new Date();
                request.execute();
            }
        }

    This function augments the previous insert script by adding a new **createdAt** timestamp property to the object before it gets inserted by the call to **request**.**execute**.

    <div class="dev-callout"><b>Note</b>
	<p>Dynamic schema must be enabled the first time that this insert script runs. With dynamic schema enabled, Mobile Services automatically adds the <strong>createdAt</strong> column to the <strong>TodoItem</strong> table on the first execution. Dynamic schema is enabled by default for a new mobile service, and it should be disabled before the app is published.</p>
    </div>

2. In Visual Studio, press the **F5** key to run the app, then type text (shorter than 10 characters) in **Insert a TodoItem** and click **Save**.

   	Notice that the new timestamp does not appear in the app UI.

3. Back in the Management Portal, click the **Browse** tab in the **todoitem** table.

   	Notice that there is now a **createdAt** column, and the new inserted item has a timestamp value.

Next, you need to update the iOS app to display this new column.

## <a name="update-client-timestamp"></a>Update the client again

The Mobile Service client will ignore any data in a response that it cannot serialize into properties on the defined type. The final step is to update the client to display this new data.

1. In Visual Studio, open the file MainPage.xaml.cs, then replace the existing **TodoItem** class with the following definition:

	    public class TodoItem
	    {
	        public int Id { get; set; }

            [DataMember(Name="text")]
	        public string Text { get; set; }

            [DataMember(Name="complete")]
	        public bool Complete { get; set; }

            [DataMember(Name="createdAt")]
	        public DateTime? CreatedAt { get; set; }
	    }

    This new class definition includes the new timestamp property, as a nullable DateTime type.

    <div class="dev-callout"><b>Note</b>
	<p>The <strong>DataMemberAttribute</strong> tells the client to map the new <strong>CreatedAt</strong> property in the app to the <strong>createdAt</strong> column defined in the TodoItem table, which has a different casing. By using this attribute, your app can have property names on objects that differ from column names in the SQL Database. Without this attribute, an error would occur because of the casing differences.</p>
    </div>

5. Add the following XAML element just below the **CheckBoxComplete** element in the MainPage.xaml file:

        <TextBlock Name="WhenCreated" Text="{Binding CreatedAt}" VerticalAlignment="Center"/>

   	La nuova proprietà **CreatedAt** verrà visualizzata in una casella di testo.

6. Premere **F5** per eseguire l'app.

   Si noti che il timestamp è visualizzato solo per gli elementi inseriti dopo l'aggiornamento dello script insert.

7. Sostituire il metodo **RefreshTodoItems** esistente con il codice seguente:

        private void RefreshTodoItems()
        {
            // This query filters out completed TodoItems and
            // items without a timestamp.
            items = todoTable
               .Where(todoItem => todoItem.Complete == false
                   && todoItem.CreatedAt != null)
               .ToCollectionView();

            ListItems.ItemsSource = items;
        }

   	Questo metodo aggiorna la query affinché filtri anche gli elementi che non dispongono di un valore timestamp.

8. Premere **F5** per eseguire l'app.

   	Si noti che tutti gli elementi creati senza un valore timestamp non vengono più visualizzati nell'interfaccia utente.

L'esercitazione relativa all'uso dei dati è stata completata.-->

## <a name="next-steps"> </a>Passaggi successivi

Dopo avere completato anche questa esercitazione, provare a svolgere l'ultima esercitazione della serie relativa ai dati: [Usare il paging per ridefinire le query].

Gli script del server vengono inoltre usati per l'autorizzazione degli utenti e per l'invio di notifiche push. Per altre informazioni, vedere le esercitazioni seguenti:

* [Autorizzare gli utenti con gli script]
  <br/>Informazioni su come filtrare i dati in base all'ID di un utente autenticato.

* [Introduzione alle notifiche push]
  <br/>Informazioni sull'invio di una notifica push di base all'app.

* [Riferimento per gli script del server di Servizi mobili]
  <br/>Altre informazioni sulla registrazione e l'uso di script del server.

<!-- Anchors. -->
[Aggiungere la convalida della lunghezza della stringa]: #string-length-validation
[Aggiornare il client per il supporto della convalida]: #update-client-validation
[Aggiungere un timestamp all'inserimento]: #add-timestamp
[Aggiornare il client per la visualizzazione del timestamp]: #update-client-timestamp
[Passaggi successivi]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-insert-script-users.png

[4]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-quickstart-data-error-ios.png

<!-- URLs. -->
[Riferimento per gli script del server di Servizi mobili]: http://go.microsoft.com/fwlink/?LinkId=262293
[Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started-ios
[Autorizzare gli utenti con gli script]: /it-it/develop/mobile/tutorials/authorize-users-in-scripts-ios
[Usare il paging per ridefinire le query]: /it-it/develop/mobile/tutorials/add-paging-to-data-ios
[Introduzione ai dati]: /it-it/develop/mobile/tutorials/get-started-with-data-ios
[Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-ios
[Introduzione alle notifiche push]: /it-it/develop/mobile/tutorials/get-started-with-push-ios

[Portale di gestione]: https://manage.windowsazure.com/
[Portale di gestione di Azure]: https://manage.windowsazure.com/
