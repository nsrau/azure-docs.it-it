<properties pageTitle="Service-side authorization (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to authorize users in the JavaScript backend of Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Service-side authorization of Mobile Services users" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Autorizzazione lato servizio degli utenti di Servizi mobili

<div class="dev-center-tutorial-selector sublanding">

[Windows Store C#][Windows Store C#][Windows Store JavaScript][Windows Store JavaScript][Windows Phone][Windows Phone][iOS][iOS][Android][Android][HTML][HTML][Xamarin.iOS][Xamarin.iOS][Xamarin.Android][Xamarin.Android]

</div>

<div class="dev-center-tutorial-subselector">

[Back-end .NET][Back-end .NET] | [Back-end JavaScript][Back-end JavaScript]

</div>

In questo argomento viene illustrato come utilizzare script del server per autorizzare gli utenti autenticati ad accedere ai dati in Servizi mobili di Azure da un'app di Windows Store. In questa esercitazione verranno registrati gli script con Servizi Mobili per filtrare le query in base all'ID utente di un utente autenticato, per garantire che ogni utente possa visualizzare solo i relativi dati.

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili e sull'esercitazione precedente [Introduzione all'autenticazione][Introduzione all'autenticazione]. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione all'autenticazione][Introduzione all'autenticazione].

## <a name="register-scripts"></a>Registrazione di script

Poiché l'app di guida introduttiva legge e inserisce i dati, è necessario registrare gli script per queste operazioni sulla tabella TodoItem.

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure], fare clic su **Mobile Services** e quindi sull'app.

    ![][]

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

    <div class="dev-callout">

    **Nota**
    È necessario abilitare lo schema dinamico la prima volta che viene eseguito lo script insert. Quando è abilitato lo schema dinamico, Servizi mobili aggiunge automaticamente la colonna **userId** alla tabella **TodoItem** alla prima esecuzione. Lo schema dinamico è abilitato per impostazione predefinita per un nuovo servizio mobile e deve essere disabilitato prima che l'app venga pubblicata in Windows Store.

    </div>

5.  Ripetere i passaggi 3 e 4 per sostituire l'operazione **Read** esistente con la funzione seguente:

        function read(query, user, request) {
           query.where({ userId: user.userId });    
           request.execute();
        }

    Questo script consente di filtrare gli oggetti TodoItem restituiti, in modo che ogni utente riceva solo gli elementi inseriti personalmente.

## Test dell'app

1.  In Visual Studio 2012 Express per Windows 8 aprire il progetto modificato dopo avere completato l'esercitazione [Introduzione all'autenticazione][Introduzione all'autenticazione].

2.  Premere F5 per eseguire l'app e accedervi con il provider di identità desiderato.

    Si noti che questa volta non viene restituito alcun elemento, anche se nel corso delle esercitazioni precedenti sono stati aggiunti elementi nella tabella TodoItem. Questo si verifica perché gli elementi precedenti sono stati inseriti senza la colonna userId e ora presentano valori Null.

3.  Nell'app digitare un testo in **Insert a TodoItem**, quindi fare clic su **Save**.

    ![][3]

    Testo e userId verranno inseriti nella tabella TodoItem nel servizio mobile. Poiché il nuovo elemento contiene il valore userId corretto, viene restituito dal servizio mobile e i dati vengono visualizzati nella seconda colonna.

4.  Tornare alla tabella **todoitem** nel [portale di gestione][portale di gestione di Azure], quindi fare clic su **Browse** e verificare che a ogni elemento appena aggiunto sia associato un valore userId.

5.  (Facoltativo) Se si dispone di altri account di accesso, per verificare che gli utenti possano visualizzare solo i relativi dati, chiudere l'app (ALT+F4) ed eseguirla di nuovo. Quando viene visualizzata la finestra di dialogo per l'immissione delle credenziali di accesso, immettere un account di accesso diverso e verificare che i dati immessi nell'account precedente non siano visualizzati.

## Passaggi successivi

L'esercitazione sulle nozioni di base dell'utilizzo dell'autenticazione è terminata. Per ulteriori informazioni, vedere anche i seguenti argomenti su Servizi mobili:

-   [Introduzione ai dati][Introduzione ai dati]
    Ulteriori informazioni sull'archiviazione e sulle query dei dati mediante Servizi mobili.

-   [Introduzione alle notifiche push][Introduzione alle notifiche push]
    Informazioni sull'invio di una notifica push di base all'app.

-   [Riferimento per gli script del server di Servizi mobili][Riferimento per gli script del server di Servizi mobili]
    Ulteriori informazioni sulla registrazione e l'utilizzo di script del server.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [Windows Store C#]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts "Windows Store C#"
  [Windows Store JavaScript]: /it-it/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts "Windows Store JavaScript"
  [Windows Phone]: /it-it/documentation/articles/mobile-services-windows-phone-authorize-users-in-scripts "Windows Phone"
  [iOS]: /it-it/documentation/articles/mobile-services-ios-authorize-users-in-scripts "iOS"
  [Android]: /it-it/documentation/articles/mobile-services-android-authorize-users-in-scripts "Android"
  [HTML]: /it-it/documentation/articles/mobile-services-html-authorize-users-in-scripts "HTML"
  [Xamarin.iOS]: /it-it/documentation/articles/partner-xamarin-mobile-services-ios-authorize-users-in-scripts "Xamarin.iOS"
  [Xamarin.Android]: /it-it/documentation/articles/partner-xamarin-mobile-services-android-authorize-users-in-scripts "Xamarin.Android"
  [Back-end .NET]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts/ ".NET backend"
  [Back-end JavaScript]: /it-it/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts/ "JavaScript backend"
  [Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-js
  [portale di gestione di Azure]: https://manage.windowsazure.com/
  []: ./media/mobile-services-windows-store-javascript-authorize-users-in-scripts/mobile-services-selection.png
  [1]: ./media/mobile-services-windows-store-javascript-authorize-users-in-scripts/mobile-portal-data-tables.png
  [2]: ./media/mobile-services-windows-store-javascript-authorize-users-in-scripts/mobile-insert-script-users.png
  [3]: ./media/mobile-services-windows-store-javascript-authorize-users-in-scripts/mobile-quickstart-startup.png
  [Introduzione ai dati]: /it-it/documentation/articles/mobile-services-windows-store-javascript-get-started-data/
  [Introduzione alle notifiche push]: /it-it/develop/mobile/tutorials/get-started-with-push-js
  [Riferimento per gli script del server di Servizi mobili]: http://go.microsoft.com/fwlink/p/?LinkId=262293
