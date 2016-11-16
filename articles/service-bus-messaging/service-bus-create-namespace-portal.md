---
title: Creare uno spazio dei nomi del bus di servizio usando il portale di Azure | Documentazione Microsoft
description: "Per iniziare a usare il bus di servizio, è necessario uno spazio dei nomi. Ecco come crearne uno tramite il portale di Azure."
services: service-bus
documentationcenter: .net
author: jtaubensee
manager: timlt
editor: 
ms.assetid: fbb10e62-b133-4851-9d27-40bd844db3ba
ms.service: service-bus
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 08/22/2016
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a341d32149a84b9959afb6c3c1796c66c4d593cd


---
# <a name="create-a-service-bus-namespace-using-the-azure-portal"></a>Creare uno spazio dei nomi del bus di servizio usando il portale di Azure
Uno spazio dei nomi è un contenitore comune per tutti i componenti di messaggistica. Più code e argomenti possono risiedere in un unico spazio dei nomi e gli spazi dei nomi vengono spesso usati come contenitori di applicazioni. Esistono attualmente due diversi modi per creare uno spazio dei nomi del bus di servizio.

1. Portale di Azure (in questo articolo)
2. [Modelli di Resource Manager][create-namespace-using-arm]

## <a name="create-a-namespace-in-the-azure-portal"></a>Creare uno spazio dei nomi nel portale di Azure
[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

Congratulazioni. È stato creato uno spazio dei nomi per la messaggistica del bus di servizio.

## <a name="next-steps"></a>Passaggi successivi
Vedere gli [esempi nel repository GitHub][github-samples] con esempi che illustrano alcune delle funzionalità più avanzate della messaggistica del bus di servizio di Azure.

[create-namespace-using-arm]: service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples



<!--HONumber=Nov16_HO2-->


