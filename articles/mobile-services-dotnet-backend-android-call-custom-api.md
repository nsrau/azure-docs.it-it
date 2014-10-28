<properties writer="ricksal" pageTitle="Call a custom API from an Android client | Mobile Dev Center" metaKeywords="" description="Learn how to define a custom API and then call it from an Android app that uses Windows Azure Mobile Services." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="mobile-services" documentationCenter="Mobile" title="Call a custom API from the client" authors="glenga" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Chiamata di un'API personalizzata dal client

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api" title="Windows Store C#">Windows Store C#</a><a href="/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-call-custom-api" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-call-custom-api" title="Windows Phone">Windows Phone</a><a href="/it-it/documentation/articles/mobile-services-dotnet-backend-ios-call-custom-api" title="iOS">iOS</a><a href="/it-it/documentation/articles/mobile-services-dotnet-backend-android-call-custom-api" title="Android" class="current">Android</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/it-it/documentation/articles/mobile-services-dotnet-backend-android-call-custom-api" title=".NET backend" class="current">Back-end .NET</a> | <a href="/it-it/documentation/articles/mobile-services-android-call-custom-api"  title="JavaScript backend">Back-end JavaScript</a></div>

In questo argomento viene descritto come chiamare un'API personalizzata da un'app per Android. Un'API personalizzata consente di definire endpoint personalizzati che espongono la funzionalità del server di cui non è possibile eseguire il mapping a un'operazione di inserimento, aggiornamento, eliminazione o lettura. L'utilizzo di un'API personalizzata offre maggiore controllo sulla messaggistica, incluse la lettura e l'impostazione delle intestazioni del messaggio HTTP e la definizione di un formato del corpo del messaggio diverso da JSON.

L'API personalizzata creata in questo argomento consente di inviare una singola richiesta POST che imposta il flag *completed* su `true` per tutti gli elementi todo nella tabella del servizio mobile. Senza l'API personalizzata, il client dovrebbe inviare singole richieste per aggiornare il flag per ogni elemento Todo nella tabella.

Questa funzionalità verrà aggiunta all'app creata durante l'esercitazione [Introduzione a Servizi mobili][] o [Introduzione ai dati][]. A questo scopo, verranno eseguiti i passaggi seguenti:

1.  [Definizione dell'API personalizzata][]
2.  [Aggiornamento dell'app per la chiamata all'API personalizzata][]
3.  [Test dell'app][]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione a Servizi mobili][].

## <a name="define-custom-api"></a>Definizione dell'API personalizzata

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-custom-api][]]

[WACOM.INCLUDE [mobile-services-android-call-custom-api][]]

## Passaggi successivi

Dopo avere creato un'API personalizzata e averla chiamata dall'app per Android, per ulteriori informazioni, vedere anche gli argomenti relativi a Servizi mobili seguenti:

-   [Riferimento per gli script del server di Servizi mobili][]
    Ulteriori informazioni sulla creazione di API personalizzate.

-   [Archiviazione degli script del server nel controllo del codice sorgente][]
     Ulteriori informazioni su come utilizzare la funzionalità di controllo del codice sorgente per sviluppare e pubblicare in modo facile e sicuro il codice di script dell'API personalizzata.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [Windows Store C#]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api "Windows Store C#"
  [Windows Store JavaScript]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-call-custom-api "Windows Store JavaScript"
  [Windows Phone]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-call-custom-api "Windows Phone"
  [iOS]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-call-custom-api "iOS"
  [Android]: /it-it/documentation/articles/mobile-services-dotnet-backend-android-call-custom-api "Android"
  [Back-end .NET]: /it-it/documentation/articles/mobile-services-dotnet-backend-android-call-custom-api ".NET backend"
  [Back-end JavaScript]: /it-it/documentation/articles/mobile-services-android-call-custom-api "JavaScript backend"
  [Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-dotnet-backend-android-get-started/
  [Introduzione ai dati]: /it-it/documentation/articles/mobile-services-dotnet-backend-android-get-started-data/
  [Definizione dell'API personalizzata]: #define-custom-api
  [Aggiornamento dell'app per la chiamata all'API personalizzata]: #update-app
  [Test dell'app]: #test-app
  [mobile-services-dotnet-backend-create-custom-api]: ../includes/mobile-services-dotnet-backend-create-custom-api.md
  [mobile-services-android-call-custom-api]: ../includes/mobile-services-android-call-custom-api.md
  [Riferimento per gli script del server di Servizi mobili]: http://go.microsoft.com/fwlink/?LinkId=262293
  [Archiviazione degli script del server nel controllo del codice sorgente]: /it-it/documentation/articles/mobile-services-store-scripts-source-control
