<properties pageTitle="Service-side authorization (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to authorize users in the .NET backend of Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Service-side authorization of Mobile Services users" authors="glenga" solutions="" manager="" editor="" />

Autorizzazione lato servizio degli utenti di Servizi mobili
===========================================================

[Windows Store C\#](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts "Windows Store C#")[Windows Store JavaScript](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts "Windows Store JavaScript")[Windows Phone](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts "Windows Phone")[iOS](/en-us/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts "iOS")
[Back-end .NET](/en-us/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts/ "Back-end .NET") | [Back-end JavaScript](/en-us/documentation/articles/mobile-services-ios-authorize-users-in-scripts/ "Back-end JavaScript")

In questo argomento viene illustrato come autorizzare gli utenti autenticati per accedere ai dati in Servizi mobili di Azure da un'app per iOS. In questa esercitazione verrà aggiunto il codice ai metodi di accesso ai dati nel controller per filtrare le query in base all'ID utente di un utente autenticato, per garantire che ogni utente possa visualizzare solo i relativi dati.

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili e sull'esercitazione precedente [Introduzione all'autenticazione](/en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users). Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione all'autenticazione](/en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users).

Modifica dei metodi di accesso ai dati
--------------------------------------

[WACOM.INCLUDE [mobile-services-filter-user-results-dotnet-backend](../includes/mobile-services-filter-user-results-dotnet-backend.md)]

Test dell'app
-------------

1.  In Xcode aprire il progetto modificato dopo aver completato l'esercitazione [Introduzione all'autenticazione](/en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users).

2.  Premere il pulsante **Run** per creare il progetto, avviare l'app nell'emulatore iPhone e quindi accedere con il provider di identità scelto.

    Si noti che questa volta, sebbene nel corso delle esercitazioni precedenti siano stati aggiunti elementi nella tabella TodoItem, non viene restituito alcun elemento. Questo si verifica perché gli elementi precedenti sono stati inseriti senza la colonna userId e ora presentano valori Null.

3.  Nell'app digitare un testo in **Insert a TodoItem**, quindi fare clic su **Save**.

    ![][3]

    Testo e userId verranno inseriti nella tabella TodoItem nel servizio mobile. Poiché il nuovo elemento contiene il valore userId corretto, viene restituito dal servizio mobile e i dati vengono visualizzati nella seconda colonna.

4.  Tornare alla tabella **todoitem** nel [portale di gestione][portale di gestione di Azure], quindi fare clic su **Browse** e verificare che a ogni elemento appena aggiunto sia associato un valore userId.

5.  (Facoltativo) Se si dispone di altri account di accesso, per verificare che gli utenti possano visualizzare solo i propri dati, chiudere l'app ed eseguirla di nuovo. Quando viene visualizzata la finestra di dialogo per l'immissione delle credenziali di accesso, immettere un account di accesso diverso e verificare che i dati immessi nell'account precedente non siano visualizzati.


<!-- Anchors. -->
[Register server scripts]: #register-scripts
[Next Steps]:#next-steps

<!-- Images. -->

[3]: ./media/mobile-services-dotnet-backend-ios-authorize-users-in-scripts/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Get started with Mobile Services]: /en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started
[Get started with data]: /en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data
[Get started with authentication]: /en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users
[Get started with push notifications]: /en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push

[Mobile Services .NET How-to Conceptual Reference]: /en-us/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/