<properties linkid="develop-mobile-tutorials-add-paging-to-data-android" urlDisplayName="Add paging to data" pageTitle="Add paging to data (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Android app from Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="" solutions="" manager="" editor="" />

Utilizzo del paging per ridefinire le query di Servizi mobili
=============================================================

[Windows Store C\#](/en-us/develop/mobile/tutorials/add-paging-to-data-dotnet "Windows Store C#")[Windows Store JavaScript](/en-us/develop/mobile/tutorials/add-paging-to-data-js "Windows Store JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/add-paging-to-data-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/add-paging-to-data-ios "iOS")[Android](/en-us/develop/mobile/tutorials/add-paging-to-data-android "Android")[HTML](/en-us/develop/mobile/tutorials/add-paging-to-data-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-android "Xamarin.Android")

In questo argomento viene illustrato come utilizzare il paging per gestire la quantità di dati restituiti all'app per Android da Servizi mobili di Azure. In questa esercitazione verranno utilizzati i metodi di query **top** e **skip** sul client per richiedere "pagine" di dati specifiche.

**Nota**

Per evitare l'overflow dei dati nei client dei dispositivi mobili, Servizi mobili implementa un limite di pagina automatico, che per impostazione predefinita prevede un massimo di 50 elementi in una risposta. Specificando la dimensione della pagina, è possibile richiedere in modo esplicito fino a un massimo di 1.000 elementi nella risposta.

Questa esercitazione è basata sulle procedure e sull'app di esempio creata nell'esercitazione precedente [Introduzione ai dati](/en-us/develop/mobile/tutorials/get-started-with-data-android). Prima di iniziare questa esercitazione, è necessario completare almeno la prima esercitazione della serie relativa all'utilizzo dei dati, [Introduzione ai dati](/en-us/develop/mobile/tutorials/get-started-with-data-android).

1.  In Eclipse aprire il progetto creato durante l'esercitazione [Introduzione ai dati](/en-us/develop/mobile/tutorials/get-started-with-data-android).

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

1.  Ricompilare e avviare l'app.

    Si noti che sono visualizzati solo i primi tre risultati della tabella TodoItem.

2.  (Facoltativo) È possibile visualizzare l'URI della richiesta inviata al servizio mobile utilizzando un software di ispezione dei messaggi come gli strumenti di sviluppo per browser o [Fiddler].

   	Si noti che il metodo `top(3)` è stata convertito nell'opzione di query `$top=3` nell'URI della query.

3.  Aggiornare nuovamente il metodo **RefreshTodoItems** con il codice seguente:

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

    **Nota**

    Nell'esercitazione lo scenario è stato semplificato con il passaggio di valori di paging hardcoded ai metodi **top** e **skip**. In un'app reale è possibile utilizzare query simili con un controllo pager o un'interfaccia utente paragonabile per consentire agli utenti di passare alle pagine precedenti e successive. È inoltre possibile chiamare il metodo **includeInlineCount** per ottenere il conteggio totale degli elementi disponibili sul server, insieme ai dati di paging.

4.  (Facoltativo) Anche in questo caso, è possibile visualizzare l'URI della richiesta inviata al servizio mobile.

   	Si noti che il metodo `skip(3)` è stato convertito nell'opzione di query `$skip=3` nell'URI della query.

Passaggi successivi
-------------------

Il set di esercitazioni relative alle nozioni di base dell'utilizzo dei dati in Servizi mobili è concluso. Per ulteriori informazioni, vedere anche i seguenti argomenti su Servizi mobili:

-   [Introduzione all'autenticazione](/en-us/develop/mobile/tutorials/get-started-with-users-android)

    Informazioni sull'autenticazione degli utenti dell'app con l'account Windows.

-   [Introduzione alle notifiche push](/en-us/develop/mobile/tutorials/get-started-with-push-android)

    Informazioni sull'invio di una notifica push di base all'app.


