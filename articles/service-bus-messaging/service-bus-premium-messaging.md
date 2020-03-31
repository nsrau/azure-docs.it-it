---
title: Livelli Premium e standard del bus di servizio di AzureAzure Service Bus premium and standard tiers
description: Questo articolo descrive i livelli standard e premium del bus di servizio di Azure.This article describes standard and premium tiers of Azure Service Bus. Confronta questi livelli e fornisce differenze tecniche.
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: e211774d-821c-4d79-8563-57472d746c58
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: ef3cc8d4c7354b43389244e72c2dbc5899b8db25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774559"
---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>Livelli di messaggistica Standard e Premium del bus di servizio

La messaggistica del bus di servizio, che include entità come code e argomenti, unisce funzionalità di messaggistica aziendale a una semantica di pubblicazione-sottoscrizione completa a livello di cloud. La messaggistica del bus di servizio viene usata come backbone di comunicazione per molte soluzioni cloud avanzate.

Il livello *Premium* di Messaggistica del bus di servizio risponde alle richieste comuni dei clienti in termini di scalabilità, prestazioni e disponibilità per le applicazioni mission-critical. Per gli scenari di produzione è consigliabile usare il livello Premium. Anche se i set di funzionalità sono quasi identici, questi due livelli di messaggistica del bus di servizio sono progettati per gestire diversi casi d'uso.

Nella tabella seguente sono evidenziate alcune differenze generali.

| Premium | Standard |
| --- | --- |
| Velocità effettiva elevata |Velocità effettiva variabile |
| Prestazioni prevedibili |Latenza variabile |
| Prezzi fissi |Prezzi variabili in base all'uso |
| Possibilità di aumentare o ridurre le prestazioni del carico di lavoro |N/D |
| Dimensione messaggi fino a 1 MB |Dimensione messaggi fino a 256 KB |

La **messaggistica di livello Premium del bus di servizio** garantisce l'isolamento delle risorse a livello di CPU e di memoria in modo che ogni carico di lavoro del cliente venga eseguito in isolamento. Questo contenitore di risorse è denominato unità di *messaggistica*. Ad ogni spazio dei nomi Premium viene allocata almeno un'unità di messaggistica. È possibile acquistare 1, 2, 4 o 8 unità di messaggistica per ogni spazio dei nomi Service Bus Premium. Un singolo carico di lavoro o entità può estendersi su più unità di messaggistica e il numero di unità di messaggistica può essere modificato a tempo. Ne risultano prestazioni prevedibili e ripetibili per la soluzione basata sul bus di servizio.

