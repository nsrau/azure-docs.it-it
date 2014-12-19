<properties urlDisplayName="" pageTitle="Aggiungere paging ai dati (Xamarin iOS) | Mobile Developer Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Xamarin iOS app from Mobile Services." metaCanonical="" services="mobile-services" authors="donnam" solutions="" manager="dwrede" editor="" title="Refine Mobile Services queries with paging" documentationCenter="Mobile" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="donnam" />


# Usare il paging per ridefinire le query di Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-add-paging-data](../includes/mobile-services-selector-add-paging-data.md)]

Questo argomento illustra come usare il paging per gestire la quantità di dati restituiti all'app per iOS da Servizi mobili di Azure. In questa esercitazione verranno usate le proprietà di query **Skip** e **Take** sul client per richiedere "pagine" di dati specifiche.

<div class="dev-callout"><b>Nota</b>
<p>Per evitare l'overflow dei dati nei client dei dispositivi mobili, Servizi mobili implementa un limite di pagina automatico, che prevede un massimo di 50 elementi in una risposta per impostazione predefinita. Specificando le dimensioni della pagina, è possibile richiedere in modo esplicito fino a un massimo di 1.000 elementi nella risposta.</p>
</div>

Questa esercitazione si basa sulle procedure e sull'app di esempio dell'esercitazione precedente [Introduzione ai dati]. Prima di iniziare questa esercitazione, è necessario completare almeno la prima esercitazione della serie relativa all'uso dei dati, [Introduzione ai dati]. 

1. In Xamarin Studio aprire il progetto modificato durante l'esercitazione [Introduzione ai dati].

2. Premere il pulsante **Run** per compilare il progetto e avviare l'app, quindi immettere il testo nella casella di testo e fare clic sull'icona con il segno più (**+**).

3. Ripetere il passaggio precedente almeno tre volte, in modo da avere più di tre elementi archiviati nella tabella TodoItem. 

4. Aprire il file **TodoService.cs**, individuare il metodo **RefreshDataAsync** e sostituire la query LINQ in <code>todoTable</code> con la query seguente: 

			Items = await todoTable
							.Where (todoItem => todoItem.Complete == false)
        		            .Take(3)
                		    .ToListAsync();

   	Questa query restituisce i primi tre elementi non contrassegnati come completati. 

5. Ricompilare e avviare l'app. 
   
    Si noti che sono visualizzati solo i primi tre risultati della tabella TodoItem. 

7. Aggiornare nuovamente la query LINQ nel metodo **RefreshDataAsync** con la query seguente:

			Items = await todoTable
							.Where (todoItem => todoItem.Complete == false)
		                    .Skip(3)
		                    .Take(3)
		                    .ToListAsync();

   	Questa volta, impostare il valore **Skip** su 3. 

Questa query ignora i primi tre risultati e restituisce i tre risultati successivi. In sostanza, si tratta della seconda "pagina" di dati le cui dimensioni corrispondono a tre voci.

    <div class="dev-callout"><b>Nota</b>
    <p>In questa esercitazione viene usato uno scenario semplificato impostando valori di paging hardcoded per le proprietà <strong>Skip</strong> e <strong>Take</strong>. In un'app reale è possibile usare query simili con un controllo pager o un'interfaccia utente paragonabile per consentire agli utenti di passare alle pagine precedenti e successive. È anche possibile chiamare il metodo <strong>IncludeTotalCount</strong> per ottenere il conteggio totale di tutti gli elementi disponibili nel server, insieme ai dati di paging.</p>
    </div>

## <a name="next-steps"> </a>Passaggi successivi

Il set di esercitazioni relative alle nozioni di base dell'uso dei dati in Servizi mobili è concluso. Per altre informazioni, vedere anche il seguente argomento su Servizi mobili:

* [Introduzione all'autenticazione]
  <br/>Informazioni sull'autenticazione degli utenti dell'app con un account Windows.
 
<!--
* [Get started with push notifications] 
  <br/>Informazioni sull'invio di una notifica push di base all'app.
-->

<!-- Anchors. -->

[Passaggi successivi]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[Introduzione a Servizi mobili]: ./it-it/develop/mobile/tutorials/get-started-xamarin-ios
[Introduzione ai dati]: /it-it/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Introduzione alle notifiche push]: /it-it/develop/mobile/tutorials/get-started-with-push-xamarin-ios

[Portale di gestione]: https://manage.windowsazure.com/
