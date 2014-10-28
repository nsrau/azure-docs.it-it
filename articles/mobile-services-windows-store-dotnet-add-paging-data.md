<properties pageTitle="Refine Mobile Services queries with paging (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Windows Store app from Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Utilizzo del paging per ridefinire le query di Servizi mobili

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/mobile-services-windows-store-dotnet-add-paging-data" title="Windows Store C#" class="current">Windows Store C#</a><a href="/it-it/documentation/articles/mobile-services-windows-store-javascript-add-paging-data" title="Windows Store JavaScript" class="current">Windows Store JavaScript</a><a href="/it-it/documentation/articles/mobile-services-windows-phone-add-paging-data" title="Windows Phone" class="current">Windows Phone</a><a href="/it-it/documentation/articles/mobile-services-ios-add-paging-data" title="iOS" class="current">iOS</a><a href="/it-it/documentation/articles/mobile-services-android-add-paging-data" title="Android">Android</a><a href="/it-it/documentation/articles/mobile-services-html-add-paging-data" title="HTML" class="current">HTML</a><a href="/it-it/documentation/articles/partner-xamarin-mobile-services-ios-add-paging-data" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/it-it/documentation/articles/partner-xamarin-mobile-services-android-add-paging-data" title="Xamarin.Android" class="current">Xamarin.Android</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-add-paging-data" title=".NET backend">Back-end .NET</a> | <a href="/it-it/documentation/articles/mobile-services-windows-store-dotnet-add-paging-data"  title="JavaScript backend" class="current">Back-end JavaScript</a></div>

In questo argomento viene illustrato come utilizzare il paging per gestire la quantità di dati restituiti all'app di Windows Store da Servizi mobili di Azure. In questa esercitazione verranno utilizzati i metodi di query **Take** e **Skip** sul client per richiedere "pagine" di dati specifiche.

> [WACOM.NOTE]Per evitare l'overflow dei dati nei client dei dispositivi mobili, Servizi mobili implementa un limite di pagina automatico, che per impostazione predefinita prevede un massimo di 50 elementi in una risposta. Specificando la dimensione della pagina, è possibile richiedere in modo esplicito fino a un massimo di 1.000 elementi nella risposta.

Questa esercitazione è basata sulle procedure e sull'app di esempio creata nell'esercitazione precedente [Introduzione ai dati][Introduzione ai dati]. Prima di iniziare questa esercitazione, è necessario completare almeno la prima esercitazione della serie relativa all'utilizzo dei dati, [Introduzione ai dati][Introduzione ai dati].

[WACOM.INCLUDE [mobile-services-windows-dotnet-paging][mobile-services-windows-dotnet-paging]]

## <a name="next-steps"> </a>Passaggi successivi

Il set di esercitazioni relative alle nozioni di base dell'utilizzo dei dati in Servizi mobili è concluso. Per ulteriori informazioni, vedere anche i seguenti argomenti su Servizi mobili:

-   [Introduzione all'autenticazione][Introduzione all'autenticazione]
    Informazioni sull'autenticazione degli utenti dell'app con l'account Windows.

-   [Introduzione alle notifiche push][Introduzione alle notifiche push]
    Informazioni sull'invio di una notifica push di base all'app.

-   [Riferimento per i concetti e le procedure di Servizi mobili con .NET][Riferimento per i concetti e le procedure di Servizi mobili con .NET]
    Ulteriori informazioni su come utilizzare Servizi mobili con .NET.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [Windows Store C#]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-add-paging-data "Windows Store C#"
  [Windows Store JavaScript]: /it-it/documentation/articles/mobile-services-windows-store-javascript-add-paging-data "Windows Store JavaScript"
  [Windows Phone]: /it-it/documentation/articles/mobile-services-windows-phone-add-paging-data "Windows Phone"
  [iOS]: /it-it/documentation/articles/mobile-services-ios-add-paging-data "iOS"
  [Android]: /it-it/documentation/articles/mobile-services-android-add-paging-data "Android"
  [HTML]: /it-it/documentation/articles/mobile-services-html-add-paging-data "HTML"
  [Xamarin.iOS]: /it-it/documentation/articles/partner-xamarin-mobile-services-ios-add-paging-data "Xamarin.iOS"
  [Xamarin.Android]: /it-it/documentation/articles/partner-xamarin-mobile-services-android-add-paging-data "Xamarin.Android"
  [Back-end .NET]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-add-paging-data ".NET backend"
  [Back-end JavaScript]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-add-paging-data "JavaScript backend"
  [Introduzione ai dati]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/
  [mobile-services-windows-dotnet-paging]: ../includes/mobile-services-windows-dotnet-paging.md
  [Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
  [Introduzione alle notifiche push]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-get-started-push/
  [Riferimento per i concetti e le procedure di Servizi mobili con .NET]: /it-it/develop/mobile/how-to-guides/work-with-net-client-library
