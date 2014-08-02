<properties linkid="develop-mobile-tutorials-add-paging-to-data-ios" urlDisplayName="Add paging to data" pageTitle="Add paging to data (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your iOS app from Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="" solutions="" manager="" editor="" />

Utilizzo del paging per ridefinire le query di Servizi mobili
=============================================================

> [AZURE.SELECTOR-LIST (Platform | Backend )]
- [(Windows Store C\# | .NET)](mobile-services-dotnet-backend-windows-store-dotnet-add-paging-data.md)
- [(Windows Store C\# | JavaScript)](mobile-services-windows-store-dotnet-add-paging-data.md)
- [(Windows Store JavaScript | .NET)](mobile-services-dotnet-backend-windows-store-javascript-add-paging-data.md)
- [(Windows Store JavaScript | JavaScript)](mobile-services-windows-store-javascript-add-paging-data.md)
- [(Windows Phone | .NET)](mobile-services-dotnet-backend-windows-phone-add-paging-data)
- [(Windows Phone | JavaScript)](mobile-services-windows-phone-add-paging-data)
- [(iOS | JavaScript)](mobile-services-ios-add-paging-data)
- [(Android | JavaScript)](mobile-services-android-add-paging-data)
- [(HTML | .NET)](mobile-services-html-add-paging-data)
- [(Xamarin iOS | .NET)](partner-xamarin-mobile-services-ios-add-paging-data)
- [(Xamarin Android | .NET)](partner-xamarin-mobile-services-android-add-paging-data)

In questo argomento viene illustrato come utilizzare il paging per gestire la quantità di dati restituiti all'app per iOS da Servizi mobili di Azure. In questa esercitazione verranno utilizzate le proprietà di query **fetchLimit** e **fetchOffset** sul client per richiedere "pagine" di dati specifiche.

**Nota**

Per evitare l'overflow dei dati nei client dei dispositivi mobili, Servizi mobili implementa un limite di pagina automatico, che per impostazione predefinita prevede un massimo di 50 elementi in una risposta. Specificando la dimensione della pagina, è possibile richiedere in modo esplicito fino a un massimo di 1.000 elementi nella risposta.

Questa esercitazione è basata sulle procedure e sull'app di esempio creata nell'esercitazione precedente [Introduzione ai dati](/en-us/develop/mobile/tutorials/get-started-with-data-ios). Prima di iniziare questa esercitazione, è necessario completare almeno la prima esercitazione della serie relativa all'utilizzo dei dati, [Introduzione ai dati](/en-us/develop/mobile/tutorials/get-started-with-data-ios).

1.  In Xcode aprire il progetto modificato dopo aver completato l'esercitazione [Introduzione ai dati](/en-us/develop/mobile/tutorials/get-started-with-data-ios).

2.  Premere il pulsante **Run** (Comando + R) per compilare il progetto e avviare l'app, quindi immettere il testo nella casella di testo e fare clic sull'icona con il segno più (**+**).

3.  Ripetere il passaggio precedente almeno tre volte, in modo da avere più di tre elementi archiviati nella tabella TodoItem.

4.  Aprire il file QSTodoService.m e individuare il metodo seguente:

         - (void)refreshDataOnSuccess:(QSCompletionBlock)completion

   	Sostituire il corpo dell'intero metodo con il codice seguente. 

         // Create a predicate that finds active items in which complete is false
         NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];

         // Retrieve the MSTable's MSQuery instance with the predicate you just created.
         MSQuery * query = [self.table queryWithPredicate:predicate];
            
         query.includeTotalCount = YES; // Request the total item count

         // Start with the first item, and retrieve only three items
         query.fetchOffset = 0;
         query.fetchLimit = 3;

         // Invoke the MSQuery instance directly, rather than using the MSTable helper methods.
         [query readWithCompletion:^(NSArray *results, NSInteger totalCount, NSError *error) {
                
             [self logErrorIfNotNil:error];
             if (!error)
             {
                 // Log total count.
                 NSLog(@"Total item count: %@",[NSString stringWithFormat:@"%zd", (ssize_t) totalCount]);            
             }
            
             items = [results mutableCopy];
            
             // Let the caller know that we finished
             completion();
         }];

   	Questa query restituisce i primi tre elementi non contrassegnati come completati. 

5.  Ricompilare e avviare l'app.

    Si noti che sono visualizzati solo i primi tre risultati della tabella TodoItem.

6.  Aggiornare di nuovo il metodo **refreshDataOnSuccess** individuando la riga di codice seguente:

         query.fetchOffset = 0;

   	Questa volta impostare il valore di **query.fetchOffset** su 3. 

   	Questa query ignora i primi tre risultati e restituisce i tre risultati successivi. In sostanza, si tratta della seconda "pagina" di dati, pagina la cui dimensione corrisponde a tre voci.

    **Nota**

    In questa esercitazione viene utilizzato uno scenario semplificato impostando valori di paging hardcoded per le proprietà **fetchOffset** e **fetchLimit**. In un'app reale è possibile utilizzare query simili con un controllo pager o un'interfaccia utente paragonabile per consentire agli utenti di passare alle pagine precedenti e successive. È inoltre possibile impostare **query includeTotalCount = YES** per ottenere il conteggio totale degli elementi disponibili sul server, insieme ai dati di paging.

Passaggi successivi
-------------------

Il set di esercitazioni relative alle nozioni di base dell'utilizzo dei dati in Servizi mobili è concluso. Per ulteriori informazioni, vedere anche il seguente argomento su Servizi mobili:

-   [Introduzione all'autenticazione](/en-us/develop/mobile/tutorials/get-started-with-users-ios)
  <br/>Informazioni sull'autenticazione degli utenti dell'app con l'account Windows.

