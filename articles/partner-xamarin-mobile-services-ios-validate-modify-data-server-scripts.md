<properties linkid="develop-mobile-tutorials-validate-modify-and-augment-data-Xamarin-iOS" urlDisplayName="" pageTitle="Use server scripts to validate and modify data (Xamarin iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to validate and modify data sent using server scripts from your Xamarin iOS app." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="donnam" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam"></tags>

# Convalida e modifica dei dati in Servizi mobili mediante script del server

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet" title="Windows Store C#">Windows Store C#</a><a href="/it-it/develop/mobile/tutorials/validate-modify-and-augment-data-js" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/it-it/develop/mobile/tutorials/validate-modify-and-augment-data-wp8" title="Windows Phone">Windows Phone</a><a href="/it-it/develop/mobile/tutorials/validate-modify-and-augment-data-ios" title="iOS">iOS</a><a href="/it-it/develop/mobile/tutorials/validate-modify-and-augment-data-android" title="Android">Android</a><a href="/it-it/develop/mobile/tutorials/validate-modify-and-augment-data-html" title="HTML">HTML</a><a href="/it-it/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/it-it/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android" title="Xamarin.Android" class="current">Xamarin.Android</a></div>

In questo argomento viene illustrato come utilizzare gli script del server in Servizi mobili di Azure. Gli script del server vengono registrati in un servizio mobile e possono essere utilizzati per eseguire numerose operazioni sui dati inseriti e aggiornati, incluse la convalida e la modifica dei dati. In questa esercitazione si procederà alla definizione e alla registrazione di script del server per la convalida e la modifica dei dati. Poiché il comportamento degli script sul lato server spesso influisce sul client, si procederà quindi all'aggiornamento dell'app per iOS per implementare questi nuovi comportamenti. Il codice compilato è disponibile nell'esempio di [app ValidateModifyData][].

In questa esercitazione vengono descritte le operazioni di base seguenti:

1.  [Aggiunta della convalida della lunghezza della stringa][]
2.  [Aggiornamento del client per il supporto della convalida][]
3.  [Aggiunta di un timestamp all'inserimento][]
4.  [Aggiornamento del client per la visualizzazione del timestamp][]

Questa esercitazione è basata sulle procedure e sull'app di esempio creata nell'esercitazione precedente [Introduzione ai dati][]. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione ai dati][].

## <a name="string-length-validation"></a>Aggiunta della convalida

È sempre consigliabile convalidare la lunghezza dei dati inviati dagli utenti. A questo scopo, è innanzitutto necessario registrare uno script che convalida la lunghezza dei dati di stringa inviati al servizio mobile e rifiuta le stringhe troppo lunghe, in questo caso lunghe più di 10 caratteri.

1.  Accedere al [portale di gestione di Azure][], fare clic su **Mobile Services** e quindi sull'app.

    ![][]

2.  Fare clic sulla scheda **Data** e quindi sulla tabella **TodoItem**.

    ![][1]

3.  Fare clic su **Script** e quindi selezionare l'operazione **Insert**.

    ![][2]

