---
title: Espressioni di routing e tag in hub di notifica di Azure
description: Informazioni su come indirizzare e contrassegnare espressioni per gli hub di notifica di Azure.
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
ms.openlocfilehash: b1162e6070deba7f645298b59ffeb1898eb030a8
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76545773"
---
# <a name="routing-and-tag-expressions"></a>Espressioni di routing e tag

## <a name="overview"></a>Overview

Le espressioni tag consentono di avere come destinazione set specifici di dispositivi, o più specificamente registrazioni, quando si invia una notifica push tramite hub di notifica.

## <a name="targeting-specific-registrations"></a>Destinazione su registrazioni specifiche

L'unico modo per avere come destinazione registrazioni di notifiche specifiche consiste nell'associare tag ad esse, quindi utilizzare come destinazione tali tag. Come descritto in [gestione delle registrazioni](notification-hubs-push-notification-registration-management.md), per ricevere notifiche push, un'app deve registrare un handle di dispositivo in un hub di notifica. Quando l'app crea una registrazione in un hub di notifica, il back-end dell'applicazione può inviare notifiche push. Il back-end dell'applicazione può scegliere le registrazioni da utilizzare come destinazione con una notifica specifica nei modi seguenti:

1. **Trasmissione**: tutte le registrazioni nell'hub di notifica ricevono la notifica.
2. **Tag**: tutte le registrazioni contenenti il tag specificato ricevono la notifica.
3. **Espressione tag**: tutte le registrazioni il cui set di tag corrisponde all'espressione specificata ricevono la notifica.

## <a name="tags"></a>Tag

Un tag può essere qualsiasi stringa, fino a 120 caratteri, che contiene caratteri alfanumerici e i caratteri non alfanumerici seguenti:'`_`','`@`','`#`','`.`','`:`','`-`'. Nell'esempio seguente viene illustrata un'applicazione da cui è possibile ricevere notifiche di tipo avviso popup su gruppi musicali specifici. In questo scenario, un modo semplice per instradare le notifiche consiste nell'etichettare le registrazioni con tag che rappresentano le diverse bande, come illustrato nella figura seguente:

![Cenni preliminari sui tag](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags.png)

Nella figura il messaggio contrassegnato con **Beatles** raggiunge solo il tablet registrato con il tag **Beatles**.

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

Non è necessario eseguire il pre-provisioning di tag e può fare riferimento a più concetti specifici dell'app. Ad esempio, gli utenti di questa applicazione di esempio possono commentare i gruppi e desiderano ricevere avvisi popup, non solo per i commenti sui propri gruppi preferiti, ma anche per tutti i commenti degli amici, indipendentemente dal gruppo che stanno commentando. Nella figura seguente viene evidenziato un esempio di questo scenario:

![Tag-amici](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags2.png)

In questo esempio, Alice è interessata agli aggiornamenti per Beatles e Bob è interessato agli aggiornamenti dei lamenti. Bob è anche interessato ai commenti di Charlie e Charlie è interessato ai gemiti. Quando viene inviata una notifica per il commento di Charlie su Beatles, hub di notifica lo invia a Alice e Bob.

Sebbene sia possibile codificare più problemi nei tag (ad esempio, `band_Beatles` o `follows_Charlie`), i tag sono semplici stringhe e non proprietà con valori. Una registrazione corrisponde solo alla presenza o all'assenza di un tag specifico.

Per un'esercitazione completa dettagliata su come usare i tag per l'invio a gruppi di interesse, vedere [Ultime notizie](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md).

> [!NOTE]
> Hub di notifica di Azure supporta un massimo di 60 tag per registrazione.

## <a name="using-tags-to-target-users"></a>Uso dei tag per considerare come obiettivo gli utenti

Un altro modo per usare i tag consiste nell'identificare tutti i dispositivi associati a un utente specifico. È possibile contrassegnare una registrazione con un tag che contiene l'ID utente, come illustrato nella figura seguente:

![Utenti tag](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags3.png)

Nella figura il messaggio con tag `user_Alice` raggiunge tutti i dispositivi contrassegnati con `user_Alice`.

## <a name="tag-expressions"></a>Espressioni tag

Esistono casi in cui le notifiche devono essere destinate a un set di registrazioni identificato non da un singolo tag, ma da un'espressione booleana che usa i tag.

Si consideri un'applicazione sportiva che invia un promemoria su una partita tra Red Sox e Cardinals a tutte le persone di Boston. Se l'app client registra i tag relativi all'interesse per squadre e località, la notifica deve essere destinata a tutte le persone di Boston interessate ai Red Sox o ai Cardinals. Questa condizione può essere espressa con l'espressione booleana seguente:

```csharp
(follows_RedSox || follows_Cardinals) && location_Boston
```

![Espressioni tag](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags4.png)

Le espressioni tag supportano gli operatori booleani comuni, ad esempio `AND` (`&&`), `OR` (`||`) e `NOT` (`!`); possono inoltre contenere parentesi. Le espressioni Tag che usano solo operatori `OR` possono fare riferimento a 20 Tag; in caso contrario, le espressioni tag sono limitate a 6 tag.

Di seguito è riportato un esempio per l'invio di notifiche con espressioni tag con l'SDK:

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
