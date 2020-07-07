---
title: Inviare notifiche APNS VOIP con hub di notifica di Azure
description: Informazioni su come inviare notifiche VOIP APNS tramite hub di notifica di Azure (non supportato ufficialmente).
author: sethmanheim
ms.author: sethm
ms.date: 3/23/2020
ms.topic: how-to
ms.service: notification-hubs
ms.openlocfilehash: c99af881b8f93b75633741c2352dc5df17dd2963
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80146888"
---
# <a name="use-apns-voip-through-notification-hubs-not-officially-supported"></a>Usare APNS VOIP tramite hub di notifica (non ufficialmente supportato)

È possibile usare le notifiche VOIP di APNS tramite hub di notifica di Azure. Tuttavia, non esiste alcun supporto ufficiale per questo scenario.

## <a name="considerations"></a>Considerazioni

Se si sceglie ancora di inviare notifiche VOIP APNS tramite hub di notifica, tenere presenti le limitazioni seguenti:

- Per inviare una notifica VOIP è necessario che l' `apns-topic` intestazione sia impostata sull'ID bundle dell'applicazione + il `.voip` suffisso. Ad esempio, per un'app di esempio con ID bundle `com.microsoft.nhubsample` , l' `apns-topic` intestazione deve essere impostata su`com.microsoft.nhubsample.voip.`

   Questo metodo non funziona bene con hub di notifica di Azure, perché l'ID bundle dell'app deve essere configurato come parte delle credenziali APNS dell'hub e il valore non può essere modificato. Hub di notifica, inoltre, non consente di eseguire l'override del valore dell' `apns-topic` intestazione in fase di esecuzione.

   Per inviare notifiche VOIP, è necessario configurare un hub di notifica separato con l' `.voip` ID bundle dell'app.

- Per inviare una notifica VOIP `apns-push-type` è necessario che l'intestazione sia impostata sul `voip` valore.

   Per consentire ai clienti di passare a iOS 13, hub di notifica tenta di dedurre il valore corretto per l' `apns-push-type` intestazione. La logica di inferenza è intenzionalmente semplice, allo scopo di evitare interruzioni delle notifiche standard. Sfortunatamente, questo metodo causa problemi con le notifiche VOIP, perché Apple considera le notifiche VOIP come un caso speciale che non segue le stesse regole delle notifiche standard.

   Per inviare notifiche VOIP, è necessario specificare un valore esplicito per l' `apns-push-type` intestazione.

- Hub di notifica limita i payload APNS a 4 KB, come documentato da Apple. Per le notifiche VOIP, Apple consente i payload fino a 5 KB. Hub di notifica non distingue tra le notifiche standard e VOIP. Pertanto, tutte le notifiche sono limitate a 4 KB.

   Per inviare notifiche VOIP, non è necessario superare il limite di dimensioni del payload di 4 KB.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere i seguenti collegamenti:

- [Documentazione per `apns-topic` intestazioni e valori di e `apns-push-type` , inclusi i casi speciali per le notifiche VoIP](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns).

- [Documentazione per il limite delle dimensioni del payload](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification).

- [Aggiornamenti di hub di notifica per iOS 13](push-notification-updates-ios-13.md#apns-push-type).