4.  Sostituire lo script esistente con la funzione seguente e quindi fare clic su **Save**.

    function insert(item, user, request) {
     if (item.text.length \> 10) {
     request.respond(statusCodes.BAD\_REQUEST, 'Text length must be 10 characters or less.');
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

1.  In Xamarin Studio aprire il progetto modificato durante l'esercitazione [Introduzione ai dati][].

2.  Premere il pulsante **Run** per compilare il progetto e avviare l'app, quindi immettere un testo più lungo di 10 caratteri nella casella di testo e fare clic sull'icona con il segno più (**+**).

    Si noti che l'app genera un errore non gestito a seguito della risposta 400 (Richiesta non valida) restituita dal servizio mobile.

3.  Nel file TodoService.cs file individuare l'attuale gestione eccezioni `try/catch` nel metodo **InsertTodoItemAsync**, quindi sostituire la parte relativa a `catch` con:

    catch (Exception ex) {
     var exDetail = (ex.InnerException.InnerException as MobileServiceInvalidOperationException);
     Console.WriteLine(exDetail.Message);

        UIAlertView alert = new UIAlertView() { 
                Title = "Error", 
                Message = exDetail.Message
        } ;
        alert.AddButton("Ok");
        alert.Show();

        return -1;
        }

    Ciò determina l'apertura di una finestra popup in cui è visualizzato l'errore per l'utente.

4.  Individuare il metodo **OnAdd** in **TodoListViewController.cs**. Aggiornare il metodo per assicurarsi che il valore `index` restituito non sia `-1` come restituito nella gestione eventi in **InsertTodoItemAsync**. In questo caso non verrà aggiunta una nuova riga a `TableView`.

    if (index != -1) {
     TableView.InsertRows(new [] { NSIndexPath.FromItemSection(index, 0) },
     UITableViewRowAnimation.Top);
     itemText.Text = "";
    }

5.  Ricompilare e avviare l'app.

    ![][3]

    Si noti che l'errore viene gestito e che il messaggio di errore viene visualizzato all'utente.

## <a name="next-steps"> </a>Passaggi successivi

Dopo avere completato anche questa esercitazione, provare a svolgere l'ultima esercitazione della serie relativa ai dati: [Utilizzo del paging per ridefinire le query][].

Gli script server vengono inoltre utilizzati per l'autorizzazione degli utenti e per l'invio di notifiche push. Per ulteriori informazioni, vedere le esercitazioni seguenti:

-   [Autorizzazione di utenti con script][]
    Informazioni su come filtrare i dati in base all'ID di un utente autenticato.

-   [Introduzione alle notifiche push][]
    Informazioni sull'invio di una notifica push di base all'app.

-   [Riferimento per gli script del server di Servizi mobili][]
    Ulteriori informazioni sulla registrazione e l'utilizzo di script del server.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [Windows Store C#]: /it-it/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet "Windows Store C#"
  [Windows Store JavaScript]: /it-it/develop/mobile/tutorials/validate-modify-and-augment-data-js "Windows Store JavaScript"
  [Windows Phone]: /it-it/develop/mobile/tutorials/validate-modify-and-augment-data-wp8 "Windows Phone"
  [iOS]: /it-it/develop/mobile/tutorials/validate-modify-and-augment-data-ios "iOS"
  [Android]: /it-it/develop/mobile/tutorials/validate-modify-and-augment-data-android "Android"
  [HTML]: /it-it/develop/mobile/tutorials/validate-modify-and-augment-data-html "HTML"
  [Xamarin.iOS]: /it-it/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios "Xamarin.iOS"
  [Xamarin.Android]: /it-it/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android "Xamarin.Android"
  [app ValidateModifyData]: http://go.microsoft.com/fwlink/p/?LinkId=331330
  [Aggiunta della convalida della lunghezza della stringa]: #string-length-validation
  [Aggiornamento del client per il supporto della convalida]: #update-client-validation
  [Aggiunta di un timestamp all'inserimento]: #add-timestamp
  [Aggiornamento del client per la visualizzazione del timestamp]: #update-client-timestamp
  [Introduzione ai dati]: /it-it/develop/mobile/tutorials/get-started-with-data-xamarin-ios
  [portale di gestione di Azure]: https://manage.windowsazure.com/
  []: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-services-selection.png
  [1]: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-portal-data-tables.png
  [2]: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-insert-script-users.png
  [3]: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-quickstart-data-error-ios.png
  [Utilizzo del paging per ridefinire le query]: /it-it/develop/mobile/tutorials/add-paging-to-data-xamarin-ios
  [Autorizzazione di utenti con script]: /it-it/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios
  [Introduzione alle notifiche push]: /it-it/develop/mobile/tutorials/get-started-with-push-xamarin-ios
  [Riferimento per gli script del server di Servizi mobili]: http://go.microsoft.com/fwlink/?LinkId=262293
