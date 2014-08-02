<properties pageTitle="Add paging to data (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Windows Phone app from Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="glenga" solutions="" manager="" editor="" />

Utilizzo del paging per ridefinire le query di Servizi mobili
=============================================================

> [AZURE.SELECTOR-LIST (Platform | Backend )] 
- [(Windows Store C\# | .NET)](mobile-services-dotnet-backend-windows-store-dotnet-add-paging-data.md) 
- [(Windows Store C\# | JavaScript)](mobile-services-windows-store-dotnet-add-paging-data.md) 
- [(Windows Store JavaScript | .NET)](mobile-services-dotnet-backend-windows-store-javascript-add-paging-data.md) 
- [(Windows Store JavaScript | JavaScript)](mobile-services-windows-store-javascript-add-paging-data.md) 
- [(Windows Phone | .NET)](mobile-services-dotnet-backend-windows-phone-add-paging-data) 
- [(Windows Phone | JavaScript)](mobile-services-windows-phone-add-paging-data) 
- [(iOS | JavaScript)](mobile-services-ios-add-paging-data) 
- [(Android | JavaScript)](mobile-services-android-add-paging-data) 
- [(HTML | .NET)](mobile-services-html-add-paging-data) 
- [(Xamarin iOS | .NET)](partner-xamarin-mobile-services-ios-add-paging-data) 
- [(Xamarin Android | .NET)](partner-xamarin-mobile-services-android-add-paging-data)

In questo argomento viene illustrato come utilizzare il paging per gestire la quantità di dati restituiti all'app per Windows Phone da Servizi mobili di Azure. In questa esercitazione verranno utilizzati i metodi di query **Take** e **Skip** sul client per richiedere "pagine" di dati specifiche.

> [WACOM.NOTE]Per evitare l'overflow dei dati nei client dei dispositivi mobili, Servizi mobili implementa un limite di pagina automatico, che per impostazione predefinita prevede un massimo di 50 elementi in una risposta. Specificando la dimensione della pagina, è possibile richiedere in modo esplicito fino a un massimo di 1.000 elementi nella risposta.

Questa esercitazione è basata sulle procedure e sull'app di esempio creata nell'esercitazione precedente [Introduzione ai dati](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/). Prima di iniziare questa esercitazione, è necessario completare almeno la prima esercitazione della serie relativa all'utilizzo dei dati, [Introduzione ai dati](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/).

[WACOM.INCLUDE [mobile-services-windows-dotnet-paging](../includes/mobile-services-windows-dotnet-paging.md)]

Passaggi successivi
-------------------

Il set di esercitazioni relative alle nozioni di base dell'utilizzo dei dati in Servizi mobili è concluso. Per ulteriori informazioni, vedere anche i seguenti argomenti su Servizi mobili:

-   [Introduzione all'autenticazione](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/)
    <br/>Informazioni sull'autenticazione degli utenti dell'app con l'account Windows.

-   [Introduzione alle notifiche push](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/)
    <br/>Informazioni sull'invio di una notifica push di base all'app.


<!-- Anchors. -->

[Next Steps]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[Get started with Mobile Services]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/
[Get started with data]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/
[Get started with authentication]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/
[Get started with push notifications]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/


[Management Portal]: https://manage.windowsazure.com/
