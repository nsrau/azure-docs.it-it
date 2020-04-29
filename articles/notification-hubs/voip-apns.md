---
title: Inviare notifiche APNS VOIP con hub di notifica di Azure
description: Informazioni su come inviare notifiche VOIP APNS tramite hub di notifica di Azure (non supportato ufficialmente).
author: sethmanheim
ms.author: sethm
ms.date: 3/23/2020
ms.topic: how-to
ms.service: notification-hubs
ms.openlocfilehash: c99af881b8f93b75633741c2352dc5df17dd2963
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80146888"
---
# <a name="use-apns-voip-through-notification-hubs-not-officially-supported"></a>Usare APNS VOIP tramite hub di notifica (non ufficialmente supportato)

È possibile usare le notifiche VOIP di APNS tramite hub di notifica di Azure. Tuttavia, non esiste alcun supporto ufficiale per questo scenario.

## <a name="considerations"></a>Considerazioni

Se si sceglie ancora di inviare notifiche VOIP APNS tramite hub di notifica, tenere presenti le limitazioni seguenti:

- Per inviare una notifica VOIP è `apns-topic` necessario che l'intestazione sia impostata sull'ID bundle dell'applicazione `.voip` + il suffisso. Ad esempio, per un'app di esempio con ID `com.microsoft.nhubsample`bundle, l' `apns-topic` intestazione deve essere impostata su`com.microsoft.nhubsample.voip.`

   Questo metodo non funziona bene con hub di notifica di Azure, perché l'ID bundle dell'app deve essere configurato come parte delle credenziali APNS dell'hub e il valore non può essere modificato. Hub di notifica, inoltre, non consente di eseguire l' `apns-topic` override del valore dell'intestazione in fase di esecuzione.

   Per inviare notifiche VOIP, è necessario configurare un hub di notifica separato con `.voip` l'ID bundle dell'app.

- Per inviare una notifica VOIP è `apns-push-type` necessario che l'intestazione sia impostata sul `voip`valore.

   Per consentire ai clienti di passare a iOS 13, hub di notifica tenta di dedurre il valore corretto `apns-push-type` per l'intestazione. La logica di inferenza è intenzionalmente semplice, allo scopo di evitare interruzioni delle notifiche standard. Sfortunatamente, questo metodo causa problemi con le notifiche VOIP, perché Apple considera le notifiche VOIP come un caso speciale che non segue le stesse regole delle notifiche standard.

   Per inviare notifiche VOIP, è necessario specificare un valore esplicito per `apns-push-type` l'intestazione.

- Hub di notifica limita i payload APNS a 4 KB, come documentato da Apple. Per le notifiche VOIP, Apple consente i payload fino a 5 KB. Hub di notifica non distingue tra le notifiche standard e VOIP. Pertanto, tutte le notifiche sono limitate a 4 KB.

   Per inviare notifiche VOIP, non è necessario superare il limite di dimensioni del payload di 4 KB.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere i seguenti collegamenti:

- [Documentazione per `apns-topic` intestazioni `apns-push-type` e valori di e, inclusi i casi speciali per le notifiche VoIP](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns).

- [Documentazione per il limite delle dimensioni del payload](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification).

- [Aggiornamenti di hub di notifica per iOS 13](push-notification-updates-ios-13.md#apns-push-type).
