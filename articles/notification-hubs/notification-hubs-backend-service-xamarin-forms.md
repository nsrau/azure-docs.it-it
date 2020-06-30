---
title: Inviare notifiche push alle app Xamarin.Forms con Hub di notifica di Azure tramite un servizio back-end | Microsoft Docs
description: Informazioni su come inviare notifiche push alle app Xamarin.Forms che usano Hub di notifica di Azure tramite un servizio back-end.
author: mikeparker104
ms.service: notification-hubs
ms.topic: tutorial
ms.date: 06/02/2020
ms.author: miparker
ms.openlocfilehash: 5d8ad51c06411e46871fe16022538dfc61adbf5c
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85248939"
---
# <a name="tutorial-send-push-notifications-to-xamarinforms-apps-using-azure-notification-hubs-via-a-backend-service"></a>Esercitazione: Inviare notifiche push alle app Xamarin.Forms con Hub di notifica di Azure tramite un servizio back-end  

[![Scarica esempio](./media/notification-hubs-backend-service-xamarin-forms/download.png) Scaricare l'esempio](https://github.com/xamcat/mobcat-samples/tree/master/notification_hub_backend_service)  

In questa esercitazione viene usato [Hub di notifica di Azure](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-overview) per inviare notifiche push a un'applicazione [Xamarin.Forms](https://dotnet.microsoft.com/apps/xamarin/xamarin-forms) per **Android** e **iOS**.  

[!INCLUDE [Notification Hubs Backend Service Introduction](../../includes/notification-hubs-backend-service-introduction.md)]

Durante l'esercitazione vengono eseguiti i passaggi seguenti:

> [!div class="checklist"]
>
> * [Configurare i servizi di notifica push e Hub di notifica di Azure.](#set-up-push-notification-services-and-azure-notification-hub)
> * [Creare un'applicazione back-end API Web ASP.NET Core.](#create-an-aspnet-core-web-api-backend-application)
> * [Creare un'applicazione Xamarin.Forms multipiattaforma.](#create-a-cross-platform-xamarinforms-application)
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
* [Google Firebase Console](https://console.firebase.google.com/u/0/)
* [Microsoft Azure](https://portal.azure.com) e [Inviare notifiche push alle app iOS con Hub di notifica di Azure](ios-sdk-get-started.md).
* [Xamarin](https://dotnet.microsoft.com/apps/xamarin) e [Xamarin.Forms](https://dotnet.microsoft.com/apps/xamarin/xamarin-forms).

I passaggi descritti sono relativi a [Visual Studio per Mac](https://visualstudio.microsoft.com/vs/mac/), ma è possibile seguire l'esercitazione anche usando [Visual Studio 2019](https://visualstudio.microsoft.com/vs).

## <a name="set-up-push-notification-services-and-azure-notification-hub"></a>Configurare i servizi di notifica push e Hub di notifica di Azure

In questa sezione vengono configurati **[Firebase Cloud Messaging (FCM)](https://firebase.google.com/docs/cloud-messaging)** e **[Apple Push Notification Services (APNS)](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html)** . Viene quindi creato e configurato un hub di notifica da usare con questi servizi.

[!INCLUDE [Create a Firebase project and enable Firebase Cloud Messaging](../../includes/notification-hubs-common-enable-firebase-cloud-messaging.md)]

[!INCLUDE [Enable Apple Push Notifications](../../includes/notification-hubs-common-enable-apple-push-notifications.md)]

[!INCLUDE [Create a notification hub](../../includes/notification-hubs-common-create-notification-hub.md)]

[!INCLUDE [Configure your notification hub with APNs information](../../includes/notification-hubs-common-configure-with-apns-information.md)]

[!INCLUDE [Configure your notification hub with FCM information](../../includes/notification-hubs-common-configure-with-fcm-information.md)]

## <a name="create-an-aspnet-core-web-api-backend-application"></a>Creare un'applicazione back-end API Web ASP.NET Core

In questa sezione viene creato il back-end dell'[API Web ASP.NET Core](https://dotnet.microsoft.com/apps/aspnet/apis) per gestire la [registrazione del dispositivo](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#what-is-device-registration) e l'invio delle notifiche all'app per dispositivi mobili Xamarin.Forms.

[!INCLUDE [Create an ASP.NET Core Web API backend application](../../includes/notification-hubs-backend-service-web-api.md)]

## <a name="create-a-cross-platform-xamarinforms-application"></a>Creare un'applicazione Xamarin.Forms multipiattaforma

In questa sezione viene compilata un'applicazione per dispositivi mobili [Xamarin.Forms](https://dotnet.microsoft.com/apps/xamarin/xamarin-forms) che implementa le notifiche push in modalità multipiattaforma.

[!INCLUDE [Sample application generic overview](../../includes/notification-hubs-backend-service-sample-app-overview.md)]

[!INCLUDE [Create Xamarin.Forms application](../../includes/notification-hubs-backend-service-sample-app-xamarin-forms.md)]

## <a name="configure-the-native-android-project-for-push-notifications"></a>Configurare il progetto Android nativo per le notifiche push

[!INCLUDE [Configure the native Android project](../../includes/notification-hubs-backend-service-configure-xamarin-android.md)]

## <a name="configure-the-native-ios-project-for-push-notifications"></a>Configurare il progetto iOS nativo per le notifiche push

[!INCLUDE [Configure the native iOS project](../../includes/notification-hubs-backend-service-configure-xamarin-ios.md)]

## <a name="test-the-solution"></a>Testare la soluzione

A questo punto è possibile testare l'invio di notifiche tramite il servizio back-end.

[!INCLUDE [Testing the solution](../../includes/notification-hubs-backend-service-testing.md)]

## <a name="next-steps"></a>Passaggi successivi

Si dispone ora di un'app Xamarin.Forms di base connessa a un hub di notifica tramite un servizio back-end e in grado di inviare e ricevere notifiche.

[!INCLUDE [Next steps](../../includes/notification-hubs-backend-service-next-steps.md)]

## <a name="troubleshooting"></a>Risoluzione dei problemi

[!INCLUDE [Troubleshooting](../../includes/notification-hubs-backend-service-troubleshooting.md)]

## <a name="related-links"></a>Collegamenti correlati

* [Panoramica di Hub di notifica di Azure](notification-hubs-push-notification-overview.md)
* [Installazione di Visual Studio per Mac](https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019)
* [Installazione di Xamarin in Windows](https://docs.microsoft.com/xamarin/get-started/installation/windows)
* [SDK di Hub di notifica per le operazioni back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
* [SDK di Hub di notifica su GitHub](https://github.com/Azure/azure-notificationhubs)
* [Registrazione con il back-end dell'applicazione](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
* [Gestione delle registrazioni](notification-hubs-push-notification-registration-management.md)
* [Uso dei tag](notification-hubs-tags-segment-push-message.md)
* [Uso di modelli personalizzati](notification-hubs-templates-cross-platform-push-messages.md)
