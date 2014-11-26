<properties linkid="develop-mobile-tutorials-add-paging-to-data-android" urlDisplayName="Add paging to data" pageTitle="Add paging to data (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Android app from Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="ricksal" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal" />

# Utilizzo del paging per ridefinire le query di Servizi mobili

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/it-it/develop/mobile/tutorials/add-paging-to-data-dotnet" title="Windows Store C#">Windows Store C#</a><a href="/it-it/develop/mobile/tutorials/add-paging-to-data-js" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/it-it/develop/mobile/tutorials/add-paging-to-data-wp8" title="Windows Phone">Windows Phone</a><a href="/it-it/develop/mobile/tutorials/add-paging-to-data-ios" title="iOS">iOS</a><a href="/it-it/develop/mobile/tutorials/add-paging-to-data-android" title="Android" class="current">Android</a><a href="/it-it/develop/mobile/tutorials/add-paging-to-data-html" title="HTML">HTML</a><a href="/it-it/develop/mobile/tutorials/add-paging-to-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/it-it/develop/mobile/tutorials/add-paging-to-data-xamarin-android" title="Xamarin.Android">Xamarin.Android</a></div>

In questo argomento viene illustrato come utilizzare il paging per gestire la quantità di dati restituiti all'app per Android da Servizi mobili di Azure. In questa esercitazione verranno utilizzati i metodi di query **top** e **skip** sul client per richiedere "pagine" di dati specifiche.

<div class="dev-callout"><b>Nota</b>
<p>Per evitare l'overflow dei dati nei client dei dispositivi mobili, Servizi mobili implementa un limite di pagina automatico, che per impostazione predefinita prevede un massimo di 50 elementi in una risposta. Specificando la dimensione della pagina, &egrave; possibile richiedere in modo esplicito fino a un massimo di 1.000 elementi nella risposta.</p>
</div>

Questa esercitazione è basata sulle procedure e sull'app di esempio creata nell'esercitazione precedente [Introduzione ai dati][Introduzione ai dati]. Prima di iniziare questa esercitazione, è necessario completare almeno la prima esercitazione della serie relativa all'utilizzo dei dati, [Introduzione ai dati][Introduzione ai dati].

1.  In Eclipse aprire il progetto creato durante l'esercitazione [Introduzione ai dati][Introduzione ai dati].

2.  Scegliere **Run** dal menu **Run** per avviare l'app, immettere il testo nella casella di testo e fare clic sul pulsante **Add**.

3.  Ripetere il passaggio precedente almeno tre volte, in modo da avere più di tre elementi archiviati nella tabella TodoItem.

4.  Nel file ToDoActivity.java sostituire il metodo **RefreshTodoItems** esistente con il codice seguente:

        private void refreshItemsFromTable() {
            // Define a filtered query that returns the top 3 items.
            mToDoTable.where().field("complete").eq(false).top(3)
                    .execute(new TableQueryCallback<ToDoItem>() {

                        public void onCompleted(List<ToDoItem> result, int count,
                                Exception exception, ServiceFilterResponse response) {
                            if (exception == null) {
                                mAdapter.clear();

                                for (ToDoItem item : result) {
                                    mAdapter.add(item);
                                }

                            } else {
                                createAndShowDialog(exception, "Error");
                            }
                        }
                    });
        }

    Questa query restituisce i primi tre elementi non contrassegnati come completati.

5.  Ricompilare e avviare l'app.

    Si noti che sono visualizzati solo i primi tre risultati della tabella TodoItem.

6.  (Facoltativo) È possibile visualizzare l'URI della richiesta inviata al servizio mobile utilizzando un software di ispezione dei messaggi come gli strumenti di sviluppo per browser o [Fiddler].

    Si noti che il metodo `top(3)` è stato convertito nell'opzione di query `$top=3` nell'URI della query.

7.  Aggiornare nuovamente il metodo **RefreshTodoItems** con il codice seguente:

        private void refreshItemsFromTable() {
            // Define a filtered query that returns the top 3 items.
            mToDoTable.where().field("complete").eq(false).skip(3).top(3)
                    .execute(new TableQueryCallback<ToDoItem>() {

                        public void onCompleted(List<ToDoItem> result, int count,
                                Exception exception, ServiceFilterResponse response) {
                            if (exception == null) {
                                mAdapter.clear();

                                for (ToDoItem item : result) {
                                    mAdapter.add(item);
                                }

                            } else {
                                createAndShowDialog(exception, "Error");
                            }
                        }
                    });
        }

    Questa query ignora i primi tre risultati e restituisce i tre risultati successivi. In sostanza, si tratta della seconda "pagina" di dati, pagina la cui dimensione corrisponde a tre voci.

    <div class="dev-callout"><b>Nota</b>
<p>Nell'esercitazione lo scenario &egrave; stato semplificato con il passaggio di valori di paging hardcoded ai metodi <strong>top</strong> e <strong>skip</strong>. In un'app reale &egrave; possibile utilizzare query simili con un controllo pager o un'interfaccia utente paragonabile per consentire agli utenti di passare alle pagine precedenti e successive. &Egrave; inoltre possibile chiamare il metodo <strong>includeInlineCount</strong> per ottenere il conteggio totale degli elementi disponibili sul server, insieme ai dati di paging.</p>
</div>

8.  (Facoltativo) Anche in questo caso, è possibile visualizzare l'URI della richiesta inviata al servizio mobile.

    Si noti che il metodo `skip(3)` è stato convertito nell'opzione di query `$skip=3` nell'URI della query.

## <a name="next-steps"> </a>Passaggi successivi

Il set di esercitazioni relative alle nozioni di base dell'utilizzo dei dati in Servizi mobili è concluso. Per ulteriori informazioni, vedere anche i seguenti argomenti su Servizi mobili:

-   [Introduzione all'autenticazione][Introduzione all'autenticazione]
    Informazioni sull'autenticazione degli utenti dell'app con l'account Windows.

-   [Introduzione alle notifiche push][Introduzione alle notifiche push]
    Informazioni sull'invio di una notifica push di base all'app.

 

  [Introduzione ai dati]: /it-it/develop/mobile/tutorials/get-started-with-data-android
  [Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-android
  [Introduzione alle notifiche push]: /it-it/develop/mobile/tutorials/get-started-with-push-android
