---
title: Creare uno spazio dei nomi del bus di servizio nel Portale di Azure | Microsoft Docs
description: Procedura sulla creazione di uno spazio dei nomi del bus di servizio usando il Portale di Azure.
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: fbb10e62-b133-4851-9d27-40bd844db3ba
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 03/23/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 5de92033eb7eb4fef8d27a215b3284ab80594065
ms.contentlocale: it-it
ms.lasthandoff: 03/24/2017


---
# <a name="create-a-service-bus-namespace-using-the-azure-portal"></a>Creare uno spazio dei nomi del bus di servizio usando il portale di Azure
Uno spazio dei nomi è un contenitore comune per tutti i componenti di messaggistica. Più code e argomenti possono risiedere in un unico spazio dei nomi e gli spazi dei nomi vengono spesso usati come contenitori di applicazioni. Esistono due diversi modi per creare uno spazio dei nomi del bus di servizio:

1. Portale di Azure (in questo articolo)
2. [Modelli di Gestione risorse][create-namespace-using-arm]

## <a name="create-a-namespace-in-the-azure-portal"></a>Creare uno spazio dei nomi nel portale di Azure
[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

Congratulazioni. È stato creato uno spazio dei nomi per la messaggistica del bus di servizio.

## <a name="next-steps"></a>Passaggi successivi
Vedere gli [esempi nel repository GitHub][github-samples] che illustrano alcune delle funzionalità più avanzate della messaggistica del bus di servizio di Azure.

[create-namespace-using-arm]: service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples

