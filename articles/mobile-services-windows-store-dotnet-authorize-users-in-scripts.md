<properties pageTitle="Autorizzazione lato servizio (Windows Store) | Mobile Developer Center" metaKeywords="" description="Informazioni su come autorizzare gli utenti nel back-end JavaScript di Servizi mobili di Azure." metaCanonical="" services="" documentationCenter="Mobile" title="Service-side authorization of Mobile Services users" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/29/2014" ms.author="glenga" />

# Autorizzazione sul lato servizio degli utenti di Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-service-auth-users](../includes/mobile-services-selector-service-auth-users.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Questo argomento illustra come autorizzare gli utenti autenticati per accedere ai dati in Servizi mobili di Azure da un'app di Windows Store. In questa esercitazione verranno registrati gli script con Servizi mobili per filtrare le query in base all'ID utente di un utente autenticato, per garantire che ogni utente possa visualizzare solo i propri dati.</p>
<p>È inoltre possibile visualizzare una versione video dell'esercitazione facendo clic sul fotogramma a destra.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Authenticate-and-Authorize-users-with-Server-Scripts-in-Windows-Azure-Mobile-Servi" target="_blank" class="label">video di esercitazione</a> <a style="background-image: url('/media/devcenter/mobile/videos/authorize-users-with-scripts-windows-store-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Authenticate-and-Authorize-users-with-Server-Scripts-in-Windows-Azure-Mobile-Servi" target="_blank" class="dev-onpage-video"><span class="icon">Riproduci video</span></a> <span class="time">13:52</span></div>
</div>

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili e sull'esercitazione precedente [Introduzione all'autenticazione]. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione all'autenticazione].  

## <a name="register-scripts"></a>Registrare gli script
Poiché l'app di guida introduttiva legge e inserisce i dati, è necessario registrare gli script per queste operazioni sulla tabella TodoItem.

1. Accedere al [Portale di gestione di Azure], fare clic su **Servizi mobili** e quindi sull'app. 

   	![][0]

2. Fare clic sulla scheda **Dati** e quindi sulla tabella **TodoItem**.

   	![][1]

3. Fare clic su **Script** e quindi selezionare l'operazione **Insert**.

   	![][2]

4. Sostituire lo script esistente con la funzione seguente e quindi fare clic su **Salva**.

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

1. In Visual Studio 2012 Express per Windows 8 aprire il progetto modificato dopo avere completato l'esercitazione [Introduzione all'autenticazione].

2. Premere F5 per eseguire l'app e accedervi con il provider di identità desiderato. 

   	Si noti che questa volta, sebbene nel corso delle esercitazioni precedenti siano stati aggiunti elementi nella tabella TodoItem, non viene restituito alcun elemento. Questo si verifica perché gli elementi precedenti sono stati inseriti senza la colonna userId e ora presentano valori Null.

3. Nell'app digitare un testo in **Insert a TodoItem**, quindi fare clic su **Save**.

   	![][3]

   	Testo e userId verranno inseriti nella tabella TodoItem nel servizio mobile. Poiché il nuovo elemento contiene il valore userId corretto, viene restituito dal servizio mobile e i dati vengono visualizzati nella seconda colonna.

5. Tornare alla tabella **todoitem** nel [portale di gestione][Azure Management Portal], quindi fare clic su **Sfoglia** e verificare che a ogni elemento appena aggiunto sia associato un valore userId.

6. (Facoltativo) Se si dispone di altri account di accesso, per verificare che gli utenti possano visualizzare solo i relativi dati, chiudere l'app (ALT+F4) ed eseguirla di nuovo. Quando viene visualizzata la finestra di dialogo per l'immissione delle credenziali di accesso, immettere un account di accesso diverso e verificare che i dati immessi nell'account precedente non siano visualizzati. 

## Passaggi successivi

L'esercitazione sulle nozioni di base dell'uso dell'autenticazione è terminata. Per altre informazioni, vedere anche i seguenti argomenti su Servizi mobili:

* [Introduzione ai dati]
  <br/>Altre informazioni sull'archiviazione e l'esecuzione di query sui dati tramite Servizi mobili.

* [Introduzione alle notifiche push] 
  <br/>Informazioni sull'invio di una notifica push di base all'app.

* [Riferimento per gli script del server di Servizi mobili]
  <br/>Altre informazioni sulla registrazione e l'uso di script del server.
  
* [Riferimento per i concetti e le procedure di .NET per Servizi mobili]
  <br/>Altre informazioni su come usare Servizi mobili con .NET.

<!-- Anchors. -->
[Registrare gli script del server]: #register-scripts
[Passaggi successivi]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-dotnet-authorize-users-in-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-windows-store-dotnet-authorize-users-in-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-windows-store-dotnet-authorize-users-in-scripts/mobile-insert-script-users.png
[3]: ./media/mobile-services-windows-store-dotnet-authorize-users-in-scripts/mobile-quickstart-startup.png

<!-- URLs. -->
[Notifiche push di Windows e Live Connect]: http://go.microsoft.com/fwlink/?LinkID=257677
[Riferimento per gli script del server di Servizi mobili]: http://go.microsoft.com/fwlink/?LinkId=262293
[Dashboard App personali]: http://go.microsoft.com/fwlink/?LinkId=262039
[Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started/#create-new-service
[Introduzione ai dati]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/
[Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-dotnet
[Introduzione alle notifiche push]: /it-it/develop/mobile/tutorials/get-started-with-push-dotnet
[JavaScript e HTML]: mobile-services-win8-javascript/

[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Riferimento per i concetti e le procedure di .NET per Servizi mobili]: /it-it/develop/mobile/how-to-guides/work-with-net-client-library
