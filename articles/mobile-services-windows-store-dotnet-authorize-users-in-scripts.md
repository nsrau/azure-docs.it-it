<properties  pageTitle="Service-side authorization (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to authorize users in the JavaScript backend of Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Service-side authorization of Mobile Services users" authors="glenga" solutions="" manager="" editor="" />

# Autorizzazione lato servizio degli utenti di Servizi mobili
<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts" title="Windows Store C#" class="current">Windows Store C#</a><a href="/it-it/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/it-it/documentation/articles/mobile-services-windows-phone-authorize-users-in-scripts" title="Windows Phone">Windows Phone</a><a href="/it-it/documentation/articles/mobile-services-ios-authorize-users-in-scripts" title="iOS">iOS</a><a href="/it-it/documentation/articles/mobile-services-android-authorize-users-in-scripts" title="Android">Android</a><a href="/it-it/documentation/articles/mobile-services-html-authorize-users-in-scripts" title="HTML">HTML</a><a href="/it-it/documentation/articles/partner-xamarin-mobile-services-ios-authorize-users-in-scripts" title="Xamarin.iOS">Xamarin.iOS</a><a href="/it-it/documentation/articles/partner-xamarin-mobile-services-android-authorize-users-in-scripts" title="Xamarin.Android">Xamarin.Android</a></div>
<div class="dev-center-tutorial-subselector"><a href="/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts/" title=".NET backend">.NET backend</a> | <a href="/it-it/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts/"  title="JavaScript backend" class="current">JavaScript backend</a></div>

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
In questo argomento viene illustrato come autorizzare gli utenti autenticati per accedere ai dati in Servizi mobili di Azure da un'app di Windows Store. In questa esercitazione verranno registrati gli script con Servizi Mobili per filtrare le query in base all'ID utente di un utente autenticato, per garantire che ogni utente possa visualizzare solo i relativi dati.


<p>È inoltre possibile visualizzare una versione video dell'esercitazione facendo clic sul fotogramma a destra.</p>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Authenticate-and-Authorize-users-with-Server-Scripts-in-Windows-Azure-Mobile-Servi" target="_blank" class="label">guarda l'esercitazione</a> <a style="background-image: url('/media/devcenter/mobile/videos/authorize-users-with-scripts-windows-store-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Authenticate-and-Authorize-users-with-Server-Scripts-in-Windows-Azure-Mobile-Servi" target="_blank" class="dev-onpage-video"><span class="icon">Play Video</span></a> <span class="time">13:52</span></div>
</div>

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili e sull'esercitazione precedente [Introduzione all'autenticazione](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet). Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione all'autenticazione](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet).

## <a name="register-scripts"></a>Registrazione di script

Poiché l'app di guida introduttiva legge e inserisce i dati, è necessario registrare gli script per queste operazioni sulla tabella TodoItem.

1.  Accedere al [portale di gestione di Azure][2], fare clic su **Mobile Services** e quindi sull'app.
    
	![][0]

2.  Fare clic sulla scheda **Data** e quindi sulla tabella **TodoItem**.
    
	![][1]

3.  Fare clic su **Script** e quindi selezionare l'operazione
    **Insert**.
    
	![][2]

4.  Sostituire lo script esistente con la funzione seguente e quindi fare clic su **Save**.
    
        function insert(item, user, request) {
          item.userId = user.userId;    
          request.execute();
        }
    
    Questo script aggiunge un valore userId, ovvero l'ID utente
    dell'utente autenticato, all'elemento prima che venga inserito nella
    tabella TodoItem.
    


	**Nota**
	È necessario abilitare lo schema dinamico la prima volta che viene
	eseguito lo script insert. Quando è abilitato lo schema dinamico, Servizi mobili aggiunge automaticamente la colonna **userId** alla tabella **TodoItem** alla prima esecuzione. Lo schema dinamico è abilitato per impostazione predefinita per un nuovo servizio mobile e deve essere disabilitato prima che l'app venga pubblicata in Windows Store.





