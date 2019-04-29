---
title: Informazioni su come inviare notifiche pianificate | Microsoft Docs
description: Questo argomento descrive l'uso di notifiche pianificate con Hub di notifica.
services: notification-hubs
documentationcenter: .net
keywords: notifiche push, notifica push, pianificazione notifiche push
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 6b718c75-75dd-4c99-aee3-db1288235c1a
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: dotnet
ms.topic: article
origin.date: 04/14/2018
ms.date: 02/25/2019
ms.author: v-biyu
ms.openlocfilehash: af0de9e8c18644f4ae200f6546c0dd0a41320f9f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61457686"
---
# <a name="how-to-send-scheduled-notifications"></a>Procedura: Inviare notifiche pianificate

Si consideri uno scenario in cui si desidera inviare una notifica in un certo momento in futuro, ma non si dispone di una soluzione semplice per riattivare il codice di back-end per l'invio della notifica. Il livello Standard Hub di notifica supporta una funzionalità che consente di pianificare le notifiche fino a 7 giorni prima dell'invio.


## <a name="schedule-your-notifications"></a>Pianificare le notifiche
Per l'invio di una notifica è sufficiente usare la classe [`ScheduledNotification`](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx) nell'SDK di Hub di notifica, come illustrato nell'esempio seguente:

```c#
Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));
```

## <a name="cancel-scheduled-notifications"></a>Annullare notifiche pianificate
È anche possibile annullare una notifica pianificata in precedenza usando il relativo notificationId:

```c#
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
