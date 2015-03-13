<properties 
	pageTitle="Aggiungere paging ai dati (iOS) | Mobile Dev Center" 
	description="Informazioni su come usare il paging per gestire la quantità di dati restituiti all'app per iOS da Servizi mobili." 
	services="mobile-services" 
	documentationCenter="ios" 
	authors="krisragh" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="krisragh"/>

# Usare il paging per ridefinire le query di Servizi mobili

[AZURE.INCLUDE [mobile-services-selector-add-paging-data](../includes/mobile-services-selector-add-paging-data.md)]

Questo argomento descrive come usare il paging per gestire la quantità di dati restituiti all'app per iOS da Servizi mobili di Azure. In questa esercitazione verranno usate le proprietà di query **fetchLimit** e **fetchOffset** sul client per richiedere "pagine" di dati specifiche.

> [AZURE.NOTE] Per evitare l'overflow dei dati nei client dei dispositivi mobili, Servizi mobili implementa un limite di pagina automatico, che per impostazione predefinita prevede un massimo di 50 elementi in una risposta. Specificando la dimensione della pagina, è possibile richiedere in modo esplicito fino a un massimo di 1.000 elementi nella risposta.

Questa esercitazione è basata sulle procedure e sull'app di esempio creata nell'esercitazione precedente [Introduzione ai dati]. Prima di iniziare questa esercitazione, è necessario completare almeno la prima esercitazione della serie relativa all'uso dei dati, [Introduzione ai dati].

1. In Xcode aprire il progetto modificato dopo aver completato l'esercitazione [Introduzione ai dati].

2. Premere il pulsante **Run** (Comando + R) per compilare il progetto e avviare l'app, quindi immettere il testo nella casella di testo e fare clic sull'icona con il segno più (**+**).

3. Ripetere il passaggio precedente almeno tre volte, in modo da avere più di tre elementi archiviati nella tabella TodoItem.

4. Aprire il file QSTodoService.m e individuare il metodo seguente:

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

5. Ricompilare e avviare l'app.

    Si noti che sono visualizzati solo i primi tre risultati della tabella TodoItem.

7. Aggiornare di nuovo il metodo **refreshDataOnSuccess** individuando la riga di codice seguente:

        query.fetchOffset = 0;

   	Questa volta impostare il valore di **query.fetchOffset** su 3.

   	Questa query ignora i primi tre risultati e restituisce i tre risultati successivi. In sostanza, si tratta della seconda "pagina" di dati le cui dimensioni corrispondono a tre voci.

    > [AZURE.NOTE] In questa esercitazione viene usato uno scenario semplificato impostando valori di paging hardcoded per le proprietà **fetchOffset** e **fetchLimit**. In un'app reale è possibile usare query simili con un controllo pager o un'interfaccia utente paragonabile per consentire agli utenti di passare alle pagine precedenti e successive. È anche possibile impostare **query.includeTotalCount = YES** per ottenere il conteggio totale degli elementi disponibili sul server, insieme ai dati di paging.

## <a name="next-steps"> </a>Passaggi successivi

Il set di esercitazioni relative alle nozioni di base dell'uso dei dati in Servizi mobili è concluso. Per altre informazioni, vedere anche il seguente argomento su Servizi mobili:

* [Introduzione all'autenticazione]
  <br/>Informazioni sull'autenticazione degli utenti dell'app con un account Windows.

<!--
* [Introduzione alle notifiche push]
  <br/>Informazioni sull'invio di una notifica push di base all'app.
-->

<!-- Anchors. -->

[Passaggi successivi]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started-ios
[Introduzione ai dati]: /it-it/develop/mobile/tutorials/get-started-with-data-ios
[Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-ios
[Introduzione alle notifiche push]: /it-it/develop/mobile/tutorials/get-started-with-push-ios

[Portale di gestione]: https://manage.windowsazure.com/


<!--HONumber=42-->
