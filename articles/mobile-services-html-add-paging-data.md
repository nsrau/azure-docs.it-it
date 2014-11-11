<properties linkid="develop-mobile-tutorials-add-paging-to-data-html" urlDisplayName="Add paging to data (HTML5)" pageTitle="Add paging to data (HTML 5) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your HTML app from Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Utilizzo del paging per ridefinire le query di Servizi mobili

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/it-it/develop/mobile/tutorials/add-paging-to-data-dotnet" title="Windows Store C#">Windows Store C#</a><a href="/it-it/develop/mobile/tutorials/add-paging-to-data-js" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/it-it/develop/mobile/tutorials/add-paging-to-data-wp8" title="Windows Phone">Windows Phone</a><a href="/it-it/develop/mobile/tutorials/add-paging-to-data-ios" title="iOS">iOS</a><a href="/it-it/develop/mobile/tutorials/add-paging-to-data-android" title="Android">Android</a><a href="/it-it/develop/mobile/tutorials/add-paging-to-data-html" title="HTML" class="current">HTML</a><a href="/it-it/develop/mobile/tutorials/add-paging-to-data-xamarin-ios" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/it-it/develop/mobile/tutorials/add-paging-to-data-xamarin-android" title="Xamarin.Android" class="current">Xamarin.Android</a>
</div>

In questo argomento viene illustrato come utilizzare il paging per gestire la quantità di dati restituiti all'app HTML da Servizi mobili di Azure. In questa esercitazione verranno utilizzati i metodi di query **Take** e **Skip** sul client per richiedere "pagine" di dati specifiche.

<div class="dev-callout"><b>Nota</b>
<p>Per evitare l'overflow dei dati nei client dei dispositivi mobili, Servizi mobili implementa un limite di pagina automatico, che per impostazione predefinita prevede un massimo di 50 elementi in una risposta. Specificando la dimensione della pagina, &egrave; possibile richiedere in modo esplicito fino a un massimo di 1.000 elementi nella risposta.</p>
</div>

Questa esercitazione è basata sulle procedure e sull'app di esempio creata nell'esercitazione precedente [Introduzione ai dati][Introduzione ai dati]. Prima di iniziare questa esercitazione, è necessario completare almeno la prima esercitazione della serie relativa all'utilizzo dei dati, [Introduzione ai dati][Introduzione ai dati].

1.  Eseguire uno dei seguenti file di comando dalla sottocartella **server** del progetto modificato al termine dell'esercitazione [Introduzione ai dati][Introduzione ai dati].

    -   **launch-windows** (computer Windows)
    -   **launch-mac.command** (computer Mac OS X)
    -   **launch-linux.sh** (computer Linux)

    <div class="dev-callout"><b>Nota</b>
    <p>In un computer Windows, digitare 'R' quando PowerShell chiede di confermare che si desidera eseguire lo script. Il Web browser potrebbe visualizzare un avviso in cui si consiglia di non eseguire lo script in quanto scaricato da Internet. In questo caso, &egrave; necessario richiedere che il browser proceda nel caricamento dello script.</p>
</div>

    Verrà quindi avviato un server Web nel computer locale per ospitare l'app.

2.  In un Web browser, passare a <http://localhost:8000/>, quindi digitare un testo in **Add new task** e fare clic su **Add**.

3.  Ripetere il passaggio precedente almeno tre volte, in modo da avere più di tre elementi archiviati nella tabella TodoItem.

4.  Nel file app.js sostituire la definizione della variabile `query` nel metodo **refreshTodoItems** con il codice seguente:

        var query = todoItemTable.where({ complete: false }).take(3);

    Questa query, se eseguita, restituisce i primi tre elementi non contrassegnati come completi.

5.  Tornare al Web browser e ricaricare la pagina.

    Si noti che sono visualizzati solo i primi tre risultati della tabella TodoItem.

6.  (Facoltativo) È possibile visualizzare l'URI della richiesta inviata al servizio mobile utilizzando un software di ispezione dei messaggi come gli strumenti di sviluppo per browser o [Fiddler].

    Si noti che il metodo **take(3)** è stato convertito nell'opzione di query **$top=3** nell'URI della query.

7.  Aggiornare la query ancora una volta con il codice seguente:

        var query = todoItemTable.where({ complete: false }).skip(3).take(3);

8.  Tornare al Web browser e ricaricare la pagina.

    Questa query ignora i primi tre risultati e restituisce i tre risultati successivi. In sostanza, si tratta della seconda "pagina" di dati, pagina la cui dimensione corrisponde a tre voci.

    <div class="dev-callout"><b>Nota</b>
<p>Nell'esercitazione, lo scenario &egrave; stato semplificato con il passaggio di valori di paging hardcoded ai metodi <strong>Take</strong> e <strong>Skip</strong>. In un'app reale &egrave; possibile utilizzare query simili con un controllo pager o un'interfaccia utente paragonabile per consentire agli utenti di passare alle pagine precedenti e successive.  &Egrave; inoltre possibile chiamare il metodo <strong>includeTotalCount</strong> per ottenere il conteggio totale degli elementi disponibili sul server, insieme ai dati di paging.</p>
</div>

9.  (Facoltativo) Anche in questo caso, è possibile visualizzare l'URI della richiesta inviata al servizio mobile.

    Si noti che il metodo **skip(3)** è stato convertito nell'opzione di query **$skip=3** nell'URI della query.

## <a name="next-steps"> </a>Passaggi successivi

Il set di esercitazioni relative alle nozioni di base dell'utilizzo dei dati in Servizi mobili è concluso. Ora si apprenderà come autenticare gli utenti dell'app in [Introduzione all'autenticazione][Introduzione all'autenticazione]. Per ulteriori informazioni su come utilizzare Servizi mobili con HTML/JavaScript, vedere [Riferimento per i concetti e le procedure di Servizi mobili con HTML/JavaScript][Riferimento per i concetti e le procedure di Servizi mobili con HTML/JavaScript]



  [Introduzione ai dati]: /it-it/develop/mobile/tutorials/get-started-with-data-html
  [Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-html
  [Riferimento per i concetti e le procedure di Servizi mobili con HTML/JavaScript]: /it-it/develop/mobile/how-to-guides/work-with-html-js-client
