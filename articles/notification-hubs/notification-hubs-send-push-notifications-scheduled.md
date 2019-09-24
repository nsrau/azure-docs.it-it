---
title: Informazioni su come inviare notifiche pianificate | Microsoft Docs
description: Questo argomento descrive l'uso di notifiche pianificate con Hub di notifica.
services: notification-hubs
documentationcenter: .net
keywords: notifiche push, notifica push, pianificazione notifiche push
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 6b718c75-75dd-4c99-aee3-db1288235c1a
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: ef5eedaa903480ae670f9bc48d0af89744a99d22
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213011"
---
# <a name="how-to-send-scheduled-notifications"></a>Procedura: Inviare notifiche pianificate

Si consideri uno scenario in cui si desidera inviare una notifica in un certo momento in futuro, ma non si dispone di una soluzione semplice per riattivare il codice di back-end per l'invio della notifica. Il livello Standard Hub di notifica supporta una funzionalità che consente di pianificare le notifiche fino a 7 giorni prima dell'invio.


## <a name="schedule-your-notifications"></a>Pianificare le notifiche
Per l'invio di una notifica è sufficiente usare la classe [`ScheduledNotification`](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx) nell'SDK di Hub di notifica, come illustrato nell'esempio seguente:

```csharp
Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));
```

## <a name="cancel-scheduled-notifications"></a>Annullare notifiche pianificate
È anche possibile annullare una notifica pianificata in precedenza usando il relativo notificationId:

```csharp
await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);
```

Non sono previsti limiti al numero di notifiche pianificate che è possibile inviare.

## <a name="next-steps"></a>Passaggi successivi

Vedere le esercitazioni seguenti:

 - [Eseguire il push di notifiche a tutti i dispositivi registrati](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
 - [Eseguire il push di notifiche a dispositivi specifici](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)
 - [Eseguire il push di notifiche localizzate](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
 - [Eseguire il push di notifiche a utenti specifici](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) 
 - [Eseguire il push di notifiche basate sulla posizione](notification-hubs-push-bing-spatial-data-geofencing-notification.md)
