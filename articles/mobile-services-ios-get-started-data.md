<properties linkid="develop-mobile-tutorials-get-started-with-data-ios" urlDisplayName="Get Started with Data" pageTitle="Get started with data (iOS) | Mobile Dev Center" metaKeywords="Azure iOS data, Azure mobile services data, " description="Learn how to get started using Mobile Services to leverage data in your iOS app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

Introduzione ai dati in Servizi mobili
======================================

[Windows Store C#](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet "Windows Store C#")[Windows Store JavaScript](/en-us/develop/mobile/tutorials/get-started-with-data-js "Windows Store JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-data-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-data-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-data-android "Android")[HTML](/en-us/develop/mobile/tutorials/get-started-with-data-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-android "Xamarin.Android")

In questo argomento viene illustrato come utilizzare Servizi mobili di Azure per sfruttare i dati in un'app per iOS. In questa esercitazione si scaricherà un'app che archivia dati in memoria, si creerà un nuovo servizio mobile e lo si integrerà con l'app e quindi si effettuerà l'accesso al portale di gestione di Azure per visualizzare le modifiche apportate ai dati durante l'esecuzione dell'app.

**Nota**

In questa esercitazione viene descritto come è possibile utilizzare Servizi mobili di Azure per archiviare e recuperare i dati da un'app per iOS e vengono pertanto riproposte molte delle procedure già completate nella guida introduttiva a Servizi mobili. Se si tratta della prima esperienza con Servizi mobili, è consigliabile iniziare dall'esercitazione [Introduzione a Servizi mobili](/en-us/develop/mobile/tutorials/get-started-ios).

In questa esercitazione vengono descritte le operazioni di base seguenti:

1.  [Download del progetto dell'app per iOS](#download-app)
2.  [Creazione del servizio mobile](#create-service)
3.  [Aggiunta di una tabella dati per l'archiviazione](#add-table)
4.  [Aggiornamento dell'app per l'utilizzo di Servizi mobili](#update-app)
5.  [Test dell'app in Servizi mobili](#test-app)

Per completare questa esercitazione, è necessario disporre di [Mobile Services SDK per iOS](https://go.microsoft.com/fwLink/p/?LinkID=266533), di [XCode 4.5](https://go.microsoft.com/fwLink/p/?LinkID=266532) e di iOS 5.0 o versioni successive.

**Nota**

Per completare l'esercitazione, è necessario un account Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-ios%2F).

Download del progettoDownload del progetto GetStartedWithData
-------------------------------------------------------------

Questa esercitazione è basata sull'[app GetStartedWithData](http://go.microsoft.com/fwlink/p/?LinkId=268622) che è un'app per iOS. L'interfaccia utente di questa app è identica a quella dell'app generata dalla guida introduttiva di Servizi mobili per iOS, ma in questo caso gli elementi aggiunti vengono archiviati nella memoria locale.

1.  Scaricare l'[app di esempio](http://go.microsoft.com/fwlink/p/?LinkId=268622) GetStartedWithData.

2.  In Xcode aprire il progetto scaricato ed esaminare il file QSTodoService.m.

   	Si noti che sono presenti otto commenti **//TODO** in cui vengono specificate le procedure da eseguire per il funzionamento dell'app con il servizio mobile.

3.  Premere il pulsante **Run** (o il tasto Comando+R) per ricompilare il progetto e avviare l'app.

4.  Nell'app digitare un testo nella casella di testo e quindi fare clic sul pulsante **+**.

   	![](./media/mobile-services-ios-get-started-data/mobile-quickstart-startup-ios.png)

   	Si noti che il testo salvato è visualizzato nell'elenco riportato di seguito.

Creazione del servizio mobileCreazione di un nuovo servizio mobile nel portale di gestione
------------------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

Aggiunta di una nuova tabellaAggiunta di una nuova tabella al servizio mobile
-----------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

Aggiornamento dell'appAggiornamento dell'app per l'utilizzo del servizio mobile per l'accesso ai dati
-----------------------------------------------------------------------------------------------------

Ora che il servizio mobile è pronto, è possibile aggiornare l'app in modo che gli elementi vengano archiviati in Servizi mobili anziché nella raccolta locale.

1.  Se non lo si è già fatto, installare adesso l'[Mobile Services SDK per iOS](https://go.microsoft.com/fwLink/p/?LinkID=266533).

2.  Nella struttura del progetto in Xcode aprire i file TodoService.m e TodoService.h nella cartella Quickstart e aggiungere l'istruzione import seguente

         #import <WindowsAzureMobileServices/WindowsAzureMobileServices.h>  

3.  Nel file ToDoService.h individuare la riga di codice commentata seguente:

        // Create an MSClient property comment in the #interface declaration for the TodoService.

   	Dopo questo commento, aggiungere la riga di codice seguente:

        @property (nonatomic, strong)   MSClient *client;

   	Verrà creata una proprietà che rappresenta il client Servizi mobili che si connette al servizio

4.  Nel file TodoService.m individuare la riga di codice commentata seguente:

        // Create an MSTable property for your items.  

   	Dopo questo commento, aggiungere la riga di codice seguente all'interno della dichiarazione @interface:

        @property (nonatomic, strong)   MSTable *table;

   	Verrà creata una rappresentazione della proprietà per la tabella di Servizi mobili.

5.  Nel portale di gestione fare clic su **Mobile Services** e quindi sul servizio mobile appena creato.

6.  Fare clic sulla scheda **Dashboard** e prendere nota del valore di **Site URL**, quindi fare clic su **Manage keys** e prendere nota del valore di **Application key**.

   	![](./media/mobile-services-ios-get-started-data/mobile-dashboard-tab.png)

  	Questi valori sono necessari per accedere al servizio mobile dal codice dell'app.

1.  In Xcode, aprire il file TodoService.m e individuare la riga di codice commentata seguente:

         // Initialize the Mobile Service client with your URL and key.

    Dopo questo commento, aggiungere la riga di codice seguente:

         self.client = [MSClient clientWithApplicationURLString:@"APPURL" applicationKey:@"APPKEY"];

    Verrà creata un'istanza del client di Servizi mobili.

2.  Nel codice sostituire **APPURL** e **APPKEY** con l'URL del servizio mobile e la chiave dell'applicazione del servizio mobile acquisiti nel passaggio 6.

3.  Individuare la riga di codice commentata seguente:

         // Create an MSTable instance to allow us to work with the TodoItem table.

    Dopo questo commento, aggiungere la riga di codice seguente:

         self.table = [self.client tableWithName:@"TodoItem"];

    Verrà creata un'istanza della tabella TodoItem.

4.  Individuare la riga di codice commentata seguente:

 	    // Create a predicate that finds items where complete is false comment in the refreshDataOnSuccess method. 

    Dopo questo commento, aggiungere la riga di codice seguente:

        NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];

    Verrà creata una query che restituisce tutte le attività non ancora completate.

1.  Individuare la riga di codice commentata seguente:

        // Query the TodoItem table and update the items property with the results from the service.

	Sostituire il commento e la chiamata al blocco **completion** successiva con il codice seguente:

        // Query the TodoItem table and update the items property with the results from the service
        [self.table readWithPredicate:predicate completion:^(NSArray *results, NSInteger totalCount, NSError *error) 
        {
           self.items = [results mutableCopy];
           completion();
        }]; 

1.  Individuare il metodo **addItem** e sostituire il corpo del metodo con il codice seguente:

        // Insert the item into the TodoItem table and add to the items array on completion
        [self.table insert:item completion:^(NSDictionary *result, NSError *error) {
            NSUInteger index = [items count];
            [(NSMutableArray *)items insertObject:item atIndex:index];
         
            // Let the caller know that we finished
            completion(index);
            }];

    Il codice invia una richiesta insert al servizio mobile.

2.  Individuare il metodo **completeItem** e sostituire il corpo del metodo con il codice seguente:

        // Update the item in the TodoItem table and remove from the items array on completion
        [self.table update:mutable completion:^(NSDictionary *item, NSError *error) {

            // TODO
            // Get a fresh index in case the list has changed
            NSUInteger index = [items indexOfObjectIdenticalTo:mutable];

            [mutableItems removeObjectAtIndex:index];

            // Let the caller know that we have finished
            completion(index);
            }]; 

	Il codice rimuove gli elementi TodoItems dopo che sono stati contrassegnati come completati.

Una volta aggiornata l'app per consentire l'utilizzo di Servizi mobili per l'archiviazione back-end, è possibile verificarne il funzionamento in Servizi mobili.

Test dell'appTest dell'app nel nuovo servizio mobile
----------------------------------------------------

1.  In Xcode selezionare un emulatore in cui effettuare la distribuzione (iPhone o iPad), premere il pulsante **Run** (o il tasto Comando+R) per ricompilare il progetto e avviare l'app.

   	Verrà eseguito il client Servizi mobili di Azure, compilato con l'SDK per iOS, e verrà eseguita una query sugli elementi dal servizio mobile.

2.  Come in precedenza, digitare un testo nella casella di testo e quindi fare clic sul pulsante **+**.

   	Un nuovo elemento verrà inviato come inserimento al servizio mobile.

3.  Nel [portale di gestione](https://manage.windowsazure.com/) fare clic su **Mobile Services** e quindi sul servizio mobile.

4.  Fare clic sulla scheda **Data** e quindi su **Browse**.

   	![](./media/mobile-services-ios-get-started-data/mobile-todoitem-data-browse.png)
  
   	Si noti che la tabella **TodoItem** ora contiene dati, con valori ID generati da Servizi mobili, e che alla tabella sono state aggiunte automaticamente colonne per garantire la corrispondenza con la classe TodoItem nell'app.

L'esercitazione **Introduzione ai dati** per iOS è terminata.

Passaggi successivi
-------------------

In questa esercitazione sono state illustrate le nozioni di base per consentire a un'app per iOS di utilizzare dati in Servizi mobili.

In seguito, è consigliabile eseguire una delle esercitazioni seguenti, basate sull'app GetStartedWithData creata in questa esercitazione:

-   [Utilizzo di script per la convalida e la modifica di dati](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet)
    Ulteriori informazioni sull'utilizzo di script del server in Servizi mobili per convalidare e modificare i dati inviati dall'app.

-   [Utilizzo del paging per ridefinire le query](/en-us/develop/mobile/tutorials/add-paging-to-data-ios)
    Ulteriori informazioni su come utilizzare il paging nelle query per controllare la quantità di dati gestiti in un'unica richiesta.

Una volta completata la serie relativa ai dati, provare a eseguire le esercitazioni per iOS seguenti:

-   [Introduzione all'autenticazione](/en-us/develop/mobile/tutorials/get-started-with-users-ios)
    Informazioni sull'autenticazione degli utenti dell'app.

-   [Introduzione alle notifiche push](/en-us/develop/mobile/tutorials/get-started-with-push-ios)
    Informazioni sull'invio di una notifica push di base all'app con Servizi mobili.


