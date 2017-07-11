---
title: Come creare uno spazio dei nomi del bus di servizio nel portale di Azure | Microsoft Docs
description: Creare uno spazio dei nomi del bus di servizio usando il portale di Azure.
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
ms.date: 06/27/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: ffb134085c8555b22a317213622ca6c9490497d8
ms.contentlocale: it-it
ms.lasthandoff: 06/28/2017


---
<a id="create-a-service-bus-namespace-using-the-azure-portal" class="xliff"></a>

# Creare uno spazio dei nomi del bus di servizio usando il portale di Azure

Uno spazio dei nomi è un contenitore di ambito per tutti i componenti di messaggistica. Più code e argomenti possono risiedere in un unico spazio dei nomi e gli spazi dei nomi vengono spesso usati come contenitori di applicazioni. Esistono due diversi modi per creare uno spazio dei nomi del bus di servizio:

1. Portale di Azure (in questo articolo)
2. [Modelli di Gestione risorse][create-namespace-using-arm]

<a id="create-a-namespace-in-the-azure-portal" class="xliff"></a>

## Creare uno spazio dei nomi nel portale di Azure

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

Congratulazioni. È stato creato uno spazio dei nomi per la messaggistica del bus di servizio.

<a id="next-steps" class="xliff"></a>

## Passaggi successivi

Vedere gli [esempi nel repository GitHub][github-samples], che illustrano alcune delle funzionalità più avanzate della messaggistica del bus di servizio di Azure.

[create-namespace-using-arm]: service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples

