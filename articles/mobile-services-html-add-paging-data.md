<properties 
	pageTitle="Aggiungere paging ai dati (HTML 5) | Mobile Dev Center" 
	description="Informazioni su come usare il paging per gestire la quantità di dati restituiti all'app HTML da Servizi mobili." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-html" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="09/24/2014" 
	ms.author="glenga"/>

# Usare il paging per ridefinire le query di Servizi mobili

[AZURE.INCLUDE [mobile-services-selector-add-paging-data](../includes/mobile-services-selector-add-paging-data.md)]

Questo argomento descrive come usare il paging per gestire la quantità di dati restituiti all'app HTML da Servizi mobili di Azure. In questa esercitazione verranno usati i metodi di query **Take** e **Skip** sul client per richiedere "pagine" di dati specifiche.

> [AZURE.NOTE] Per evitare l'overflow dei dati nei client dei dispositivi mobili, Servizi mobili implementa un limite di pagina automatico, che per impostazione predefinita prevede un massimo di 50 elementi in una risposta. Specificando la dimensione della pagina, è possibile richiedere in modo esplicito fino a un massimo di 1.000 elementi nella risposta.

Questa esercitazione è basata sulle procedure e sull'app di esempio creata nell'esercitazione precedente [Introduzione ai dati]. Prima di iniziare questa esercitazione, è necessario completare almeno la prima esercitazione della serie relativa all'uso dei dati, [Introduzione ai dati]. 

1. Eseguire uno dei seguenti file di comando dalla sottocartella **server** del progetto modificato al termine dell'esercitazione [Introduzione ai dati].

	+ **launch-windows** (computer Windows) 
	+ **launch-mac.command** (computer Mac OS X)
	+ **launch-linux.sh** (computer Linux)

	> [AZURE.NOTE] In un computer Windows, digitare 'R' quando PowerShell chiede di confermare che si desidera eseguire lo script. Il Web browser potrebbe visualizzare un avviso in cui si consiglia di non eseguire lo script in quanto scaricato da Internet. In questo caso, è necessario richiedere che il browser proceda nel caricamento dello script.

	Verrà quindi avviato un server Web nel computer locale per ospitare l'app.

1. In un Web browser, passare a <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a>, quindi digitare un testo in **Add new task** e fare clic su **Add**.

3. Ripetere il passaggio precedente almeno tre volte, in modo da avere più di tre elementi archiviati nella tabella TodoItem. 

2. Nel file app.js sostituire la definizione della variabile  `query` nel metodo **refreshTodoItems** con il codice seguente:

       
        var query = todoItemTable.where({ complete: false }).take(3);

  	Questa query, se eseguita, restituisce i primi tre elementi non contrassegnati come completi.

3. Tornare al Web browser e ricaricare la pagina.

  	Si noti che sono visualizzati solo i primi tre risultati della tabella TodoItem. 

4. (Facoltativo) È possibile visualizzare l'URI della richiesta inviata al servizio mobile usando un software di ispezione dei messaggi come gli strumenti di sviluppo per browser [Fiddler]. 

   	Si noti che il metodo **take(3)** è stato convertito nell'opzione di query **$top=3** nell'URI della query.

5. Aggiornare la query ancora una volta con il codice seguente:
            
        var query = todoItemTable.where({ complete: false }).skip(3).take(3);

3. Tornare al Web browser e ricaricare la pagina.

   	Questa query ignora i primi tre risultati e restituisce i tre risultati successivi. In sostanza, si tratta della seconda "pagina" di dati le cui dimensioni corrispondono a tre voci.

    > [AZURE.NOTE] Nell'esercitazione lo scenario è stato semplificato con il passaggio di valori di paging hardcoded ai metodi **Take** e **Skip**. In un'app reale è possibile usare query simili con un controllo pager o un'interfaccia utente paragonabile per consentire agli utenti di passare alle pagine precedenti e successive.  È anche possibile chiamare il metodo **includeTotalCount** per ottenere il conteggio totale degli elementi disponibili sul server, insieme ai dati di paging.

6. (Facoltativo) Anche in questo caso, è possibile visualizzare l'URI della richiesta inviata al servizio mobile. 

   	Si noti che il metodo **skip(3)** è stato convertito nell'opzione di query **$skip=3** nell'URI della query.

## <a name="next-steps"> </a>Passaggi successivi

Il set di esercitazioni relative alle nozioni di base dell'uso dei dati in Servizi mobili è concluso. Ora si apprenderà come autenticare gli utenti dell'app in [Introduzione all'autenticazione]. Per altre informazioni su come usare Servizi mobili con HTML/JavaScript, vedere [Riferimento per i concetti e le procedure di HTML/JavaScript per Servizi mobili]

<!-- Anchors. -->

[Passaggi successivi]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started-html
[Introduzione ai dati]: /it-it/develop/mobile/tutorials/get-started-with-data-html
[Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-html


[Portale di gestione]: https://manage.windowsazure.com/
[Riferimento per i concetti e le procedure di Servizi mobili con HTML/JavaScript]: /it-it/develop/mobile/how-to-guides/work-with-html-js-client



<!--HONumber=42-->
