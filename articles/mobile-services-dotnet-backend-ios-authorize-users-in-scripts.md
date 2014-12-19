<properties pageTitle="Autorizzazione lato servizio (iOS) | Mobile Developer Center" metaKeywords="" description="Learn how to authorize users in the .NET backend of Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Service-side authorization of Mobile Services users" authors="krisragh" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# Autorizzazione sul lato servizio degli utenti di Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-service-auth-users](../includes/mobile-services-selector-service-auth-users.md)]

Questo argomento illustra come autorizzare gli utenti autenticati per l'accesso ai dati in Servizi mobili di Azure da un'app per iOS. In questa esercitazione verrà aggiunto il codice ai metodi di accesso ai dati nel controller per filtrare le query in base all'ID utente di un utente autenticato, per garantire che ogni utente possa visualizzare solo i relativi dati.

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili e sull'esercitazione precedente [Introduzione all'autenticazione]. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione all'autenticazione].  

## <a name="register-scripts"></a>Modificare i metodi di accesso ai dati

[WACOM.INCLUDE [mobile-services-filter-user-results-dotnet-backend](../includes/mobile-services-filter-user-results-dotnet-backend.md)]


## Testare l'app

1. In Xcode aprire il progetto modificato dopo aver completato l'esercitazione [Introduzione all'autenticazione].

2. Premere il pulsante **Run** per creare il progetto, avviare l'app nell'emulatore iPhone e quindi accedere con il provider di identità scelto.

   	Si noti che questa volta, sebbene nel corso delle esercitazioni precedenti siano stati aggiunti elementi nella tabella TodoItem, non viene restituito alcun elemento. Questo si verifica perché gli elementi precedenti sono stati inseriti senza la colonna userId e ora presentano valori Null.

3. Nell'app digitare un testo in **Insert a TodoItem**, quindi fare clic su **Save**.

   	![][3]

   	Testo e userId verranno inseriti nella tabella TodoItem nel servizio mobile. Poiché il nuovo elemento contiene il valore userId corretto, viene restituito dal servizio mobile e i dati vengono visualizzati nella seconda colonna.

5. Tornare alla tabella **todoitem** nel [portale di gestione][portale di gestione di Azure], fare clic su **Sfoglia** e verificare che a ogni elemento appena aggiunto sia associato un valore userId.

6. (Facoltativo) Se si dispone di altri account di accesso, per verificare che gli utenti possano visualizzare solo i propri dati, chiudere l'app ed eseguirla di nuovo. Quando viene visualizzata la finestra di dialogo per l'immissione delle credenziali di accesso, immettere un account di accesso diverso e verificare che i dati immessi nell'account precedente non siano visualizzati.

<!--## Next steps

This concludes the tutorials that demonstrate the basics of working with authentication. Consider finding out more about the following Mobile Services topics:

* [Get started with data]
  <br/>Altre informazioni sull'archiviazione e l'esecuzione di query sui dati tramite Servizi mobili.

* [Introduzione alle notifiche push]
  <br/>Informazioni sull'invio di una notifica push di base all'app.

* [Riferimento per i concetti e le procedure di .NET per Servizi mobili]
  <br/>Altre informazioni su come usare Servizi mobili con .NET.
-->

<!-- Anchors. -->
[Registrare script del server]: #register-scripts
[Passaggi successivi]:#next-steps

<!-- Images. -->

[3]: ./media/mobile-services-dotnet-backend-ios-authorize-users-in-scripts/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started
[Introduzione ai dati]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data
[Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users
[Introduzione alle notifiche push]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push

[Riferimento per i concetti e le procedure di NET per Servizi mobili]: /it-it/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
