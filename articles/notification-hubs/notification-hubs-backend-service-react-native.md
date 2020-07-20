---
title: Inviare notifiche push alle app React Native con Hub di notifica di Azure tramite un servizio back-end | Microsoft Docs
description: Informazioni su come inviare notifiche push alle app React Native che usano Hub di notifica di Azure tramite un servizio back-end.
author: alexeystrakh
ms.service: notification-hubs
ms.topic: tutorial
ms.date: 06/11/2020
ms.author: alstrakh
ms.openlocfilehash: 007be386b7b64fd3461fa508d35a4ef9be377c1f
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86170911"
---
# <a name="tutorial-send-push-notifications-to-react-native-apps-using-azure-notification-hubs-via-a-backend-service"></a>Esercitazione: Inviare notifiche push alle app React Native con Hub di notifica di Azure tramite un servizio back-end  

[![Scarica esempio](./media/notification-hubs-backend-service-react-native/download.png) Scaricare l'esempio](https://github.com/xamcat/mobcat-samples/tree/master/notification_hub_backend_service)  

> [!div class="op_single_selector"]
>
> * [Xamarin.Forms](notification-hubs-backend-service-xamarin-forms.md)
> * [Flutter](notification-hubs-backend-service-flutter.md)
> * [React Native](notification-hubs-backend-service-react-native.md)

In questa esercitazione viene usato [Hub di notifica di Azure](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-overview) per inviare notifiche push a un'applicazione [React Native](https://reactnative.dev/) per **Android** e **iOS**.  

[!INCLUDE [Notification Hubs Backend Service Introduction](../../includes/notification-hubs-backend-service-introduction.md)]

Durante l'esercitazione vengono eseguiti i passaggi seguenti:

> [!div class="checklist"]
>
> * [Configurare i servizi di notifica push e Hub di notifica di Azure.](#set-up-push-notification-services-and-azure-notification-hub)
> * [Creare un'applicazione back-end API Web ASP.NET Core.](#create-an-aspnet-core-web-api-backend-application)
> * [Creare un'applicazione React Native multipiattaforma.](#create-a-cross-platform-react-native-application)
> * [Configurare il progetto Android nativo per le notifiche push.](#configure-the-native-android-project-for-push-notifications)
> * [Configurare il progetto iOS nativo per le notifiche push.](#configure-the-native-ios-project-for-push-notifications)
> * [Testare la soluzione.](#test-the-solution)

## <a name="prerequisites"></a>Prerequisiti

Per seguire l'esercitazione occorre:

* Una [sottoscrizione di Azure](https://portal.azure.com) in cui sia possibile creare e gestire risorse.
* Un Mac con [Visual Studio per Mac](https://visualstudio.microsoft.com/vs/mac/) installato (o un PC che esegue [Visual Studio 2019](https://visualstudio.microsoft.com/vs) con il carico di lavoro **Sviluppo di applicazioni per dispositivi mobili con .NET**).
* La possibilità di eseguire l'app in **Android** (dispositivo fisico o emulatore) o **iOS** (solo dispositivo fisico).

Per Android, è necessario avere:

* Un dispositivo fisico sbloccato per lo sviluppo o un emulatore *(che esegue l'API 26 e versioni successive con Google Play Services)* .

Per iOS, è necessario avere:

* Un [account Apple Developer](https://developer.apple.com) attivo.
* Un dispositivo fisico iOS [registrato con l'account per sviluppatore](https://help.apple.com/developer-account/#/dev40df0d9fa) *(che esegue iOS 13.0 e versioni successive)* .
* Un [certificato di sviluppo](https://help.apple.com/developer-account/#/dev04fd06d56) **.p12** installato nel **keychain** che consenta di [eseguire un'app su un dispositivo fisico](https://help.apple.com/xcode/mac/current/#/dev5a825a1ca).

> [!NOTE]
> Il simulatore iOS non supporta le notifiche remote, quindi è necessario un dispositivo fisico per esplorare questo esempio in iOS. Non è però necessario eseguire l'app sia in **Android** che in **iOS** per completare questa esercitazione.

Non occorre avere un'esperienza precedente per seguire i passaggi di questo esempio. È tuttavia utile avere familiarità con gli aspetti seguenti.

* [Portale Apple Developer](https://developer.apple.com)
* [ASP.NET Core](https://docs.microsoft.com/aspnet/core/introduction-to-aspnet-core?view=aspnetcore-3.1)
* [Hub di notifica di Azure](notification-hubs-push-notification-overview.md)
* [Google Firebase Console](https://console.firebase.google.com/u/0/)
* [React Native](https://reactnative.dev/docs/getting-started)

I passaggi descritti sono relativi a [Visual Studio per Mac](https://visualstudio.microsoft.com/vs/mac/) e [Visual Studio Code](https://code.visualstudio.com/download), ma è possibile seguire l'esercitazione anche usando [Visual Studio 2019](https://visualstudio.microsoft.com/vs).

## <a name="set-up-push-notification-services-and-azure-notification-hub"></a>Configurare i servizi di notifica push e Hub di notifica di Azure

In questa sezione vengono configurati **[Firebase Cloud Messaging (FCM)](https://firebase.google.com/docs/cloud-messaging)** e **[Apple Push Notification Services (APNS)](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html)** . Viene quindi creato e configurato un hub di notifica da usare con questi servizi.

[!INCLUDE [Create a Firebase project and enable Firebase Cloud Messaging](../../includes/notification-hubs-common-enable-firebase-cloud-messaging.md)]

[!INCLUDE [Enable Apple Push Notifications](../../includes/notification-hubs-common-enable-apple-push-notifications.md)]

[!INCLUDE [Create a notification hub](../../includes/notification-hubs-common-create-notification-hub.md)]

[!INCLUDE [Configure your notification hub with APNs information](../../includes/notification-hubs-common-configure-with-apns-information.md)]

[!INCLUDE [Configure your notification hub with FCM information](../../includes/notification-hubs-common-configure-with-fcm-information.md)]

## <a name="create-an-aspnet-core-web-api-backend-application"></a>Creare un'applicazione back-end API Web ASP.NET Core

In questa sezione viene creato il back-end dell'[API Web ASP.NET Core](https://dotnet.microsoft.com/apps/aspnet/apis) per gestire la [registrazione del dispositivo](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#what-is-device-registration) e l'invio delle notifiche all'app per dispositivi mobili React Native.

[!INCLUDE [Create an ASP.NET Core Web API backend application](../../includes/notification-hubs-backend-service-web-api.md)]

## <a name="create-a-cross-platform-react-native-application"></a>Creare un'applicazione React Native multipiattaforma

In questa sezione viene compilata un'applicazione per dispositivi mobili [React Native](https://reactnative.dev/) che implementa le notifiche push in modalità multipiattaforma.

[!INCLUDE [Sample application generic overview](../../includes/notification-hubs-backend-service-sample-app-overview.md)]

[!INCLUDE [Create React Native application](../../includes/notification-hubs-backend-service-sample-app-reactnative.md)]

## <a name="configure-the-native-android-project-for-push-notifications"></a>Configurare il progetto Android nativo per le notifiche push

[!INCLUDE [Configure the native Android project](../../includes/notification-hubs-backend-service-configure-reactnative-android.md)]

## <a name="configure-the-native-ios-project-for-push-notifications"></a>Configurare il progetto iOS nativo per le notifiche push

[!INCLUDE [Configure the native iOS project](../../includes/notification-hubs-backend-service-configure-reactnative-ios.md)]

## <a name="test-the-solution"></a>Testare la soluzione

A questo punto è possibile testare l'invio di notifiche tramite il servizio back-end.

[!INCLUDE [Testing the solution](../../includes/notification-hubs-backend-service-testing.md)]

## <a name="next-steps"></a>Passaggi successivi

Si dispone ora di un'app React Native di base connessa a un hub di notifica tramite un servizio back-end e in grado di inviare e ricevere notifiche.

[!INCLUDE [Next steps](../../includes/notification-hubs-backend-service-next-steps.md)]

## <a name="troubleshooting"></a>Risoluzione dei problemi

[!INCLUDE [Troubleshooting](../../includes/notification-hubs-backend-service-troubleshooting.md)]

## <a name="related-links"></a>Collegamenti correlati

* [Panoramica di Hub di notifica di Azure](notification-hubs-push-notification-overview.md)
* [Installazione di Visual Studio per Mac](https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019)
* [Installazione di Visual Studio Code](https://code.visualstudio.com/download)
* [Configurazione dell'ambiente di sviluppo per React Native](https://reactnative.dev/docs/environment-setup)
* [SDK di Hub di notifica per le operazioni back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
* [SDK di Hub di notifica su GitHub](https://github.com/Azure/azure-notificationhubs)
* [Registrazione con il back-end dell'applicazione](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
* [Gestione delle registrazioni](notification-hubs-push-notification-registration-management.md)
* [Uso dei tag](notification-hubs-tags-segment-push-message.md)
* [Uso di modelli personalizzati](notification-hubs-templates-cross-platform-push-messages.md)
