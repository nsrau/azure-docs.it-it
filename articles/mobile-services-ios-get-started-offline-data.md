<properties linkid="develop-mobile-tutorials-get-started-offline-data-ios" urlDisplayName="Getting Started with Offline Data" pageTitle="Get started with offline data in Mobile Services (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to cache and sync offline data in your iOS application" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with offline data sync in Mobile Services" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="wesmc"></tags>

# Introduzione alla sincronizzazione dei dati offline in Servizi mobili

<div class="dev-center-tutorial-selector sublanding">
<a href="/it-it/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data" title="Windows Store C#">Windows Store C#</a>
<a href="/it-it/documentation/articles/mobile-services-windows-phone-get-started-offline-data" title="Windows Phone">Windows Phone</a>
<a href="/it-it/documentation/articles/mobile-services-ios-get-started-offline-data" title="iOS" class="current">iOS</a>
<a href="/it-it/documentation/articles/mobile-services-xamarin-ios-get-started-offline-data" title="Xamarin.iOS">Xamarin.iOS</a>
<a href="/it-it/documentation/articles/mobile-services-xamarin-android-get-started-offline-data" title="Xamarin.Android">Xamarin.Android</a>
</div>

Questa esercitazione illustra la funzionalità di sincronizzazione offline di Servizi mobili, grazie alla quale gli sviluppatori possono scrivere app utilizzabili anche se l'utente finale non ha accesso alla rete.

La sincronizzazione offline ha diversi usi potenziali:

-   Migliorare la velocità di risposta dell'app memorizzando i dati del server nella cache locale del dispositivo
-   Rendere le app resilienti rispetto alla connettività di rete intermittente
-   Permettere agli utenti finali di creare e modificare i dati anche in mancanza di accesso di rete, supportando scenari con connettività scarsa o assente
-   Sincronizzare i dati tra più dispositivi e rilevare i conflitti quando lo stesso record viene modificato da due dispositivi

In questa esercitazione verrà illustrato come aggiornare l'app creata nell'esercitazione [Introduzione a Servizi mobili][Introduzione a Servizi mobili] o [Introduzione ai dati][Introduzione ai dati] per supportare le funzionalità offline di Servizi mobili di Azure. Quindi, verranno aggiunti dati in uno scenario offline, verrà effettuata la sincronizzazione degli elementi con il database online, quindi si effettuerà l'accesso al portale di gestione di Azure per visualizzare le modifiche apportate durate l'esecuzione dell'app.

> [WACOM.NOTE] Per completare l'esercitazione, è necessario un account Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][versione di valutazione gratuita di Azure].

## Scaricare l'app esempio

**Un'esercitazione completa sarà disponibile a breve. Nel frattempo, vedere [l'elenco attività offline di esempio per iOS][l'elenco attività offline di esempio per iOS] e il [post di blog di accompagnamento][post di blog di accompagnamento].**

<!-- ## Next steps

* [How to use the Mobile Services client library for iOS]
   -->

<!-- URLs. -->

  [Windows Store C#]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data "Windows Store C#"
  [Windows Phone]: /it-it/documentation/articles/mobile-services-windows-phone-get-started-offline-data "Windows Phone"
  [iOS]: /it-it/documentation/articles/mobile-services-ios-get-started-offline-data "iOS"
  [Xamarin.iOS]: /it-it/documentation/articles/mobile-services-xamarin-ios-get-started-offline-data "Xamarin.iOS"
  [Xamarin.Android]: /it-it/documentation/articles/mobile-services-xamarin-android-get-started-offline-data "Xamarin.Android"
  [Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-ios-get-started/
  [Introduzione ai dati]: /it-it/documentation/articles/mobile-services-ios-get-started-data/
  [versione di valutazione gratuita di Azure]: http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=AE564AB28
  [l'elenco attività offline di esempio per iOS]: https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS/blog20140611
  [post di blog di accompagnamento]: http://aka.ms/iosoffline
