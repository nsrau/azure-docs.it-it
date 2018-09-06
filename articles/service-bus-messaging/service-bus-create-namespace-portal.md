---
title: Come creare uno spazio dei nomi del bus di servizio nel portale di Azure | Microsoft Docs
description: Creare uno spazio dei nomi del bus di servizio usando il portale di Azure.
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: fbb10e62-b133-4851-9d27-40bd844db3ba
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 06/29/2018
ms.author: spelluru
ms.openlocfilehash: 699129e9f75cce76d1682e3e2e2fb83d248ea92e
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/05/2018
ms.locfileid: "43696026"
---
# <a name="create-a-service-bus-namespace-using-the-azure-portal"></a>Creare uno spazio dei nomi del bus di servizio usando il portale di Azure

Uno spazio dei nomi è un contenitore di ambito per tutti i componenti di messaggistica. Più code e argomenti possono risiedere in un unico spazio dei nomi e gli spazi dei nomi vengono spesso usati come contenitori di applicazioni. Esistono due modi per creare uno spazio dei nomi del bus di servizio:

1. Portale di Azure (in questo articolo)
2. [Modelli di Gestione risorse][create-namespace-using-arm]

## <a name="create-a-namespace-in-the-azure-portal"></a>Creare uno spazio dei nomi nel portale di Azure

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

Congratulazioni! È stato creato uno spazio dei nomi per la messaggistica del bus di servizio.

## <a name="next-steps"></a>Passaggi successivi

Vedere gli [esempi GitHub][github-samples] del bus di servizio, che illustrano alcune delle funzionalità più avanzate della messaggistica del bus di servizio.

[create-namespace-using-arm]: service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure/azure-service-bus/tree/master/samples
