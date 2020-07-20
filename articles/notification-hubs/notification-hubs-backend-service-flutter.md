---
title: Inviare notifiche push alle app Flutter con Hub di notifica di Azure tramite un servizio back-end | Microsoft Docs
description: Informazioni su come inviare notifiche push alle app Flutter che usano Hub di notifica di Azure tramite un servizio back-end.
author: mikeparker104
ms.service: notification-hubs
ms.topic: tutorial
ms.date: 07/07/2020
ms.author: miparker
ms.openlocfilehash: 5fa753a6b8b1284c4f8fcd046f74fabcbae3f8fb
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171007"
---
# <a name="tutorial-send-push-notifications-to-flutter-apps-using-azure-notification-hubs-via-a-backend-service"></a>Esercitazione: Inviare notifiche push alle app Flutter con Hub di notifica di Azure tramite un servizio back-end  

[![Scarica esempio](./media/notification-hubs-backend-service-flutter/download.png) Scaricare l'esempio](https://github.com/xamcat/mobcat-samples/tree/master/notification_hub_backend_service)  

> [!div class="op_single_selector"]
>
> * [Xamarin.Forms](notification-hubs-backend-service-xamarin-forms.md)
> * [Flutter](notification-hubs-backend-service-flutter.md)
> * [React Native](notification-hubs-backend-service-react-native.md)

In questa esercitazione viene usato [Hub di notifica di Azure](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-overview) per inviare notifiche push a un'applicazione [Flutter](https://flutter.dev) per **Android** e **iOS**.  

[!INCLUDE [Notification Hubs Backend Service Introduction](../../includes/notification-hubs-backend-service-introduction.md)]

Durante l'esercitazione vengono eseguiti i passaggi seguenti:

> [!div class="checklist"]
>
> * [Configurare i servizi di notifica push e Hub di notifica di Azure.](#set-up-push-notification-services-and-azure-notification-hub)
> * [Creare un'applicazione back-end API Web ASP.NET Core.](#create-an-aspnet-core-web-api-backend-application)
> * [Creare un'applicazione Flutter multipiattaforma.](#create-a-cross-platform-flutter-application)
> * [Configurare il progetto Android nativo per le notifiche push.](#configure-the-native-android-project-for-push-notifications)
> * [Configurare il progetto iOS nativo per le notifiche push.](#configure-the-native-ios-project-for-push-notifications)
> * [Testare la soluzione.](#test-the-solution)

## <a name="prerequisites"></a>Prerequisiti

Per seguire l'esercitazione occorre:

* Una [sottoscrizione di Azure](https://portal.azure.com) in cui sia possibile creare e gestire risorse.
* Il toolkit [Flutter](https://flutter.dev/docs/get-started/install) (unitamente ai relativi prerequisiti).
* [Visual Studio Code](https://code.visualstudio.com) con i plug-in [Flutter e Dart](https://flutter.dev/docs/get-started/editor?tab=vscode) installati.
* La possibilità di eseguire l'app in **Android** (dispositivo fisico o emulatore) o **iOS** (solo dispositivo fisico).

Per Android, è necessario avere:

* Un dispositivo fisico sbloccato per lo sviluppo o un emulatore *(che esegue l'API 26 e versioni successive con Google Play Services)* .

Per iOS, è necessario avere:

* Un [account Apple Developer](https://developer.apple.com) attivo.
* Un dispositivo fisico iOS [registrato con l'account per sviluppatore](https://help.apple.com/developer-account/#/dev40df0d9fa) *(che esegue iOS 13.0 e versioni successive)* .
* Un [certificato di sviluppo](https://help.apple.com/developer-account/#/dev04fd06d56) **.p12** installato nel **keychain** che consenta di [eseguire un'app su un dispositivo fisico](https://help.apple.com/xcode/mac/current/#/dev5a825a1ca).
* [CocoaPods](https://guides.cocoapods.org/using/getting-started.html#installation) installato per la gestione delle dipendenze della libreria.

> [!NOTE]
> Il simulatore iOS non supporta le notifiche remote, quindi è necessario un dispositivo fisico per esplorare questo esempio in iOS. Non è però necessario eseguire l'app sia in **Android** che in **iOS** per completare questa esercitazione.

Non occorre avere un'esperienza precedente per seguire i passaggi di questo esempio. È tuttavia utile avere familiarità con gli aspetti seguenti.

* [Portale Apple Developer](https://developer.apple.com)
* [ASP.NET Core](https://docs.microsoft.com/aspnet/core/introduction-to-aspnet-core?view=aspnetcore-3.1)
* [Hub di notifica di Azure](notification-hubs-push-notification-overview.md)
* [Google Firebase Console](https://console.firebase.google.com/u/0/)
* [Flutter](https://flutter.dev) e [Dart](https://dart.dev) per lo sviluppo multipiattaforma
* [Kotlin](https://kotlinlang.org) e [Swift](https://developer.apple.com/swift) per lo sviluppo nativo per Android e iOS

I passaggi forniti sono specifici per [macOS](https://developer.apple.com/macos). È possibile eseguire la procedura per [Windows](https://www.microsoft.com/windows) ignorando gli aspetti specifici di **iOS**.

## <a name="set-up-push-notification-services-and-azure-notification-hub"></a>Configurare i servizi di notifica push e Hub di notifica di Azure

In questa sezione vengono configurati **[Firebase Cloud Messaging (FCM)](https://firebase.google.com/docs/cloud-messaging)** e **[Apple Push Notification Services (APNS)](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html)** . Viene quindi creato e configurato un hub di notifica da usare con questi servizi.

[!INCLUDE [Create a Firebase project and enable Firebase Cloud Messaging](../../includes/notification-hubs-common-enable-firebase-cloud-messaging.md)]

[!INCLUDE [Enable Apple Push Notifications](../../includes/notification-hubs-common-enable-apple-push-notifications.md)]

[!INCLUDE [Create a notification hub](../../includes/notification-hubs-common-create-notification-hub.md)]

[!INCLUDE [Configure your notification hub with APNs information](../../includes/notification-hubs-common-configure-with-apns-information.md)]

[!INCLUDE [Configure your notification hub with FCM information](../../includes/notification-hubs-common-configure-with-fcm-information.md)]

## <a name="create-an-aspnet-core-web-api-backend-application"></a>Creare un'applicazione back-end API Web ASP.NET Core

In questa sezione viene creato il back-end dell'[API Web ASP.NET Core](https://dotnet.microsoft.com/apps/aspnet/apis) per gestire la [registrazione del dispositivo](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#what-is-device-registration) e l'invio delle notifiche all'app per dispositivi mobili Flutter.

[!INCLUDE [Create an ASP.NET Core Web API backend application](../../includes/notification-hubs-backend-service-web-api.md)]

## <a name="create-a-cross-platform-flutter-application"></a>Creare un'applicazione Flutter multipiattaforma

In questa sezione viene compilata un'applicazione per dispositivi mobili [Flutter](https://flutter.dev) che implementa le notifiche push in modalità multipiattaforma.

[!INCLUDE [Sample application generic overview](../../includes/notification-hubs-backend-service-sample-app-overview.md)]

[!INCLUDE [Create Flutter application](../../includes/notification-hubs-backend-service-sample-app-flutter.md)]

## <a name="configure-the-native-android-project-for-push-notifications"></a>Configurare il progetto Android nativo per le notifiche push

[!INCLUDE [Configure the native Android project](../../includes/notification-hubs-backend-service-configure-flutter-android.md)]

## <a name="configure-the-native-ios-project-for-push-notifications"></a>Configurare il progetto iOS nativo per le notifiche push

[!INCLUDE [Configure the native iOS project](../../includes/notification-hubs-backend-service-configure-flutter-ios.md)]

## <a name="test-the-solution"></a>Testare la soluzione

A questo punto è possibile testare l'invio di notifiche tramite il servizio back-end.

[!INCLUDE [Testing the solution](../../includes/notification-hubs-backend-service-testing.md)]

## <a name="next-steps"></a>Passaggi successivi

Si dispone ora di un'app Flutter di base connessa a un hub di notifica tramite un servizio back-end e in grado di inviare e ricevere notifiche.

[!INCLUDE [Next steps](../../includes/notification-hubs-backend-service-next-steps.md)]

## <a name="troubleshooting"></a>Risoluzione dei problemi

[!INCLUDE [Troubleshooting](../../includes/notification-hubs-backend-service-troubleshooting.md)]

## <a name="related-links"></a>Collegamenti correlati

* [Panoramica di Hub di notifica di Azure](notification-hubs-push-notification-overview.md)
* [Installazione di Flutter in macOS](https://flutter.dev/docs/get-started/install/macos)
* [Installazione di Flutter in Windows](https://flutter.dev/docs/get-started/install/windows)
* [SDK di Hub di notifica per le operazioni back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
* [SDK di Hub di notifica su GitHub](https://github.com/Azure/azure-notificationhubs)
* [Registrazione con il back-end dell'applicazione](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
* [Gestione delle registrazioni](notification-hubs-push-notification-registration-management.md)
* [Uso dei tag](notification-hubs-tags-segment-push-message.md)
* [Uso di modelli personalizzati](notification-hubs-templates-cross-platform-push-messages.md)
