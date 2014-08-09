<properties pageTitle="Service-side authorization (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to authorize users in the JavaScript backend of Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Service-side authorization of Mobile Services users" authors="glenga" solutions="" manager="" editor="" />

Autorizzazione lato servizio degli utenti di Servizi mobili
===========================================================

[Windows Store C\#](/it-it/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts "Windows Store C#")[Windows Store JavaScript](/it-it/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts "Windows Store JavaScript")[Windows Phone](/it-it/documentation/articles/mobile-services-windows-phone-authorize-users-in-scripts "Windows Phone")[iOS](/it-it/documentation/articles/mobile-services-ios-authorize-users-in-scripts "iOS")[Android](/it-it/documentation/articles/mobile-services-android-authorize-users-in-scripts "Android")[HTML](/it-it/documentation/articles/mobile-services-html-authorize-users-in-scripts "HTML")[Xamarin.iOS](/it-it/documentation/articles/partner-xamarin-mobile-services-ios-authorize-users-in-scripts "Xamarin.iOS")[Xamarin.Android](/it-it/documentation/articles/partner-xamarin-mobile-services-android-authorize-users-in-scripts "Xamarin.Android")
[Back-end .NET](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts/ "Back-end .NET") | [Back-end JavaScript](/it-it/documentation/articles/mobile-services-windows-phone-authorize-users-in-scripts/ "Back-end JavaScript")

In questo argomento viene illustrato come utilizzare gli script del server per autorizzare gli utenti autenticati per accedere ai dati in Servizi mobili di Azure da un'app per Windows Phone 8. In questa esercitazione verranno registrati gli script con Servizi Mobili per filtrare le query in base all'ID utente di un utente autenticato, per garantire che ogni utente possa visualizzare solo i relativi dati.

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili e sull'esercitazione precedente [Introduzione all'autenticazione](/en-us/develop/mobile/tutorials/get-started-with-users-wp8). Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione all'autenticazione](/en-us/develop/mobile/tutorials/get-started-with-users-wp8).

[guarda l'esercitazione](http://go.microsoft.com/fwlink/?LinkId=298630) [Riproduci video](http://go.microsoft.com/fwlink/?LinkId=298630) 15:00

Registrazione di script
-----------------------

Poiché l'app di guida introduttiva legge e inserisce i dati, è necessario registrare gli script per queste operazioni sulla tabella TodoItem.

1.  Accedere al [portale di gestione di Azure](https://manage.windowsazure.com/), fare clic su **Mobile Services** e quindi sull'app.

   	![][0]

2.  Fare clic sulla scheda **Data** e quindi sulla tabella **TodoItem**.

   	![][1]

3.  Fare clic su **Script** e quindi selezionare l'operazione **Insert**.

   	![][2]

4.  Sostituire lo script esistente con la funzione seguente e quindi fare clic su **Save**.

         function insert(item, user, request) {
           item.userId = user.userId;    
           request.execute();
         }

    Questo script aggiunge un valore userId, ovvero l'ID utente dell'utente autenticato, all'elemento prima che venga inserito nella tabella TodoItem.

    **Nota**

    È necessario abilitare lo schema dinamico la prima volta che viene eseguito lo script insert. Quando è abilitato lo schema dinamico, Servizi mobili aggiunge automaticamente la colonna **userId** alla tabella **TodoItem** alla prima esecuzione. Lo schema dinamico è abilitato per impostazione predefinita per un nuovo servizio mobile e deve essere disabilitato prima che l'app venga pubblicata in Windows Phone Store.

5.  Ripetere i passaggi 3 e 4 per sostituire l'operazione **Read** esistente con la funzione seguente:

         function read(query, user, request) {
            query.where({ userId: user.userId });    
            request.execute();
         }

   	Questo script consente di filtrare gli oggetti TodoItem restituiti, in modo che ogni utente riceva solo gli elementi inseriti personalmente.

Test dell'app
-------------

1.  In Visual Studio 2012 Express per Windows Phone aprire il progetto modificato dopo avere completato l'esercitazione [Introduzione all'autenticazione](/en-us/develop/mobile/tutorials/get-started-with-users-wp8).

2.  Premere F5 per eseguire l'app e accedervi con il provider di identità desiderato.

   	Si noti che questa volta, sebbene nel corso delle esercitazioni precedenti siano stati aggiunti elementi nella tabella TodoItem, non viene restituito alcun elemento. Questo si verifica perché gli elementi precedenti sono stati inseriti senza la colonna userId e ora presentano valori Null.

3.  Nell'app digitare un testo nella casella di testo e quindi fare clic sul pulsante **Save**.

   	![][3]

   	Testo e userId verranno inseriti nella tabella TodoItem nel servizio mobile. Poiché il nuovo elemento contiene il valore userId corretto, viene restituito dal servizio mobile.

4.  Tornare alla tabella **todoitem** nel [portale di gestione](https://manage.windowsazure.com/), quindi fare clic su **Browse** e verificare che a ogni elemento appena aggiunto sia associato un valore userId.

Passaggi successivi
-------------------

L'esercitazione sulle nozioni di base dell'utilizzo dell'autenticazione è terminata. Per ulteriori informazioni, vedere anche i seguenti argomenti su Servizi mobili:

-   [Introduzione ai dati](/en-us/develop/mobile/tutorials/get-started-with-data-wp8)

    Ulteriori informazioni sull'archiviazione e sulle query dei dati mediante Servizi mobili.

-   [Introduzione alle notifiche push](/en-us/develop/mobile/tutorials/get-started-with-push-wp8)

    Informazioni sull'invio di una notifica push di base all'app.

-   [Riferimento per gli script del server di Servizi mobili](http://go.microsoft.com/fwlink/?LinkId=262293)

    Ulteriori informazioni sulla registrazione e l'utilizzo di script del server.

<!-- Images. -->
[0]: ./media/mobile-services-windows-phone-authorize-users-in-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-windows-phone-authorize-users-in-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-windows-phone-authorize-users-in-scripts/mobile-insert-script-users.png
[3]: ./media/mobile-services-windows-phone-authorize-users-in-scripts/mobile-quickstart-startup-wp8.png
