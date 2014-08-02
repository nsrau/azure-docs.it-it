<properties linkid="" urlDisplayName="" pageTitle="Add paging to data (Xamarin iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Xamarin iOS app from Mobile Services." metaCanonical="" services="" authors="" solutions="" manager="" editor="" title="Refine Mobile Services queries with paging" documentationCenter="Mobile" />

Utilizzo del paging per ridefinire le query di Servizi mobili
=============================================================

[Windows Store C\#](/en-us/develop/mobile/tutorials/add-paging-to-data-dotnet "Windows Store C#")[Windows Store JavaScript](/en-us/develop/mobile/tutorials/add-paging-to-data-js "Windows Store JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/add-paging-to-data-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/add-paging-to-data-ios "iOS")[Android](/en-us/develop/mobile/tutorials/add-paging-to-data-android "Android")[HTML](/en-us/develop/mobile/tutorials/add-paging-to-data-html "HTML")[iOS C\#](/en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-ios "Xamarin.iOS")[Android C\#](/en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-android "Xamarin.Android")

In questo argomento viene illustrato come utilizzare il paging per gestire la quantità di dati restituiti all'app per iOS da Servizi mobili di Azure. In questa esercitazione verranno utilizzate le proprietà di query **Skip** e **Take** sul client per richiedere "pagine" di dati specifiche.

**Nota**

Per evitare l'overflow dei dati nei client dei dispositivi mobili, Servizi mobili implementa un limite di pagina automatico, che per impostazione predefinita prevede un massimo di 50 elementi in una risposta. Specificando la dimensione della pagina, è possibile richiedere in modo esplicito fino a un massimo di 1.000 elementi nella risposta.

Questa esercitazione è basata sulle procedure e sull'app di esempio creata nell'esercitazione precedente [Introduzione ai dati](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios). Prima di iniziare questa esercitazione, è necessario completare almeno la prima esercitazione della serie relativa all'utilizzo dei dati, [Introduzione ai dati](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios).

1.  In Xamarin Studio aprire il progetto modificato durante l'esercitazione [Introduzione ai dati](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios).

2.  Premere il pulsante **Run** per compilare il progetto e avviare l'app, quindi immettere il testo nella casella di testo e fare clic sull'icona con il segno più (**+**).

3.  Ripetere il passaggio precedente almeno tre volte, in modo da avere più di tre elementi archiviati nella tabella TodoItem.

4.  Aprire il file **TodoService.cs**, individuare il metodo **RefreshDataAsync** e sostituire la query LINQ in `todoTable` con la query seguente:

             Items = await todoTable
                             .Where (todoItem => todoItem.Complete == false)
                            .Take(3)
                            .ToListAsync();

	Questa query restituisce i primi tre elementi non contrassegnati come completati. 

5.  Ricompilare e avviare l'app.

    Si noti che sono visualizzati solo i primi tre risultati della tabella TodoItem.

6.  Aggiornare nuovamente la query LINQ nel metodo **RefreshDataAsync** con il contenuto seguente:

             Items = await todoTable
                             .Where (todoItem => todoItem.Complete == false)
                             .Skip(3)
                             .Take(3)
                             .ToListAsync();

	Questa volta, impostare il valore **Skip** su 3. 

	Questa query ignora i primi tre risultati e restituisce i tre risultati successivi. In sostanza, si tratta della seconda "pagina" di dati, pagina la cui dimensione corrisponde a tre voci.

    **Nota**

    In questa esercitazione viene utilizzato uno scenario semplificato impostando valori di paging hardcoded per le proprietà **Skip** e **Take**. In un'app reale è possibile utilizzare query simili con un controllo pager o un'interfaccia utente paragonabile per consentire agli utenti di passare alle pagine precedenti e successive. È inoltre possibile chiamare il metodo **IncludeTotalCount** per ottenere il conteggio totale di tutti gli elementi disponibili nel server, insieme ai dati di paging.

Passaggi successivi
-------------------

Il set di esercitazioni relative alle nozioni di base dell'utilizzo dei dati in Servizi mobili è concluso. Per ulteriori informazioni, vedere anche il seguente argomento su Servizi mobili:

-   [Introduzione all'autenticazione](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-ios)
    Informazioni sull'autenticazione degli utenti dell'app con l'account Windows.

