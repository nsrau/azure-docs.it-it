---
title: Aggiungere notifiche push all&quot;app per iOS con le app per dispositivi mobili di Azure
description: Informazioni su come usare le app per dispositivi mobili di Azure per inviare notifiche push all&quot;app per iOS.
services: app-service\mobile
documentationcenter: ios
manager: yochayk
editor: 
author: ysxu
ms.assetid: fa503833-d23e-4925-8d93-341bb3fbab7d
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 10/10/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a21ae4b13479f66ada053153834a2b3cc5687e5a


---
# <a name="add-push-notifications-to-your-ios-app"></a>Aggiungere notifiche push all'app iOS
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Panoramica
In questa esercitazione vengono aggiunte notifiche push al progetto [avvio rapido di iOS], in modo che una notifica push venga inviata al dispositivo a ogni inserimento di record.

Se non si usa il progetto server di avvio rapido scaricato, sarà necessario aggiungere il pacchetto di estensione di notifica push. Per altre informazioni, vedere [Usare l'SDK del server back-end .NET per App per dispositivi mobili di Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

[Il simulatore iOS non supporta le notifiche push](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html). È necessario un dispositivo iOS fisico e un'[appartenenza all'Apple Developer Program](https://developer.apple.com/programs/ios/).

## <a name="a-nameconfigure-hubaconfigure-notification-hub"></a><a name="configure-hub"></a>Configurare un hub di notifica
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="a-idregisteraregister-app-for-push-notifications"></a><a id="register"></a>Registrare l'app per le notifiche push
[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Configurare Azure per l'invio di notifiche push
[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a name="a-idupdate-serveraupdate-backend-to-send-push-notifications"></a><a id="update-server"></a>Aggiornare il codice back-end per l'invio di notifiche push
[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-apns](../../includes/app-service-mobile-dotnet-backend-configure-push-apns.md)]

## <a name="a-idadd-pushaadd-push-notifications-to-app"></a><a id="add-push"></a>Aggiungere notifiche push all'app
[!INCLUDE [app-service-mobile-add-push-notifications-to-ios-app.md](../../includes/app-service-mobile-add-push-notifications-to-ios-app.md)]

## <a name="a-idtestatest-push-notifications"></a><a id="test"></a>Notifiche push di prova
[!INCLUDE [Test Push Notifications in App](../../includes/test-push-notifications-in-app.md)]

## <a name="a-idmoreamore"></a><a id="more"></a>Altro
* I modelli offrono flessibilità per inviare notifiche push multipiattaforma e push localizzati. [Come usare la libreria client iOS per le app per dispositivi mobili di Azure](app-service-mobile-ios-how-to-use-client-library.md#templates) illustra come registrare modelli.

<!-- Anchors.  -->

<!-- Images. -->

<!-- URLs. -->
[avvio rapido di iOS]: app-service-mobile-ios-get-started.md



<!--HONumber=Nov16_HO3-->


