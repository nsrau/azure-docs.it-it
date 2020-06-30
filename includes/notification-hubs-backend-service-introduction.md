---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 98c9d83424e7d347bb6e1166a3b00304d39e0cce
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85095455"
---
Un back-end dell'[API Web ASP.NET Core](https://dotnet.microsoft.com/apps/aspnet/apis)viene usato per gestire la [registrazione del dispositivo](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#what-is-device-registration) per il client usando il metodo di [installazione](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#installations) più recente e migliore. Il servizio invierà inoltre notifiche push in modalità multipiattaforma. 

Queste operazioni vengono gestite tramite l'[SDK di Hub di notifica per le operazioni back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). Per altre informazioni sull'approccio generale, vedere [Registrazione dal back-end dell'app](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#registration-management-from-a-backend).
