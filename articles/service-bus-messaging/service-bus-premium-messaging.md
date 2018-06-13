---
title: Panoramica dei piani tariffari di messaggistica Standard e Premium del bus di servizio di Azure | Microsoft Docs
description: Livelli di messaggistica Standard e Premium del bus di servizio
services: service-bus-messaging
documentationcenter: .net
author: djrosanova
manager: timlt
editor: ''
ms.assetid: e211774d-821c-4d79-8563-57472d746c58
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/30/2018
ms.author: sethm
ms.openlocfilehash: 1b2c9b3a0b7565e7de40d4dc0e623a62313b79db
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/03/2018
ms.locfileid: "32775524"
---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>Livelli di messaggistica Standard e Premium del bus di servizio

La messaggistica del bus di servizio, che include entità come code e argomenti, unisce funzionalità di messaggistica aziendale a una semantica di pubblicazione-sottoscrizione completa a livello di cloud. La messaggistica del bus di servizio viene usata come backbone di comunicazione per molte soluzioni cloud avanzate.

Il livello di messaggistica *Premium* del bus di servizio soddisfa le comuni richieste dei clienti in termini di scalabilità, prestazioni e disponibilità per le applicazioni cruciali. Per gli scenari di produzione è consigliabile usare il livello Premium. Anche se i set di funzionalità sono quasi identici, questi due livelli di messaggistica del bus di servizio sono progettati per gestire diversi casi d'uso.

Nella tabella seguente sono evidenziate alcune differenze generali.

| Premium | Standard |
| --- | --- |
| Velocità effettiva elevata |Velocità effettiva variabile |
| Prestazioni prevedibili |Latenza variabile |
| Prezzi fissi |Prezzi variabili in base all'uso |
| Possibilità di aumentare o ridurre le prestazioni del carico di lavoro |N/D |
| Dimensione messaggi fino a 1 MB |Dimensione messaggi fino a 256 KB |

La **messaggistica di livello Premium del bus di servizio** garantisce l'isolamento delle risorse a livello di CPU e di memoria in modo che ogni carico di lavoro del cliente venga eseguito in isolamento. Questo contenitore di risorse viene chiamato *unità di messaggistica*. Ad ogni spazio dei nomi Premium viene allocata almeno un'unità di messaggistica. È possibile acquistare 1, 2 o 4 unità di messaggistica per ogni spazio dei nomi Premium del bus di servizio. Un singolo carico di lavoro o un'entità può estendersi su più unità di messaggistica e il numero di unità di messaggistica può essere modificato in base alle esigenze, anche se la fatturazione prevede un addebito a frequenza giornaliera o a 24 ore. Ne risultano prestazioni prevedibili e ripetibili per la soluzione basata sul bus di servizio.

Non solo le prestazioni sono più prevedibili e disponibili, ma anche più veloci. La messaggistica Premium del bus di servizio è basata sul motore di archiviazione introdotto in [Hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/). Con la messaggistica Premium, le prestazioni massime sono più veloci rispetto al livello Standard.

## <a name="premium-messaging-technical-differences"></a>Differenze tecniche della messaggistica Premium

Le sezioni seguenti illustrano alcune differenze tra i livelli di messaggistica Standard e Premium.

### <a name="partitioned-queues-and-topics"></a>Code e argomenti partizionati

Code e argomenti partizionati non sono supportati nella messaggistica Premium. Per altre informazioni sul partizionamento, vedere [Code e argomenti partizionati](service-bus-partitioning.md).

### <a name="express-entities"></a>Entità Express

Dato che la messaggistica Premium viene eseguita in un ambiente di runtime completamente isolato, le entità Express non sono supportate negli spazi dei nomi Premium. Per altre informazioni sulla funzionalità Express, vedere la proprietà [QueueDescription.EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress).

Se è presente codice in esecuzione nella messaggistica Standard e si vuole trasferirlo al livello Premium, assicurarsi che la proprietà [EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress) sia impostata su **false** (valore predefinito).

## <a name="get-started-with-premium-messaging"></a>Introduzione alla messaggistica Premium

Iniziare a usare la messaggistica Premium è estremamente semplice e il processo è simile a quello della messaggistica Standard. Iniziare [creando uno spazio dei nomi](service-bus-create-namespace-portal.md) nel [portale di Azure](https://portal.azure.com). Assicurarsi di selezionare **Premium** in **Scegliere il piano tariffario**.

![create-premium-namespace][create-premium-namespace]

È anche possibile creare [spazi dei nomi Premium usando i modelli di Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-servicebus-pn-ar/).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla messaggistica del bus di servizio, vedere gli argomenti seguenti.

* [Introducing Azure Service Bus Premium Messaging](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/) (Introduzione alla messaggistica Premium del bus di servizio di Azure, post di blog)
* [Introducing Azure Service Bus Premium Messaging](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging) (Introduzione alla messaggistica Premium del bus di servizio di Azure, Channel9)
* [Panoramica della messaggistica del bus di servizio](service-bus-messaging-overview.md)
* [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)

<!--Image references-->

[create-premium-namespace]: ./media/service-bus-premium-messaging/select-premium-tier.png
