---
title: Aggiungere notifiche push all'app per iOS con le app per dispositivi mobili di Azure
description: Informazioni su come usare le app per dispositivi mobili di Azure per inviare notifiche push all'app per iOS.
services: app-service\mobile
documentationcenter: ios
manager: crdun
editor: ''
author: elamalani
ms.assetid: fa503833-d23e-4925-8d93-341bb3fbab7d
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 7a037cfb411eb3c15f60bb6a8763374d9102bc4e
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027313"
---
# <a name="add-push-notifications-to-your-ios-app"></a>Aggiungere notifiche push all'app iOS

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center supporta end-to-end e servizi integrati centrali per lo sviluppo di app per dispositivi mobili. Gli sviluppatori possono utilizzare i servizi di **compilazione**, **test** e **distribuzione** per configurare la pipeline di integrazione e recapito continua. Una volta distribuita l'app, gli sviluppatori possono monitorare lo stato e l'utilizzo dell'app usando i servizi di **analisi** e **diagnostica** e coinvolgere gli utenti che usano il servizio di **push** . Gli sviluppatori possono inoltre sfruttare l' **autenticazione** per autenticare gli utenti e il servizio **dati** per salvare in modo permanente e sincronizzare i dati delle app nel cloud.
> Se si intende integrare servizi cloud nell'applicazione per dispositivi mobili, iscriversi con App Center [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) oggi stesso.

## <a name="overview"></a>Panoramica

In questa esercitazione vengono aggiunte notifiche push al progetto [avvio rapido di iOS], in modo che una notifica push venga inviata al dispositivo a ogni inserimento di record.

Se non si usa il progetto server di avvio rapido scaricato, sarà necessario aggiungere il pacchetto di estensione di notifica push. Per altre informazioni, vedere [Usare l'SDK del server back-end .NET per App per dispositivi mobili di Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

[Il simulatore iOS non supporta le notifiche push](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html). È necessario un dispositivo iOS fisico e un'[appartenenza all'Apple Developer Program](https://developer.apple.com/programs/ios/).

## <a name="configure-hub"></a>Configurare un hub di notifica

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a id="register"></a>Registrare l'app per le notifiche push

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Configurare Azure per l'invio di notifiche push

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a id="update-server"></a>Aggiornare il codice back-end per l'invio di notifiche push

[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-apns](../../includes/app-service-mobile-dotnet-backend-configure-push-apns.md)]

## <a id="add-push"></a>Aggiungere notifiche push all'app

[!INCLUDE [app-service-mobile-add-push-notifications-to-ios-app.md](../../includes/app-service-mobile-add-push-notifications-to-ios-app.md)]

## <a id="test"></a>Notifiche push di prova

[!INCLUDE [Test Push Notifications in App](../../includes/test-push-notifications-in-app.md)]

## <a id="more"></a>Altro

* I modelli offrono flessibilità per inviare notifiche push multipiattaforma e push localizzati. [Come usare la libreria client iOS per le app per dispositivi mobili di Azure](app-service-mobile-ios-how-to-use-client-library.md#templates) illustra come registrare modelli.

<!-- Anchors.  -->

<!-- Images. -->

<!-- URLs. -->
[Avvio rapido di iOS]: app-service-mobile-ios-get-started.md
