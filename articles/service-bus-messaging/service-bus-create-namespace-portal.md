---
title: Creare uno spazio dei nomi del bus di servizio nel Portale di Azure | Microsoft Docs
description: Procedura sulla creazione di uno spazio dei nomi del bus di servizio usando il Portale di Azure.
services: service-bus-messaging
documentationcenter: .net
author: jtaubensee
manager: timlt
editor: 
ms.assetid: fbb10e62-b133-4851-9d27-40bd844db3ba
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 11/30/2016
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: 4a972b9b8b52a90f27afda98d8bdc661016d1fe1
ms.openlocfilehash: ba7093f8a2c06ab4cecf11207174a4871435ae64
ms.lasthandoff: 02/28/2017


---
# <a name="create-a-service-bus-namespace-using-the-azure-portal"></a>Creare uno spazio dei nomi del bus di servizio usando il portale di Azure
Uno spazio dei nomi è un contenitore comune per tutti i componenti di messaggistica. Più code e argomenti possono risiedere in un unico spazio dei nomi e gli spazi dei nomi vengono spesso usati come contenitori di applicazioni. Esistono attualmente due diversi modi per creare uno spazio dei nomi del bus di servizio.

1. Portale di Azure (in questo articolo)
2. [Modelli di Gestione risorse][create-namespace-using-arm]

## <a name="create-a-namespace-in-the-azure-portal"></a>Creare uno spazio dei nomi nel portale di Azure
[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

Congratulazioni. È stato creato uno spazio dei nomi per la messaggistica del bus di servizio.

## <a name="next-steps"></a>Passaggi successivi
Vedere gli [esempi nel repository GitHub][github-samples] che illustrano alcune delle funzionalità più avanzate della messaggistica del bus di servizio di Azure.

[create-namespace-using-arm]: service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples

