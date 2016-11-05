---
title: Introduzione a Servizi mobili per app per Xamarin Android | Microsoft Docs
description: Informazioni su come usare Servizi mobili di Azure e Hub di notifica per inviare notifiche push all'app per Xamarin Android.
services: mobile-services
documentationcenter: xamarin
author: ggailey777
manager: dwrede
editor: mollybos

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 07/21/2016
ms.author: glenga

---
# Aggiungere notifiche push all'app di Servizi mobili
[!INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

&nbsp;

[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

> Per la versione equivalente di questo argomento per le app per dispositivi mobili, vedere [Aggiungere notifiche push all'app Xamarin.Android](../app-service-mobile/app-service-mobile-xamarin-android-get-started-push.md).
> 
> 

## Panoramica
Questo argomento descrive come usare Servizi mobili di Azure per inviare notifiche push a un'app per Xamarin.Android. In questa esercitazione si userà il servizio Google Cloud Messaging (GCM) per aggiungere notifiche push al progetto [Introduzione a Servizi mobili]. Al termine dell'esercitazione, il servizio mobile invierà una notifica push ogni volta che viene inserito un record.

Per completare questa esercitazione, è necessario disporre di:

* Account Google attivo
* [Componente client di Google Cloud Messaging] Questo componente verrà aggiunto durante l'esercitazione.

Si presuppone che i componenti Xamarin.Android e [Servizi mobili di Azure][Azure Mobile Services Component] siano già installati nel progetto da quando è stata completata l'[Introduzione a Servizi mobili].

## <a id="register"></a>Abilitare Google Cloud Messaging
[!INCLUDE [Abilitare GCM](../../includes/mobile-services-enable-google-cloud-messaging.md)]

## <a id="configure"></a>Configurare il servizio mobile per l'invio di richieste push
[!INCLUDE [mobile-services-android-configure-push](../../includes/mobile-services-android-configure-push.md)]

## <a id="update-server"></a>Aggiornare il servizio per l'invio di notifiche push
[!INCLUDE [mobile-services-dotnet-backend-android-push-update-service](../../includes/mobile-services-dotnet-backend-android-push-update-service.md)]

## <a id="configure-app"></a>Configurare il progetto esistente per le notifiche push
[!INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

## <a id="add-push"></a>Aggiungere il codice delle notifiche push all'app
[!INCLUDE [mobile-services-xamarin-android-push-add-to-app](../../includes/mobile-services-xamarin-android-push-add-to-app.md)]

## <a name="test-app"></a>Testare l'app sul servizio mobile pubblicato
È possibile eseguire il test dell'app collegando direttamente un telefono Android con un cavo USB oppure usando un dispositivo virtuale nell'emulatore.

### <a id="local-testing"></a> Abilitare le notifiche push per i test locali
[!INCLUDE [mobile-services-dotnet-backend-configure-local-push](../../includes/mobile-services-dotnet-backend-configure-local-push.md)]

[!INCLUDE [mobile-services-android-push-notifications-test](../../includes/mobile-services-android-push-notifications-test.md)]

<!-- URLs. -->
[Introduzione a Servizi mobili]: mobile-services-dotnet-backend-xamarin-android-get-started.md
[Componente client di Google Cloud Messaging]: http://components.xamarin.com/view/GCMClient/
[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/

<!---HONumber=AcomDC_0727_2016-->