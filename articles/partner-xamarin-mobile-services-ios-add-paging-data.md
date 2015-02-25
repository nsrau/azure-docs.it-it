<properties 
	pageTitle="Aggiunta del paging ai dati (Xamarin iOS) (Xamarin iOS) | Mobile Developer Center" 
	description="Informazioni su come usare il paging per gestire la quantità di dati restituiti all'app per Xamarin iOS da Servizi mobili." 
	services="mobile-services" 
	authors="lindydonna" 
	manager="dwrede" 
	editor="" 
	documentationCenter="xamarin"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/24/2014" 
	ms.author="donnam"/>


# Uso del paging per ridefinire le query di Servizi mobili

[AZURE.INCLUDE [mobile-services-selector-add-paging-data](../includes/mobile-services-selector-add-paging-data.md)]

Questo argomento illustra come usare il paging per gestire la quantità di dati restituiti all'app per iOS da Servizi mobili di Azure. In questa esercitazione verranno usate le proprietà di query **Skip** e **Take** sul client per richiedere "pagine" di dati specifiche.

> [AZURE.NOTE] Per evitare l'overflow dei dati nei client dei dispositivi mobili, Servizi mobili implementa un limite di pagina automatico, che per impostazione predefinita prevede un massimo di 50 elementi in una risposta. Specificando la dimensione della pagina, è possibile richiedere in modo esplicito fino a un massimo di 1.000 elementi nella risposta.

Questa esercitazione è basata sulle procedure e sull'app di esempio creata nell'esercitazione precedente [Introduzione ai dati]. Prima di iniziare questa esercitazione, è necessario completare almeno la prima esercitazione della serie relativa all'uso dei dati, [Introduzione ai dati]. 

1. In Xamarin Studio aprire il progetto modificato durante l'esercitazione [Introduzione ai dati].

2. Premere il pulsante **Run** per compilare il progetto e avviare l'app, quindi immettere il testo nella casella di testo e fare clic sull'icona con il segno più (**+**).

3. Ripetere il passaggio precedente almeno tre volte, in modo da avere più di tre elementi archiviati nella tabella TodoItem. 

4. Aprire il file **TodoService.cs** individuare il metodo **RefreshDataAsync** e sostituire la query LINQ in <code>todoTable</code> con la query seguente: 

			Items = await todoTable
							.Where (todoItem => todoItem.Complete == false)
        		            .Take(3)
                		    .ToListAsync();

   	Questa query restituisce i primi tre elementi non contrassegnati come completati. 

5. Ricompilare e avviare l'app. 
   
    Si noti che sono visualizzati solo i primi tre risultati della tabella TodoItem. 

7. Aggiornare nuovamente la query LINQ nel metodo **RefreshDataAsync** con il contenuto seguente:

			Items = await todoTable
							.Where (todoItem => todoItem.Complete == false)
		                    .Skip(3)
		                    .Take(3)
		                    .ToListAsync();

   	Questa volta, impostare il valore **Skip** su 3. 

   	Questa query ignora i primi tre risultati e restituisce i tre risultati successivi. In sostanza, si tratta della seconda "pagina" di dati le cui dimensioni corrispondono a tre voci.

    > [AZURE.NOTE] In questa esercitazione viene usato uno scenario semplificato impostando valori di paging hardcoded per le proprietà **Skip** e **Take**. In un'app reale è possibile usare query simili con un controllo pager o un'interfaccia utente paragonabile per consentire agli utenti di passare alle pagine precedenti e successive. È inoltre possibile chiamare il metodo **IncludeTotalCount** per ottenere il conteggio totale di tutti gli elementi disponibili nel server, insieme ai dati di paging.

## <a name="next-steps"> </a>Passaggi successivi

Il set di esercitazioni relative alle nozioni di base dell'uso dei dati in Servizi mobili è concluso. Per ulteriori informazioni, vedere anche il seguente argomento su Servizi mobili:

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
[Introduzione a Servizi mobili]: ./it-it/develop/mobile/tutorials/get-started-xamarin-ios
[Introduzione ai dati]: /it-it/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Introduzione alle notifiche push]: /it-it/develop/mobile/tutorials/get-started-with-push-xamarin-ios

[Portale di gestione]: https://manage.windowsazure.com/


<!--HONumber=42-->
