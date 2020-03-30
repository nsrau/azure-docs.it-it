---
title: Inviare notifiche VOIP APNS con Hub di notifica di AzureSend APNS VOIP notifications with Azure Notification Hubs
description: Informazioni su come inviare notifiche VOIP APNS tramite Hub di notifica di Azure (non ufficialmente supportato).
author: sethmanheim
ms.author: sethm
ms.date: 3/23/2020
ms.topic: how-to
ms.service: notification-hubs
ms.openlocfilehash: c99af881b8f93b75633741c2352dc5df17dd2963
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80146888"
---
# <a name="use-apns-voip-through-notification-hubs-not-officially-supported"></a>Utilizzare APNS VOIP tramite Hub di notifica (non ufficialmente supportato)

È possibile usare le notifiche VOIP di APNS tramite hub di notifica di Azure. tuttavia, non è disponibile alcun supporto ufficiale per questo scenario.

## <a name="considerations"></a>Considerazioni

Se si sceglie comunque di inviare notifiche VOIP APNS tramite Hub di notifica, tenere presenti le limitazioni seguenti:

- L'invio di una `apns-topic` notifica VOIP richiede che l'intestazione sia impostata sull'ID del bundle dell'applicazione, ovvero il `.voip` suffisso. Ad esempio, per un'app `com.microsoft.nhubsample`di `apns-topic` esempio con ID bundle, l'intestazione deve essere impostata su`com.microsoft.nhubsample.voip.`

   Questo metodo non funziona bene con gli hub di notifica di Azure, perché l'ID bundle dell'app deve essere configurato come parte delle credenziali APNS dell'hub e il valore non può essere modificato. Inoltre, Notification Hubs non consente `apns-topic` l'override del valore dell'intestazione in fase di esecuzione.

   Per inviare notifiche VOIP, è necessario `.voip` configurare un hub di notifica separato con l'ID bundle dell'app.

- L'invio di una `apns-push-type` notifica VOIP richiede `voip`che l'intestazione sia impostata sul valore .

   Per aiutare i clienti con la transizione a iOS 13, Notification `apns-push-type` Hubs tenta di dedurre il valore corretto per l'intestazione. La logica di inferenza è intenzionalmente semplice, nel tentativo di evitare di interrompere le notifiche standard. Sfortunatamente, questo metodo causa problemi con le notifiche VOIP, perché Apple tratta le notifiche VOIP come un caso speciale che non segue le stesse regole delle notifiche standard.

   Per inviare notifiche VOIP, è necessario `apns-push-type` specificare un valore esplicito per l'intestazione.

- Notification Hubs limita i payload APNS a 4 KB, come documentato da Apple. Per le notifiche VOIP, Apple consente payload fino a 5 KB. Hub di notifica non distingue tra le notifiche standard e VOIP; pertanto, tutte le notifiche sono limitate a 4 KB.

   Per inviare notifiche VOIP, non è necessario superare il limite di 4 KB per le dimensioni del payload.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere i seguenti collegamenti:

- [Documentazione `apns-topic` `apns-push-type` per e intestazioni e valori, inclusi i casi speciali per le notifiche VOIP](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns).

- [Documentazione per il limite delle dimensioni del carico utile](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification).

- [Aggiornamenti di Notification Hubs per iOS 13](push-notification-updates-ios-13.md#apns-push-type).
