<properties pageTitle="Service-side authorization (HTML) | Mobile Dev Center" metaKeywords="" description="Learn how to authorize users in the JavaScript backend of Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Service-side authorization of Mobile Services users" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Autorizzazione lato servizio degli utenti di Servizi mobili

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts" title="Windows Store C#">Windows Store C#</a><a href="/it-it/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/it-it/documentation/articles/mobile-services-windows-phone-authorize-users-in-scripts" title="Windows Phone">Windows Phone</a><a href="/it-it/documentation/articles/mobile-services-ios-authorize-users-in-scripts" title="iOS">iOS</a><a href="/it-it/documentation/articles/mobile-services-android-authorize-users-in-scripts" title="Android">Android</a><a href="/it-it/documentation/articles/mobile-services-html-authorize-users-in-scripts" title="HTML" class="current">HTML</a><a href="/it-it/documentation/articles/partner-xamarin-mobile-services-ios-authorize-users-in-scripts" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/it-it/documentation/articles/partner-xamarin-mobile-services-android-authorize-users-in-scripts" title="Xamarin.Android" class="current">Xamarin.Android</a></div>

<div class="dev-center-tutorial-subselector"><a href="/it-it/documentation/articles/mobile-services-html-authorize-users-in-scripts/"  title="JavaScript backend" class="current">Back-end JavaScript</a></div>

In questo argomento viene illustrato come utilizzare gli script del server per autorizzare gli utenti autenticati per accedere ai dati in Servizi mobili di Azure da un'app HTML. In questa esercitazione verranno registrati gli script con Servizi mobili per filtrare le query in base all'ID utente di un utente autenticato, per garantire che ogni utente possa visualizzare solo i propri dati.

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili e sull'esercitazione precedente [Introduzione all'autenticazione][Introduzione all'autenticazione]. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione all'autenticazione][Introduzione all'autenticazione].

## <a name="register-scripts"></a>Registrazione di script

Poiché l'app di guida introduttiva legge e inserisce i dati, è necessario registrare gli script per queste operazioni sulla tabella TodoItem.

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure], fare clic su **Mobile Services** e quindi sull'app.

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

    <div class="dev-callout"><b>Nota</b>
<p>&Egrave; necessario abilitare lo schema dinamico la prima volta che viene eseguito lo script insert. Quando &egrave; abilitato lo schema dinamico, Servizi mobili aggiunge automaticamente la colonna <strong>userId</strong> alla tabella <strong>TodoItem</strong> alla prima esecuzione. Lo schema dinamico &egrave; abilitato per impostazione predefinita per un nuovo servizio mobile e deve essere disabilitato prima che l'app venga pubblicata.</p>
</div>

5.  Ripetere i passaggi 3 e 4 per sostituire l'operazione **Read** esistente con la funzione seguente:

        function read(query, user, request) {
           query.where({ userId: user.userId });    
           request.execute();
        }

    Questo script consente di filtrare gli oggetti TodoItem restituiti, in modo che ogni utente riceva solo gli elementi inseriti personalmente.

## Test dell'app

1.  In un Web browser, passare alla pagina index.html dell'app, accedere al provider di identità scelto.

    Si noti che questa volta non viene restituito alcun elemento, anche se nel corso delle esercitazioni precedenti sono stati aggiunti elementi nella tabella TodoItem. Questo si verifica perché gli elementi precedenti sono stati inseriti senza la colonna userId e ora presentano valori Null.

2.  Nell'app, digitare un testo in **Enter new task** e quindi fare clic su **Add**.

    ![][3]

    Testo e userId verranno inseriti nella tabella TodoItem nel servizio mobile. Poiché il nuovo elemento contiene il valore userId corretto, viene restituito dal servizio mobile e i dati vengono visualizzati nella seconda colonna.

3.  Tornare alla tabella **todoitem** nel [portale di gestione][portale di gestione di Azure], quindi fare clic su **Browse** e verificare che a ogni elemento appena aggiunto sia associato un valore userId.

4.  (Facoltativo) Se si dispone di altri account di accesso, per verificare che gli utenti possano visualizzare solo i relativi dati, chiudere l'app (ALT+F4) ed eseguirla di nuovo. Quando viene visualizzata la finestra di dialogo per l'immissione delle credenziali di accesso, immettere un account di accesso diverso e verificare che i dati immessi nell'account precedente non siano visualizzati.

## Passaggi successivi

L'esercitazione sulle nozioni di base dell'utilizzo dell'autenticazione è terminata. Per ulteriori informazioni, vedere anche i seguenti argomenti su Servizi mobili:

-   [Introduzione ai dati][Introduzione ai dati]
    Ulteriori informazioni sull'archiviazione e sulle query dei dati mediante Servizi mobili.

-   [Riferimento per gli script del server di Servizi mobili][Riferimento per gli script del server di Servizi mobili]
    Ulteriori informazioni sulla registrazione e l'utilizzo di script del server.

-   [Riferimento per i concetti e le procedure di Servizi mobili con HTML/JavaScript][Riferimento per i concetti e le procedure di Servizi mobili con HTML/JavaScript]
    Ulteriori informazioni su come utilizzare Servizi mobili con HTML/JavaScript



  [Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-html
  [portale di gestione di Azure]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-html-authorize-users-in-scripts/mobile-services-selection.png
  [1]: ./media/mobile-services-html-authorize-users-in-scripts/mobile-portal-data-tables.png
  [2]: ./media/mobile-services-html-authorize-users-in-scripts/mobile-insert-script-users.png
  [3]: ./media/mobile-services-html-authorize-users-in-scripts/mobile-quickstart-startup-html.png
  [Introduzione ai dati]: /it-it/develop/mobile/tutorials/get-started-with-data-html
  [Riferimento per gli script del server di Servizi mobili]: http://go.microsoft.com/fwlink/p/?LinkId=262293
  [Riferimento per i concetti e le procedure di Servizi mobili con HTML/JavaScript]: /it-it/develop/mobile/how-to-guides/work-with-html-js-client
