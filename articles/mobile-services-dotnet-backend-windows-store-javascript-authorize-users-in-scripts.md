<properties pageTitle="Service-side authorization (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to authorize users in the .NET backend of Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Service-side authorization of Mobile Services users" authors="glenga" solutions="" manager="" editor="" />

Autorizzazione lato servizio degli utenti di Servizi mobili
===========================================================

[Windows Store C\#](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts "Windows Store C#")[Windows Store JavaScript](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts "Windows Store JavaScript")[Windows Phone](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts "Windows Phone")[iOS](/it-it/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts "iOS")
[Back-end .NET](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts/ "Back-end .NET") | [Back-end JavaScript](/it-it/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts/ "Back-end JavaScript")

In questo argomento viene illustrato come autorizzare gli utenti autenticati per accedere ai dati in Servizi mobili di Azure da un'app di Windows Store. In questa esercitazione verrà aggiunto il codice ai metodi di accesso ai dati nel controller per filtrare le query in base all'ID utente di un utente autenticato, per garantire che ogni utente possa visualizzare solo i relativi dati.

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili e sull'esercitazione precedente [Introduzione all'autenticazione](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users). Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione all'autenticazione](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users).

Modifica dei metodi di accesso ai dati
--------------------------------------

[WACOM.INCLUDE [mobile-services-filter-user-results-dotnet-backend](../includes/mobile-services-filter-user-results-dotnet-backend.md)]

Test dell'app
-------------

1.  In Visual Studio aprire il progetto modificato dopo aver completato l'esercitazione di [Introduzione all'autenticazione](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users).

2.  Premere F5 per eseguire l'app e accedervi con il provider di identità desiderato.

    Si noti che questa volta, sebbene nel corso delle esercitazioni precedenti siano stati aggiunti elementi nella tabella TodoItem, non viene restituito alcun elemento. Questo si verifica perché gli elementi precedenti sono stati inseriti senza la colonna userId e ora presentano valori Null.

3.  Nell'app digitare un testo in **Insert a TodoItem**, quindi fare clic su **Save**.

    ![][3]

    Testo e userId verranno inseriti nella tabella TodoItem nel servizio mobile. Poiché il nuovo elemento contiene il valore userId corretto, viene restituito dal servizio mobile e i dati vengono visualizzati nella seconda colonna.

4.  (Facoltativo) Se si dispone di altri account di accesso, per verificare che gli utenti possano visualizzare solo i relativi dati, chiudere l'app (ALT+F4) ed eseguirla di nuovo. Quando viene visualizzata la finestra di dialogo per l'immissione delle credenziali di accesso, immettere un account di accesso diverso e verificare che i dati immessi nell'account precedente non siano visualizzati.

Passaggi successivi
-------------------

L'esercitazione sulle nozioni di base dell'utilizzo dell'autenticazione è terminata. Per ulteriori informazioni, vedere anche i seguenti argomenti su Servizi mobili:

-   [Introduzione ai dati](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data)
    <br/>Ulteriori informazioni sull'archiviazione e sulle query dei dati mediante Servizi mobili.

-   [Introduzione alle notifiche push](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push)
    <br/>Informazioni sull'invio di una notifica push di base all'app.

-   [Come utilizzare un client HTML/JavaScript per Servizi mobili di Azure](/it-it/documentation/articles/mobile-services-html-how-to-use-client-library)
    <br/>Informazioni su come utilizzare Servizi mobili con JavaScript e HTML.


<!-- Anchors. -->
[Register server scripts]: #register-scripts
[Next Steps]:#next-steps

<!-- Images. -->

[3]: ./media/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts/mobile-quickstart-startup.png

<!-- URLs. -->
[Get started with Mobile Services]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-get-started
[Get started with data]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data
[Get started with authentication]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users
[Get started with push notifications]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push

[How to use an HTML/JavaScript client for Azure Mobile Services]: /it-it/documentation/articles/mobile-services-html-how-to-use-client-library
