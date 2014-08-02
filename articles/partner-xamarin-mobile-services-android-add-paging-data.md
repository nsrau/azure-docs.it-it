<properties  linkid="develop-mobile-tutorials-add-paging-to-data-xamarin-android" urlDisplayName="Add paging to data" pageTitle="Add paging to data (Xamarin Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Xamarin Android app from Mobile Services." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="" />

# Utilizzo del paging per ridefinire le query di Servizi mobili

<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/en-us/develop/mobile/tutorials/add-paging-to-data-dotnet" title="Windows Store C#">Windows Store C#</a><a href="/en-us/develop/mobile/tutorials/add-paging-to-data-js" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/en-us/develop/mobile/tutorials/add-paging-to-data-wp8" title="Windows Phone">Windows Phone</a><a href="/en-us/develop/mobile/tutorials/add-paging-to-data-ios" title="iOS">iOS</a><a href="/en-us/develop/mobile/tutorials/add-paging-to-data-android" title="Android">Android</a><a href="/en-us/develop/mobile/tutorials/add-paging-to-data-html" title="HTML">HTML</a><a href="/en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-ios" title="Xamarin.iOS">iOS C#</a><a href="/en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-android" title="Xamarin.Android" class="current">Android C#</a>
</div>

In questo argomento viene illustrato come utilizzare il paging per gestire la quantità di dati restituiti all'app per Xamarin.Android da Servizi mobili di Azure. In questa esercitazione verranno utilizzati i metodi di query **Take** e **Skip** sul client per richiedere "pagine" di dati specifiche.


**Nota**
Per evitare l'overflow dei dati nei client dei dispositivi mobili, Servizi mobili implementa un limite di pagina automatico, che per impostazione predefinita prevede un massimo di 50 elementi in una risposta. Specificando la dimensione della pagina, è possibile richiedere in modo esplicito fino a un massimo di 1.000 elementi nella risposta.


Questa esercitazione è basata sulle procedure e sull'app di esempio creata nell'esercitazione precedente [Introduzione ai dati](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-android). Prima di iniziare questa esercitazione, è necessario completare almeno la prima esercitazione della serie relativa all'utilizzo dei dati, [Introduzione ai dati](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-android).

1.  In Xamarin Studio aprire il progetto creato dopo avere completato l'esercitazione [Introduzione ai dati](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-android).

2.  Fare clic su **Run** per avviare l'app, immettere il testo nella casella di testo e fare clic sul pulsante **Add**.

3.  Ripetere il passaggio precedente almeno tre volte, in modo da avere più di tre elementi archiviati nella tabella TodoItem.

4.  Nel file **TodoActivity.cs** sostituire la query LINQ nel metodo **RefreshItemsFromTableAsync** con la query seguente:
    
         var list = await todoTable.Where(item => item.Complete == false)
                                   .Take(3)
                                   .ToListAsync();
    
    Questa query restituisce i primi tre elementi non contrassegnati come completati.

5.  Ricompilare e avviare l'app.
    
    Si noti che sono visualizzati solo i primi tre risultati della tabella TodoItem.

6.  (Facoltativo) È possibile visualizzare l'URI della richiesta inviata al servizio mobile utilizzando un software di ispezione dei messaggi come gli strumenti di sviluppo per browser o [Fiddler].
    
	Si noti che il metodo `Take(3)` è stato convertito nell'opzione di query `$top=3` nell'URI della query.

7.  Aggiornare nuovamente la query LINQ nel metodo **RefreshItemsFromTableAsync** con la query seguente:
    
             var list = await todoTable.Where(item => item.Complete == false)
                                       .Skip(3)
                                       .Take(3)
                  				      .ToListAsync();
        
	Questa query ignora i primi tre risultati e restituisce i tre risultati successivi. In sostanza, si tratta della seconda "pagina" di dati, pagina la cui dimensione corrisponde a tre voci.
    


	**Nota**
	
	Nell'esercitazione, lo scenario è stato semplificato con il passaggio di valori di paging hardcoded ai metodi **Take** e **Skip**. In un'app reale è possibile utilizzare query simili con un controllo pager o un'interfaccia utente paragonabile per consentire agli utenti di passare alle pagine precedenti e successive. È inoltre possibile chiamare il metodo **IncludeTotalCount** per ottenere il conteggio totale degli elementi disponibili sul server, insieme ai dati di paging.


8. (Facoltativo) Anche in questo caso, è possibile visualizzare l'URI della richiesta inviata al servizio mobile.

	Si noti che il metodo `Skip(3)` è stato convertito nell'opzione di query `$skip=3` nell'URI della query.

## <a name="next-steps"> </a>Passaggi successivi

Il set di esercitazioni relative alle nozioni di base dell'utilizzo dei dati in Servizi mobili è concluso. Per ulteriori informazioni, vedere anche i seguenti argomenti su Servizi mobili:

* [Introduzione all'autenticazione](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-android)
  
  Informazioni sull'autenticazione degli utenti dell'app con l'account Windows.

* [Introduzione alle notifiche push](/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-android)
  
  Informazioni sull'invio di una notifica push di base all'app.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->