---
title: Routing and tag expressions in Azure Notification Hubs
description: Informazioni su come instradare ed applicare tag alle espressioni per gli hub di notifica di Azure.Learn how to route and tag expressions for Azure Notification Hubs.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 0fffb3bb-8ed8-4e0f-89e8-0de24a47f644
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 12/09/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 12/04/2019
ms.openlocfilehash: 2432ac41645e373ea3a87ff7e69ef02a4e30c81d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062306"
---
# <a name="routing-and-tag-expressions"></a>Espressioni di routing e tag

## <a name="overview"></a>Panoramica

Le espressioni tag consentono di avere come destinazione set specifici di dispositivi, o più specificamente registrazioni, quando si invia una notifica push tramite hub di notifica.

## <a name="targeting-specific-registrations"></a>Destinazione su registrazioni specifiche

L'unico modo per avere come destinazione registrazioni di notifiche specifiche consiste nell'associare tag ad esse, quindi utilizzare come destinazione tali tag. Come descritto in [Gestione registrazioni](notification-hubs-push-notification-registration-management.md), per ricevere le notifiche push, un'app deve registrare un handle del dispositivo in un hub di notifica. Dopo che l'app crea una registrazione in un hub di notifica, il back-end dell'applicazione può inviare notifiche push. Il back-end dell'applicazione può scegliere le registrazioni da utilizzare come destinazione con una notifica specifica nei modi seguenti:

1. **Trasmissione**: tutte le registrazioni nell'hub di notifica ricevono la notifica.
2. **Tag**: tutte le registrazioni contenenti il tag specificato ricevono la notifica.
3. **Espressione tag**: tutte le registrazioni il cui set di tag corrisponde all'espressione specificata ricevono la notifica.

## <a name="tags"></a>Tag

Un tag può essere qualsiasi stringa, fino a 120 caratteri, contenente caratteri`_`alfanumerici`#`e i`.`seguenti`:`caratteri non`-`alfanumerici: ' '`@`' ' ' ' ' ' ' ' '. Nell'esempio seguente viene illustrata un'applicazione da cui è possibile ricevere notifiche di tipo avviso popup su gruppi musicali specifici. In questo scenario, un modo semplice per instradare le notifiche consiste nell'etichettare le registrazioni con tag che rappresentano le diverse bande, come nella figura seguente:In this scenario, a simple way to route notifications is to label registrations with tags that represent the different bands, as in the following figure:

![Panoramica dei tag](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags.png)

Nella figura, il messaggio etichettato con **Beatles** raggiunge solo il tablet registrato con il tag **Beatles**.

Per ulteriori informazioni sulla creazione di registrazioni per i tag, vedere [Gestione delle registrazioni](notification-hubs-push-notification-registration-management.md).

È possibile inviare notifiche ai tag usando i metodi di notifiche di invio della classe `Microsoft.Azure.NotificationHubs.NotificationHubClient` nell’SDK [hub di notifica di Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) . È inoltre possibile utilizzare Node.js o le API REST delle notifiche push.  Di seguito è riportato un esempio con l’SDK.

```csharp
Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

// Windows 8.1 / Windows Phone 8.1
var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
"You requested a Beatles notification</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Beatles");

// Windows 10
toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
"You requested a Wailers notification</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Wailers");
```

I tag non devono essere pre-provisioning e possono fare riferimento a più concetti specifici dell'app. Ad esempio, gli utenti di questa applicazione di esempio possono commentare i gruppi e desiderano ricevere avvisi popup, non solo per i commenti sui propri gruppi preferiti, ma anche per tutti i commenti degli amici, indipendentemente dal gruppo che stanno commentando. Nella figura seguente viene evidenziato un esempio di questo scenario:The following figure highlights an example of this scenario:

![Tag amici](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags2.png)

In questo esempio, Alice è interessata agli aggiornamenti per i Beatles e Bob è interessato agli aggiornamenti per i Wailers. Bob è anche interessato ai commenti di Charlie, e Charlie è interessato ai Wailers. Quando viene inviata una notifica per il commento di Charlie sui Beatles, Notification Hubs lo invia sia ad Alice che a Bob.

Sebbene sia possibile codificare più problemi `band_Beatles` `follows_Charlie`nei tag (ad esempio, o ), i tag sono stringhe semplici e non proprietà con valori. Una registrazione corrisponde solo alla presenza o all'assenza di un tag specifico.

Per un'esercitazione completa dettagliata su come usare i tag per l'invio a gruppi di interesse, vedere [Ultime notizie](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md).

> [!NOTE]
> Hub di notifica di Azure supporta un massimo di 60 tag per registrazione.

## <a name="using-tags-to-target-users"></a>Uso dei tag per considerare come obiettivo gli utenti

Un altro modo per utilizzare i tag consiste nell'identificare tutti i dispositivi associati a un determinato utente. È possibile contrassegnare una registrazione con un tag che contiene l'ID utente, come illustrato nella figura seguente:You can tag a Registration with a tag that contains the user ID, as in the following figure:

![Contrassegnare gli utenti](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags3.png)

Nella figura, il messaggio `user_Alice` contrassegnato raggiunge tutti `user_Alice`i dispositivi contrassegnati con .

## <a name="tag-expressions"></a>Espressioni tag

In alcuni casi le notifiche devono essere destinate a un set di registrazioni identificate non da un singolo tag, ma da un'espressione booleana che usa tag.

Si consideri un'applicazione sportiva che invia un promemoria su una partita tra Red Sox e Cardinals a tutte le persone di Boston. Se l'app client registra i tag relativi all'interesse per squadre e località, la notifica deve essere destinata a tutte le persone di Boston interessate ai Red Sox o ai Cardinals. Questa condizione può essere espressa con l'espressione booleana seguente:

```csharp
(follows_RedSox || follows_Cardinals) && location_Boston
```

![Espressioni tag](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags4.png)

Le espressioni di tag `AND` supportano`||`operatori `NOT` booleani comuni quali ( ),`!``&&` `OR` ( ) e ( ); possono anche contenere parentesi. Le espressioni `OR` di tag che utilizzano solo operatori possono fare riferimento a 20 tag; espressione `AND` con operatori `OR` ma nessun operatore può fare riferimento a 10 tag; in caso contrario, le espressioni di tag sono limitate a 6 tag.

Ecco un esempio per l'invio di notifiche con espressioni di tag utilizzando l'SDK:

```csharp
Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

String userTag = "(location_Boston && !follows_Cardinals)";

// Windows 8.1 / Windows Phone 8.1
var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
"You want info on the Red Sox</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

// Windows 10
toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
"You want info on the Red Sox</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
```
