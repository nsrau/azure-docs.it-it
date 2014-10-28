<properties linkid="develop-mobile-tutorials-add-paging-to-data-xamarin-ios" urlDisplayName="" pageTitle="Add paging to data (Xamarin iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Xamarin iOS app from Mobile Services." metaCanonical="" services="" authors="donnam" solutions="" manager="dwrede" editor="" title="Refine Mobile Services queries with paging" documentationCenter="Mobile" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam"></tags>

# Utilizzo del paging per ridefinire le query di Servizi mobili

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/it-it/develop/mobile/tutorials/add-paging-to-data-dotnet" title="Windows Store C#">Windows Store C#</a><a href="/it-it/develop/mobile/tutorials/add-paging-to-data-js" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/it-it/develop/mobile/tutorials/add-paging-to-data-wp8" title="Windows Phone">Windows Phone</a><a href="/it-it/develop/mobile/tutorials/add-paging-to-data-ios" title="iOS">iOS</a><a href="/it-it/develop/mobile/tutorials/add-paging-to-data-android" title="Android">Android</a><a href="/it-it/develop/mobile/tutorials/add-paging-to-data-html" title="HTML">HTML</a><a href="/it-it/develop/mobile/tutorials/add-paging-to-data-xamarin-ios" title="Xamarin.iOS" class="current">iOS C#</a><a href="/it-it/develop/mobile/tutorials/add-paging-to-data-xamarin-android" title="Xamarin.Android">Android C#</a>
</div>

In questo argomento viene illustrato come utilizzare il paging per gestire la quantità di dati restituiti all'app per iOS da Servizi mobili di Azure. In questa esercitazione verranno utilizzate le proprietà di query **Skip** e **Take** sul client per richiedere "pagine" di dati specifiche.

<div class="dev-callout"><b>Nota</b>
<p>Per evitare l'overflow dei dati nei client dei dispositivi mobili, Servizi mobili implementa un limite di pagina automatico, che per impostazione predefinita prevede un massimo di 50 elementi in una risposta. Specificando la dimensione della pagina, &egrave; possibile richiedere in modo esplicito fino a un massimo di 1.000 elementi nella risposta.</p>
</div>

Questa esercitazione è basata sulle procedure e sull'app di esempio creata nell'esercitazione precedente [Introduzione ai dati][]. Prima di iniziare questa esercitazione, è necessario completare almeno la prima esercitazione della serie relativa all'utilizzo dei dati, [Introduzione ai dati][].

1.  In Xamarin Studio aprire il progetto modificato durante l'esercitazione [Introduzione ai dati][].

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

    <div class="dev-callout"><b>Nota</b>
<p>In questa esercitazione viene utilizzato uno scenario semplificato impostando valori di paging hardcoded per le propriet&agrave; <strong>Skip</strong> e <strong>Take</strong>. In un'app reale &egrave; possibile utilizzare query simili con un controllo pager o un'interfaccia utente paragonabile per consentire agli utenti di passare alle pagine precedenti e successive. &Egrave; inoltre possibile chiamare il metodo <strong>IncludeTotalCount</strong> per ottenere il conteggio totale di tutti gli elementi disponibili nel server, insieme ai dati di paging.</p>
</div>

## <a name="next-steps"> </a>Passaggi successivi

Il set di esercitazioni relative alle nozioni di base dell'utilizzo dei dati in Servizi mobili è concluso. Per ulteriori informazioni, vedere anche il seguente argomento su Servizi mobili:

-   [Introduzione all'autenticazione][]
    Informazioni sull'autenticazione degli utenti dell'app con l'account Windows.

<!-- * [Get started with push notifications]    <br/>Learn how to send a very basic push notification to your app. --> <!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [Windows Store C#]: /it-it/develop/mobile/tutorials/add-paging-to-data-dotnet "Windows Store C#"
  [Windows Store JavaScript]: /it-it/develop/mobile/tutorials/add-paging-to-data-js "Windows Store JavaScript"
  [Windows Phone]: /it-it/develop/mobile/tutorials/add-paging-to-data-wp8 "Windows Phone"
  [iOS]: /it-it/develop/mobile/tutorials/add-paging-to-data-ios "iOS"
  [Android]: /it-it/develop/mobile/tutorials/add-paging-to-data-android "Android"
  [HTML]: /it-it/develop/mobile/tutorials/add-paging-to-data-html "HTML"
  [iOS C#]: /it-it/develop/mobile/tutorials/add-paging-to-data-xamarin-ios "Xamarin.iOS"
  [Android C#]: /it-it/develop/mobile/tutorials/add-paging-to-data-xamarin-android "Xamarin.Android"
  [Introduzione ai dati]: /it-it/develop/mobile/tutorials/get-started-with-data-xamarin-ios
  [Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-xamarin-ios