5.  Ripetere i passaggi 3 e 4 per sostituire l'operazione **Read** esistente con la funzione seguente:
    
        function read(query, user, request) {
           query.where({ userId: user.userId });    
           request.execute();
        }

	Questo script consente di filtrare gli oggetti TodoItem restituiti, in modo che ogni utente riceva solo gli elementi inseriti personalmente.

## Test dell'app

1.  In Visual Studio 2012 Express per Windows 8 aprire il progetto modificato dopo avere completato l'esercitazione [Introduzione all'autenticazione](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet).

2.  Premere F5 per eseguire l'app e accedervi con il provider di identità desiderato.
    
	Si noti che questa volta, sebbene nel corso delle esercitazioni precedenti siano stati aggiunti elementi nella tabella TodoItem, non viene restituito alcun elemento. Questo si verifica perché gli elementi precedenti sono stati inseriti senza la colonna userId e ora presentano valori Null.

3.  Nell'app digitare un testo in **Insert a TodoItem**, quindi fare clic su **Save**.
    
	![][3]

	Testo e userId verranno inseriti nella tabella TodoItem nel servizio mobile. Poiché il nuovo elemento contiene il valore userId corretto, viene restituito dal servizio mobile e i dati vengono visualizzati nella seconda colonna.

4.  Tornare alla tabella **todoitem** nel [portale di gestione][2], quindi fare clic su **Browse** e verificare che a ogni elemento appena aggiunto sia associato un valore userId.

5.  (Facoltativo) Se si dispone di altri account di accesso, per verificare che gli utenti possano visualizzare solo i relativi dati, chiudere l'app (ALT+F4) ed eseguirla di nuovo. Quando viene visualizzata la finestra di dialogo per l'immissione delle credenziali di accesso, immettere un account di accesso diverso e verificare che i dati immessi nell'account precedente non siano visualizzati.

## Passaggi successivi

L'esercitazione sulle nozioni di base dell'utilizzo dell'autenticazione è terminata. Per ulteriori informazioni, vedere anche i seguenti argomenti su Servizi mobili:

* [Introduzione ai   dati](/it-it/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/)
  Ulteriori informazioni sull'archiviazione e sulle query dei dati mediante Servizi mobili.

* [Introduzione alle notifiche push](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet)
  Informazioni sull'invio di una notifica push di base all'app.

* [Riferimento per gli script del server di Servizi mobili][3]
  Ulteriori informazioni sulla registrazione e l'utilizzo di script del server.

* [Riferimento per i concetti e le procedure di Servizi mobili con .NET](/en-us/develop/mobile/how-to-guides/work-with-net-client-library)
  Ulteriori informazioni su come utilizzare Servizi mobili con .NET.

<!-- Anchors. -->
[Register server scripts]: #register-scripts
[Next Steps]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-dotnet-authorize-users-in-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-windows-store-dotnet-authorize-users-in-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-windows-store-dotnet-authorize-users-in-scripts/mobile-insert-script-users.png
[3]: ./media/mobile-services-windows-store-dotnet-authorize-users-in-scripts/mobile-quickstart-startup.png

<!-- URLs. -->
[Windows Push Notifications & Live Connect]: http://go.microsoft.com/fwlink/?LinkID=257677
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/?LinkId=262293
[My Apps dashboard]: http://go.microsoft.com/fwlink/?LinkId=262039
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-dotnet
[JavaScript and HTML]: mobile-services-win8-javascript/

[Azure Management Portal]: https://manage.windowsazure.com/
[Mobile Services .NET How-to Conceptual Reference]: /en-us/develop/mobile/how-to-guides/work-with-net-client-library



[1]: http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Authenticate-and-Authorize-users-with-Server-Scripts-in-Windows-Azure-Mobile-Servi
[2]: https://manage.windowsazure.com/
[3]: http://go.microsoft.com/fwlink/?LinkId=262293