Non solo le prestazioni sono più prevedibili e disponibili, ma anche più veloci. La messaggistica Premium del bus di servizio si basa sul motore di archiviazione introdotto in Hub eventi di [Azure.](https://azure.microsoft.com/services/event-hubs/) Con la messaggistica Premium, le prestazioni massime sono più veloci rispetto al livello Standard.

## <a name="premium-messaging-technical-differences"></a>Differenze tecniche della messaggistica Premium

Le sezioni seguenti illustrano alcune differenze tra i livelli di messaggistica Standard e Premium.

### <a name="partitioned-queues-and-topics"></a>Code e argomenti partizionati

Code e argomenti partizionati non sono supportati nella messaggistica Premium. Per altre informazioni sul partizionamento, vedere [Code e argomenti partizionati](service-bus-partitioning.md).

### <a name="express-entities"></a>Entità Express

Dato che la messaggistica Premium viene eseguita in un ambiente di runtime completamente isolato, le entità Express non sono supportate negli spazi dei nomi Premium. Per altre informazioni sulla funzionalità Express, vedere la proprietà [QueueDescription.EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress).

Se è presente codice in esecuzione nella messaggistica Standard e si vuole trasferirlo al livello Premium, assicurarsi che la proprietà [EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress) sia impostata su **false** (valore predefinito).

## <a name="premium-messaging-resource-usage"></a>Utilizzo delle risorse di messaggistica PremiumPremium Messaging resource usage
In generale, qualsiasi operazione su un'entità può causare l'utilizzo della CPU e della memoria. Ecco alcune di queste operazioni: 

- Operazioni di gestione quali le operazioni CRUD (Create, Retrieve, Update, and Delete) su code, argomenti e sottoscrizioni.
- Operazioni di runtime (invio e ricezione di messaggi)
- Monitoraggio di operazioni e avvisi

L'utilizzo aggiuntivo della CPU e della memoria non ha un prezzo aggiuntivo però. Per il livello di messaggistica Premium, è disponibile un unico prezzo per l'unità messaggio.

L'utilizzo della CPU e della memoria viene registrato e visualizzato all'utente per i seguenti motivi: 

- Fornire trasparenza negli interni del sistema
- Comprendere la capacità delle risorse acquistate.
- Pianificazione della capacità che consente di decidere di aumentare/ridurre la scalabilità.

## <a name="messaging-unit---how-many-are-needed"></a>Unità di messaggistica - Quanti sono necessari?

Quando si esegue il provisioning di uno spazio dei nomi Premium del bus di servizio di Azure, è necessario specificare il numero di unità di messaggistica allocate. Queste unità di messaggistica sono risorse dedicate allocate allo spazio dei nomi.

Il numero di unità di messaggistica allocate allo spazio dei nomi Service Bus Premium può essere **regolato dinamicamente** per tenere conto della modifica (aumento o diminuzione) dei carichi di lavoro.

Esistono diversi fattori da prendere in considerazione quando si decide il numero di unità di messaggistica per l'architettura:

- Iniziare con 1 o 2 unità di ***messaggistica*** allocate allo spazio dei nomi.
- Studiare le metriche di utilizzo della CPU all'interno delle [metriche di utilizzo delle risorse](service-bus-metrics-azure-monitor.md#resource-usage-metrics) per lo spazio dei nomi.
    - Se l'utilizzo della CPU è inferiore al ***20%,*** potrebbe essere possibile ***ridurre*** il numero di unità di messaggistica allocate allo spazio dei nomi.
    - Se l'utilizzo della CPU è superiore al ***70%,*** l'applicazione trarrà vantaggio dalla ***scalabilità*** verticale del numero di unità di messaggistica allocate allo spazio dei nomi.

Il processo di ridimensionamento delle risorse allocate a uno spazio dei nomi del bus di servizio può essere automatizzato usando i runbook di automazione di [Azure.](../automation/automation-quickstart-create-runbook.md)

> [!NOTE]
> **Il ridimensionamento** delle risorse allocate allo spazio dei nomi può essere preemptive o reattivo.
>
>  * **Preemptive**: Se è previsto un carico di lavoro aggiuntivo (a causa della stagionalità o delle tendenze), è possibile procedere all'allocazione di più unità di messaggistica allo spazio dei nomi prima che vengano raggiunti i carichi di lavoro.
>
>  * **Reattivo:** se altri carichi di lavoro vengono identificati studiando le metriche di utilizzo delle risorse, è possibile allocare risorse aggiuntive allo spazio dei nomi per incorporare l'aumento della domanda.
>
> I contatori di fatturazione per il bus di servizio sono orari. In caso di scalabilità verticale, si paga solo per le risorse aggiuntive per le ore in cui sono state utilizzate.
>

## <a name="get-started-with-premium-messaging"></a>Introduzione alla messaggistica Premium

Iniziare a usare la messaggistica Premium è estremamente semplice e il processo è simile a quello della messaggistica Standard. Iniziare [creando uno spazio dei nomi](service-bus-create-namespace-portal.md) nel [portale di Azure](https://portal.azure.com). Assicurarsi di selezionare **Premium** in **Piano tariffario**. Fai clic su **Visualizza i dettagli sui prezzi completi** per vedere altre informazioni su ogni livello.

![create-premium-namespace][create-premium-namespace]

È anche possibile creare [spazi dei nomi Premium usando i modelli di Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-servicebus-pn-ar/).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla messaggistica del bus di servizio, vedi i collegamenti seguenti:

* [Introduzione alla messaggistica Premium del bus di servizio di Azure (post di blog)Introducing Azure Service Bus Premium Messaging (blog post)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Introducing Azure Service Bus Premium Messaging](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging) (Introduzione alla messaggistica Premium del bus di servizio di Azure, Channel9)
* [Panoramica della messaggistica del bus di servizioService Bus Messaging overview](service-bus-messaging-overview.md)
* [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)

<!--Image references-->

[create-premium-namespace]: ./media/service-bus-premium-messaging/select-premium-tier.png
