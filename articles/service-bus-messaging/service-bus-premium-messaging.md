---
title: Panoramica dei piani tariffari di messaggistica Standard e Premium del bus di servizio di Azure | Microsoft Docs
description: Livelli di messaggistica Standard e Premium del bus di servizio
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
ms.date: 03/05/2019
ms.author: aschhab
ms.openlocfilehash: 7565ce24199dd8f86f756f01f66aa79e764a1a12
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72752196"
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

La **messaggistica di livello Premium del bus di servizio** garantisce l'isolamento delle risorse a livello di CPU e di memoria in modo che ogni carico di lavoro del cliente venga eseguito in isolamento. Questo contenitore di risorse viene chiamato *unità di messaggistica*. Ad ogni spazio dei nomi Premium viene allocata almeno un'unità di messaggistica. È possibile acquistare 1, 2, 4 o 8 unità di messaggistica per ogni spazio dei nomi premium del bus di servizio. Un singolo carico di lavoro o entità può estendersi su più unità di messaggistica e il numero di unità di messaggistica può essere modificato in corrispondenza di. Ne risultano prestazioni prevedibili e ripetibili per la soluzione basata sul bus di servizio.

Non solo le prestazioni sono più prevedibili e disponibili, ma anche più veloci. La messaggistica Premium del bus di servizio è basata sul motore di archiviazione introdotto in [Hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/). Con la messaggistica Premium, le prestazioni massime sono più veloci rispetto al livello Standard.

## <a name="premium-messaging-technical-differences"></a>Differenze tecniche della messaggistica Premium

Le sezioni seguenti illustrano alcune differenze tra i livelli di messaggistica Standard e Premium.

### <a name="partitioned-queues-and-topics"></a>Code e argomenti partizionati

Code e argomenti partizionati non sono supportati nella messaggistica Premium. Per altre informazioni sul partizionamento, vedere [Code e argomenti partizionati](service-bus-partitioning.md).

### <a name="express-entities"></a>Entità Express

Dato che la messaggistica Premium viene eseguita in un ambiente di runtime completamente isolato, le entità Express non sono supportate negli spazi dei nomi Premium. Per altre informazioni sulla funzionalità Express, vedere la proprietà [QueueDescription.EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress).

Se è presente codice in esecuzione nella messaggistica Standard e si vuole trasferirlo al livello Premium, assicurarsi che la proprietà [EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress) sia impostata su **false** (valore predefinito).

## <a name="premium-messaging-resource-usage"></a>Utilizzo delle risorse di messaggistica Premium
In generale, qualsiasi operazione su un'entità può causare l'utilizzo della CPU e della memoria. Di seguito sono riportate alcune di queste operazioni: 

- Operazioni di gestione quali le operazioni CRUD (create, retrieve, Update ed Delete) su code, argomenti e sottoscrizioni.
- Operazioni di runtime (invio e ricezione di messaggi)
- Monitoraggio di operazioni e avvisi

Tuttavia, l'utilizzo aggiuntivo della CPU e della memoria non viene valutato. Per il livello di messaggistica Premium è disponibile un singolo prezzo per l'unità di messaggio.

L'utilizzo della CPU e della memoria viene monitorato e visualizzato all'utente per i motivi seguenti: 

- Fornire trasparenza negli elementi interni del sistema
- Comprendere la capacità delle risorse acquistate.
- Pianificazione della capacità che consente di scegliere la scalabilità verticale o orizzontale.

## <a name="messaging-unit---how-many-are-needed"></a>Unità di messaggistica: quante sono necessarie?

Durante il provisioning di uno spazio dei nomi premium del bus di servizio di Azure, è necessario specificare il numero di unità di messaggistica allocate. Queste unità di messaggistica sono risorse dedicate allocate allo spazio dei nomi.

Il numero di unità di messaggistica allocate allo spazio dei nomi premium del bus di servizio può essere **modificato dinamicamente** per calcolare la variazione (aumento o riduzione) dei carichi di lavoro.

Quando si decide il numero di unità di messaggistica per l'architettura, è necessario prendere in considerazione alcuni fattori:

- Iniziare con ***1 o 2 unità di messaggistica*** allocate allo spazio dei nomi.
- Esaminare le metriche di utilizzo della CPU nelle [metriche di utilizzo delle risorse](service-bus-metrics-azure-monitor.md#resource-usage-metrics) per lo spazio dei nomi.
    - Se l'utilizzo della CPU è ***inferiore al 20%***, potrebbe essere possibile ***ridurre*** il numero di unità di messaggistica allocate allo spazio dei nomi.
    - Se l'utilizzo della CPU è ***superiore al 70%***, l'applicazione trarrà vantaggio dal ***ridimensionamento*** del numero di unità di messaggistica allocate allo spazio dei nomi.

Il processo di ridimensionamento delle risorse allocate a uno spazio dei nomi del bus di servizio può essere automatizzato tramite [manuali operativi di automazione di Azure](../automation/automation-quickstart-create-runbook.md).

> [!NOTE]
> Il **ridimensionamento** delle risorse allocate allo spazio dei nomi può essere di tipo preemptive o Reactive.
>
>  * **Preemptive**: se è previsto un carico di lavoro aggiuntivo (a causa della stagionalità o delle tendenze), è possibile continuare ad allocare più unità di messaggistica allo spazio dei nomi prima che i carichi di lavoro vengano raggiunti.
>
>  * **Reattivo**: se vengono identificati carichi di lavoro aggiuntivi studiando le metriche di utilizzo delle risorse, è possibile allocare risorse aggiuntive allo spazio dei nomi per incorporare la domanda crescente.
>
> I contatori di fatturazione per il bus di servizio sono orari. Nel caso di scalabilità verticale, si pagano solo le risorse aggiuntive per le ore in cui sono state usate.
>

## <a name="get-started-with-premium-messaging"></a>Introduzione alla messaggistica Premium

Iniziare a usare la messaggistica Premium è estremamente semplice e il processo è simile a quello della messaggistica Standard. Iniziare [creando uno spazio dei nomi](service-bus-create-namespace-portal.md) nel [portale di Azure](https://portal.azure.com). Assicurarsi di selezionare **Premium** in **Piano tariffario**. Fai clic su **Visualizza i dettagli sui prezzi completi** per vedere altre informazioni su ogni livello.

![create-premium-namespace][create-premium-namespace]

È anche possibile creare [spazi dei nomi Premium usando i modelli di Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-servicebus-pn-ar/).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla messaggistica del bus di servizio, vedi i collegamenti seguenti:

* [Introducing Azure Service Bus Premium Messaging](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/) (Introduzione alla messaggistica Premium del bus di servizio di Azure, post di blog)
* [Introducing Azure Service Bus Premium Messaging](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging) (Introduzione alla messaggistica Premium del bus di servizio di Azure, Channel9)
* [Panoramica della messaggistica del bus di servizio](service-bus-messaging-overview.md)
* [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)

<!--Image references-->

[create-premium-namespace]: ./media/service-bus-premium-messaging/select-premium-tier.png
