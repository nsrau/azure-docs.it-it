<properties urlDisplayName="Authorize Users in Scripts (Xamarin.iOS)" pageTitle="Autorizzare gli utenti negli script (Xamarin.iOS) - Servizi mobili di Azure" metaKeywords="autorizzazione dell'utente in Azure, autorizzazione di script Xamarin.iOS, autorizzare servizi mobili" description="Learn how to authorize users with scripts in your Azure Mobile Services app for Xamarin.iOS." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Use scripts to authorize users in Mobile Services" documentationCenter="Mobile" authors="donnam" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="11/11/2014" ms.author="donnam" />

# Usare gli script per autorizzare gli utenti in Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-service-auth-users](../includes/mobile-services-selector-service-auth-users.md)]	

Questo argomento illustra come usare gli script del server per autorizzare gli utenti autenticati ad accedere ai dati in Servizi mobili di Azure da un'app di Xamarin.iOS.  In questa esercitazione verranno registrati gli script con Servizi Mobili per filtrare le query in base all'ID utente di un utente autenticato, per garantire che ogni utente possa visualizzare solo i propri dati.

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili e sull'esercitazione precedente [Introduzione all'autenticazione]. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione all'autenticazione].  

## <a name="register-scripts"></a>Registrare gli script
Poiché l'app di guida introduttiva legge e inserisce i dati, è necessario registrare gli script per queste operazioni sulla tabella TodoItem.

1. Accedere al [portale di gestione di Azure], fare clic su **Servizi mobili** e quindi sull'app. 

   	![][0]

2. Fare clic sulla scheda **Dati** e quindi sulla tabella **TodoItem**.

   	![][1]

3. Fare clic su **Script** e quindi selezionare l'operazione **Insert**.

   	![][2]

4. Sostituire lo script esistente con la funzione seguente e quindi fare clic su **Save**.

        function insert(item, user, request) {
          item.userId = user.userId;    
          request.execute();
        }

    Questo script aggiunge un valore userId, ovvero l'ID utente dell'utente autenticato, all'elemento prima che venga inserito nella tabella TodoItem. 

    <div class="dev-callout"><b>Nota</b>
	<p>È necessario abilitare lo schema dinamico la prima volta che viene eseguito lo script insert. Quando è abilitato lo schema dinamico, Servizi mobili aggiunge automaticamente la colonna <strong>userId</strong> alla tabella <strong>TodoItem</strong> alla prima esecuzione. Lo schema dinamico è abilitato per impostazione predefinita per un nuovo servizio mobile e deve essere disabilitato prima che l'app venga pubblicata in Windows Store.</p>
    </div>


5. Ripetere i passaggi 3 e 4 per sostituire l'operazione **Read** esistente con la funzione seguente:

        function read(query, user, request) {
           query.where({ userId: user.userId });    
           request.execute();
        }

   	Questo script consente di filtrare gli oggetti TodoItem restituiti, in modo che ogni utente riceva solo gli elementi inseriti personalmente.

## Testare l'app

1. In Xamarin Studio o Visual Studio aprire il progetto modificato dopo avere completato l'esercitazione [Introduzione all'autenticazione].

2. Premere il pulsante **Run** per creare il progetto, avviare l'app nell'emulatore iPhone e quindi accedere con il provider di identità scelto. 

Si noti che questa volta, sebbene nel corso delle esercitazioni precedenti siano stati aggiunti elementi nella tabella TodoItem, non viene restituito alcun elemento. Questo si verifica perché gli elementi precedenti sono stati inseriti senza la colonna userId e ora presentano valori Null.

3. Nell'app digitare un testo in **Insert a TodoItem**, quindi fare clic su **Save**.

   	![][3]

Testo e userId verranno inseriti nella tabella TodoItem nel servizio mobile. Poiché il nuovo elemento contiene il valore userId corretto, viene restituito dal servizio mobile e i dati vengono visualizzati nella seconda colonna.

5. Tornare alla tabella **todoitem** nel [portale di gestione][portale di gestione di Azure], fare clic su **Sfoglia** e verificare che a ogni elemento appena aggiunto sia associato un valore userId.

6. (Facoltativo) Se si dispone di altri account di accesso, per verificare che gli utenti possano visualizzare solo i propri dati, chiudere l'app ed eseguirla di nuovo. Quando viene visualizzata la finestra di dialogo per l'immissione delle credenziali di accesso, immettere un account di accesso diverso e verificare che i dati immessi nell'account precedente non siano visualizzati.

## Passaggi successivi

L'esercitazione sulle nozioni di base dell'uso dell'autenticazione è terminata. Per altre informazioni, vedere anche i seguenti argomenti su Servizi mobili:

* [Introduzione ai dati]
  <br/>Altre informazioni sull'archiviazione e l'esecuzione di query sui dati tramite Servizi mobili.

* [Introduzione alle notifiche push] 
  <br/>Informazioni sull'invio di una notifica push di base all'app.

* [Riferimento per gli script del server di Servizi mobili]
  <br/>Altre informazioni sulla registrazione e l'uso di script del server.

<!-- Anchors. -->
[Registrare gli script del server]: #register-scripts
[Passaggi successivi]:#next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-ios-authorize-users-in-scripts/mobile-services-selection.png
[1]: ./media/partner-xamarin-mobile-services-ios-authorize-users-in-scripts/mobile-portal-data-tables.png
[2]: ./media/partner-xamarin-mobile-services-ios-authorize-users-in-scripts/mobile-insert-script-users.png
[3]: ./media/partner-xamarin-mobile-services-ios-authorize-users-in-scripts/mobile-quickstart-startup-ios.png

<!-- URLs. -->

[Notifiche push di Windows e Live Connect]: http://go.microsoft.com/fwlink/p/?LinkID=257677
[Riferimento per gli script del server di Servizi mobili]: http://go.microsoft.com/fwlink/p/?LinkId=262293
[Dashboard App personali]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started/#create-new-service
[Introduzione ai dati]: /it-it/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Introduzione alle notifiche push]: /it-it/develop/mobile/tutorials/get-started-with-push-xamarin-ios

[Portale di gestione di Azure]: https://manage.windowsazure.com/
