---
title: Informazioni su come inviare notifiche pianificate | Microsoft Docs
description: Questo argomento descrive l&quot;uso di notifiche pianificate con Hub di notifica.
services: notification-hubs
documentationcenter: .net
keywords: notifiche push, notifica push, pianificazione notifiche push
author: ysxu
manager: erikre
editor: 
ms.assetid: 6b718c75-75dd-4c99-aee3-db1288235c1a
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: efac6e1ecc00359f1622d380333140bc055c83e0


---
# <a name="how-to-send-scheduled-notifications"></a>Procedura: Inviare le notifiche pianificate
## <a name="overview"></a>Panoramica
Si consideri uno scenario in cui si desidera inviare una notifica in un certo momento in futuro, ma non si dispone di una soluzione semplice per riattivare il codice di back-end per l'invio della notifica. Il livello Standard Hub di notifica supporta una funzionalità che consente di pianificare le notifiche fino a 7 giorni prima dell'invio.

Per l'invio di una notifica è sufficiente usare la classe [ScheduledNotification](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx) classe nell'SDK di Hub di notifica, come illustrato nell'esempio seguente:

    Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
    var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));

È anche possibile annullare una notifica pianificata in precedenza usando il relativo notificationId:

    await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);

Non sono previsti limiti al numero di notifiche pianificate che è possibile inviare.




<!--HONumber=Nov16_HO3-->


