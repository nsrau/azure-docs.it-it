<properties pageTitle="How to enable Google Cloud Messaging" metaKeywords="" description="Follow this tutorial to create a new service using Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="How to create a new mobile service" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-android" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Come abilitare Google Cloud Messaging

Questo argomento descrive come abilitare un'app per Android per le notifiche push usando Google Cloud Messaging (GCM). La chiave API ottenuta viene usata per registrare l'app per Android per le notifiche push nel [portale di gestione di Azure][portale di gestione di Azure]. Per l'esercitazione completa, con informazioni sull'aggiornamento dell'app, vedere [Introduzione alle notifiche push][Introduzione alle notifiche push].

[WACOM.INCLUDE [Enable GCM][Enable GCM]]

A questo punto è possibile usare questo valore della chiave API per abilitare servizi per l'autenticazione con GCM e l'invio di notifiche push per conto dell'app.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->

  [portale di gestione di Azure]: https://manage.windowsazure.com/
  [Introduzione alle notifiche push]: /it-it/documentation/articles/mobile-services-javascript-backend-android-get-started-push/
  [Enable GCM]: ../includes/mobile-services-enable-Google-cloud-messaging.md
