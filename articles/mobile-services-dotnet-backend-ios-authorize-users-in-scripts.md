<properties pageTitle="Service-side authorization (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to authorize users in the .NET backend of Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Service-side authorization of Mobile Services users" authors="krisragh" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh"></tags>

# Autorizzazione lato servizio degli utenti di Servizi mobili

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts" title="Windows Store C#">Windows Store C#</a><a href="/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts" title="Windows Phone">Windows Phone</a><a href="/it-it/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts" title="iOS" class="current">iOS</a></div>

<div class="dev-center-tutorial-subselector"><a href="/it-it/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts/" title=".NET backend" class="current">Back-end .NET</a> | <a href="/it-it/documentation/articles/mobile-services-ios-authorize-users-in-scripts/"  title="JavaScript backend">Back-end JavaScript</a></div>

In questo argomento viene illustrato come autorizzare gli utenti autenticati per accedere ai dati in Servizi mobili di Azure da un'app per iOS. In questa esercitazione verrà aggiunto il codice ai metodi di accesso ai dati nel controller per filtrare le query in base all'ID utente di un utente autenticato, per garantire che ogni utente possa visualizzare solo i relativi dati.

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili e sull'esercitazione precedente [Introduzione all'autenticazione][]. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione all'autenticazione][].

## <a name="register-scripts"></a>Modifica dei metodi di accesso ai dati

[WACOM.INCLUDE [mobile-services-filter-user-results-dotnet-backend][]]

## Test dell'app

1.  In Xcode aprire il progetto modificato dopo aver completato l'esercitazione [Introduzione all'autenticazione][].

2.  Premere il pulsante **Run** per creare il progetto, avviare l'app nell'emulatore iPhone e quindi accedere con il provider di identità scelto.

    Si noti che questa volta non viene restituito alcun elemento, anche se nel corso delle esercitazioni precedenti sono stati aggiunti elementi nella tabella TodoItem. Questo si verifica perché gli elementi precedenti sono stati inseriti senza la colonna userId e ora presentano valori Null.

3.  Nell'app digitare un testo in **Insert a TodoItem**, quindi fare clic su **Save**.

    ![][]

    Testo e userId verranno inseriti nella tabella TodoItem nel servizio mobile. Poiché il nuovo elemento contiene il valore userId corretto, viene restituito dal servizio mobile e i dati vengono visualizzati nella seconda colonna.

4.  Tornare alla tabella **todoitem** nel [portale di gestione][portale di gestione di Azure], quindi fare clic su **Browse** e verificare che a ogni elemento appena aggiunto sia associato un valore userId.

5.  (Facoltativo) Se si dispone di altri account di accesso, per verificare che gli utenti possano visualizzare solo i propri dati, chiudere l'app ed eseguirla di nuovo. Quando viene visualizzata la finestra di dialogo per l'immissione delle credenziali di accesso, immettere un account di accesso diverso e verificare che i dati immessi nell'account precedente non siano visualizzati.

<!--## Next steps  This concludes the tutorials that demonstrate the basics of working with authentication. Consider finding out more about the following Mobile Services topics:  * [Get started with data]   <br/>Learn more about storing and querying data using Mobile Services.  * [Get started with push notifications]    <br/>Learn how to send a very basic push notification to your app.    * [Mobile Services .NET How-to Conceptual Reference]   <br/>Learn more about how to use Mobile Services with .NET. --> <!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [Windows Store C#]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts "Windows Store C#"
  [Windows Store JavaScript]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts "Windows Store JavaScript"
  [Windows Phone]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts "Windows Phone"
  [iOS]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts "iOS"
  [Back-end .NET]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts/ ".NET backend"
  [Back-end JavaScript]: /it-it/documentation/articles/mobile-services-ios-authorize-users-in-scripts/ "JavaScript backend"
  [Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users
  [mobile-services-filter-user-results-dotnet-backend]: ../includes/mobile-services-filter-user-results-dotnet-backend.md
  []: ./media/mobile-services-dotnet-backend-ios-authorize-users-in-scripts/mobile-quickstart-startup-ios.png
