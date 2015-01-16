<properties urlDisplayName="Add paging to data" pageTitle="Aggiungere paging ai dati (Xamarin Android) | Mobile Developer Center" metaKeywords="" description="Informazioni su come usare il paging per gestire la quantità di dati restituiti all'app per Xamarin Android da Servizi mobili."  services="mobile-services" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="donnam" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="09/25/2014" ms.author="donnam" />

# Usare il paging per ridefinire le query di Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-add-paging-data](../includes/mobile-services-selector-add-paging-data.md)]


Questo argomento illustra come usare il paging per gestire la quantità di dati restituiti all'app di Xamarin.Android da Servizi mobili di Azure. In questa esercitazione verranno usati i metodi di query **Take** e **Skip** sul client per richiedere "pagine" di dati specifiche.

<div class="dev-callout"><b>Nota</b>
<p>Per evitare l'overflow dei dati nei client dei dispositivi mobili, Servizi mobili implementa un limite di pagina automatico, che prevede un massimo di 50 elementi in una risposta per impostazione predefinita. Specificando le dimensioni della pagina, è possibile richiedere in modo esplicito fino a un massimo di 1.000 elementi nella risposta.</p>
</div>

Questa esercitazione si basa sulle procedure e sull'app di esempio dell'esercitazione precedente [Introduzione ai dati]. Prima di iniziare questa esercitazione, è necessario completare almeno la prima esercitazione della serie relativa all'uso dei dati, [Introduzione ai dati]. 

1. In Xamarin Studio aprire il progetto creato dopo avere completato l'esercitazione [Introduzione ai dati].

2. Fare clic su **Run** per avviare l'app, immettere il testo nella casella di testo e fare clic sul pulsante **Add**.

3. Ripetere il passaggio precedente almeno tre volte, in modo da avere più di tre elementi archiviati nella tabella TodoItem. 

4. Nel file **TodoActivity.cs** sostituire la query LINQ nel metodo **RefreshItemsFromTableAsync** con la query seguente:

		var list = await todoTable.Where(item => item.Complete == false)
						          .Take(3)
			                      .ToListAsync();

	  Questa query restituisce i primi tre elementi non contrassegnati come completati.

5. Ricompilare e avviare l'app. 
   
    Si noti che sono visualizzati solo i primi tre risultati della tabella TodoItem. 

6. (Facoltativo) È possibile visualizzare l'URI della richiesta inviata al servizio mobile usando un software di ispezione dei messaggi come gli strumenti di sviluppo per browser o [Fiddler]. 

   	Si noti che il metodo `Take(3)` è stato convertito nell'opzione di query `$top=3` nell'URI della query.

7. Aggiornare nuovamente la query LINQ nel metodo **RefreshItemsFromTableAsync** con la query seguente:
            
			var list = await todoTable.Where(item => item.Complete == false)
							          .Skip(3)
				                      .Take(3)
                 				      .ToListAsync();

   	Questa query ignora i primi tre risultati e restituisce i tre risultati successivi. In sostanza, si tratta della seconda "pagina" di dati le cui dimensioni corrispondono a tre voci.

    <div class="dev-callout"><b>Nota</b>
    <p>Nell'esercitazione, lo scenario è stato semplificato con il passaggio di valori di paging hardcoded ai metodi <strong>Take</strong> e <strong>Skip</strong>. In un'app reale è possibile usare query simili con un controllo pager o un'interfaccia utente paragonabile per consentire agli utenti di passare alle pagine precedenti e successive. È anche possibile chiamare il metodo <strong>IncludeTotalCount</strong> per ottenere il conteggio totale degli elementi disponibili sul server, insieme ai dati di paging.</p>
    </div>

8. (Facoltativo) Anche in questo caso, è possibile visualizzare l'URI della richiesta inviata al servizio mobile. 

   	Si noti che il metodo `Skip(3)` è stato convertito nell'opzione di query `$skip=3` nell'URI della query.

## <a name="next-steps"> </a>Passaggi successivi

Il set di esercitazioni relative alle nozioni di base dell'uso dei dati in Servizi mobili è concluso. Per altre informazioni, vedere anche i seguenti argomenti su Servizi mobili:

* [Introduzione all'autenticazione]
  <br/>Informazioni sull'autenticazione degli utenti dell'app con un account Windows.
 
* [Introduzione alle notifiche push] 
  <br/>Informazioni sull'invio di una notifica push di base all'app.

<!-- Anchors. -->

[Passaggi successivi]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started-xamarin-android
[Introduzione ai dati]: /it-it/develop/mobile/tutorials/get-started-with-data-xamarin-android
[Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-xamarin-android
[Introduzione alle notifiche push]: /it-it/develop/mobile/tutorials/get-started-with-push-xamarin-android

[Portale di gestione]: https://manage.windowsazure.com/

