---
title: Creare uno spazio dei nomi del bus di servizio usando il portale di Azure | Microsoft Docs
description: Per iniziare a usare il bus di servizio, è necessario uno spazio dei nomi. Ecco come crearne uno tramite il portale di Azure.
services: service-bus
documentationcenter: .net
author: jtaubensee
manager: timlt
editor: ''

ms.service: service-bus
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 08/22/2016
ms.author: jotaub

---
# Creare uno spazio dei nomi del bus di servizio usando il portale di Azure
Uno spazio dei nomi è un contenitore comune per tutti i componenti di messaggistica. Più code e argomenti possono risiedere in un unico spazio dei nomi e gli spazi dei nomi vengono spesso usati come contenitori di applicazioni. Esistono attualmente due diversi modi per creare uno spazio dei nomi del bus di servizio.

1. Portale di Azure (in questo articolo)
2. [Modelli di Gestione risorse][create-namespace-using-arm]

## Creare uno spazio dei nomi nel portale di Azure
[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

Congratulazioni. È stato creato uno spazio dei nomi per la messaggistica del bus di servizio.

## Passaggi successivi
Vedere il [repository GitHub](https://github.com/Azure-Samples/azure-servicebus-messaging-samples\]\[github-samples) con esempi che illustrano alcune delle funzionalità più avanzate della messaggistica del bus di servizio di Azure.

[create-namespace-using-arm]: ../service-bus-messaging/service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples

<!---HONumber=AcomDC_1005_2016-->