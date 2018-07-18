---
title: Informazioni su come inviare notifiche pianificate | Microsoft Docs
description: Questo argomento descrive l'uso di notifiche pianificate con Hub di notifica.
services: notification-hubs
documentationcenter: .net
keywords: notifiche push, notifica push, pianificazione notifiche push
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 6b718c75-75dd-4c99-aee3-db1288235c1a
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: dotnet
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 0f4055a11d22604c0936685a7a2be3d56b259a5b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
ms.locfileid: "33776978"
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

