---
title: Livelli Premium e standard del bus di servizio di Azure
description: Questo articolo descrive i livelli standard e Premium del bus di servizio di Azure. Confronta questi livelli e fornisce le differenze tecniche.
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: 31c53a1375078cd5d185945cba55a6e5a6dd5ffb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90966790"
---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>Livelli di messaggistica Standard e Premium del bus di servizio

La messaggistica del bus di servizio, che include entità come code e argomenti, unisce funzionalità di messaggistica aziendale a una semantica di pubblicazione-sottoscrizione completa a livello di cloud. La messaggistica del bus di servizio viene usata come backbone di comunicazione per molte soluzioni cloud avanzate.

Il livello *Premium* della messaggistica del bus di servizio risolve le richieste dei clienti comuni in termini di scalabilità, prestazioni e disponibilità per le applicazioni cruciali. Per gli scenari di produzione è consigliabile usare il livello Premium. Anche se i set di funzionalità sono quasi identici, questi due livelli di messaggistica del bus di servizio sono progettati per gestire diversi casi d'uso.

Nella tabella seguente sono evidenziate alcune differenze generali.

| Premium | Standard |
| --- | --- |
| Velocità effettiva elevata |Velocità effettiva variabile |
| Prestazioni prevedibili |Latenza variabile |
| Prezzi fissi |Prezzi variabili in base all'uso |
| Possibilità di aumentare o ridurre le prestazioni del carico di lavoro |N/D |
| Dimensioni del messaggio fino a 1 MB. Questo limite può essere generato in futuro. Per gli ultimi aggiornamenti importanti al servizio, vedere la pagina [relativa alla messaggistica in Azure](https://techcommunity.microsoft.com/t5/messaging-on-azure/bg-p/MessagingonAzureBlog). |Dimensione messaggi fino a 256 KB |

La **messaggistica di livello Premium del bus di servizio** garantisce l'isolamento delle risorse a livello di CPU e di memoria in modo che ogni carico di lavoro del cliente venga eseguito in isolamento. Questo contenitore di risorse è detto *unità di messaggistica*. Ad ogni spazio dei nomi Premium viene allocata almeno un'unità di messaggistica. È possibile acquistare 1, 2, 4 o 8 unità di messaggistica per ogni spazio dei nomi premium del bus di servizio. Un singolo carico di lavoro o entità può estendersi su più unità di messaggistica e il numero di unità di messaggistica può essere modificato in corrispondenza di. Ne risultano prestazioni prevedibili e ripetibili per la soluzione basata sul bus di servizio.

Non solo le prestazioni sono più prevedibili e disponibili, ma anche più veloci. La messaggistica Premium del bus di servizio si basa sul motore di archiviazione introdotto in [Hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/). Con la messaggistica Premium, le prestazioni massime sono più veloci rispetto al livello Standard.

## <a name="premium-messaging-technical-differences"></a>Differenze tecniche della messaggistica Premium

Le sezioni seguenti illustrano alcune differenze tra i livelli di messaggistica Standard e Premium.

### <a name="partitioned-queues-and-topics"></a>Code e argomenti partizionati

Le code e gli argomenti partizionati non sono supportati nella messaggistica Premium. Per altre informazioni sul partizionamento, vedere [Code e argomenti partizionati](service-bus-partitioning.md).

### <a name="express-entities"></a>Entità Express

Dal momento che la messaggistica Premium viene eseguita in un ambiente di runtime isolato, le entità Express non sono supportate negli spazi dei nomi Premium. Per altre informazioni sulla funzionalità Express, vedere la proprietà [QueueDescription.EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress).

Se è presente codice in esecuzione nella messaggistica Standard e si vuole trasferirlo al livello Premium, assicurarsi che la proprietà [EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress) sia impostata su **false** (valore predefinito).

## <a name="premium-messaging-resource-usage"></a>Utilizzo delle risorse di messaggistica Premium
In generale, qualsiasi operazione su un'entità può causare l'utilizzo della CPU e della memoria. Di seguito sono riportate alcune di queste operazioni: 

- Operazioni di gestione quali le operazioni CRUD (create, retrieve, Update ed Delete) su code, argomenti e sottoscrizioni.
- Operazioni di runtime (invio e ricezione di messaggi)
- Monitoraggio di operazioni e avvisi

Tuttavia, l'utilizzo aggiuntivo della CPU e della memoria non viene valutato. Per il livello di messaggistica Premium è disponibile un singolo prezzo per l'unità di messaggio.

L'utilizzo della CPU e della memoria viene monitorato e visualizzato per i motivi seguenti: 

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

Per informazioni su come configurare uno spazio dei nomi del bus di servizio per la scalabilità automatica (aumento o riduzione delle unità di messaggistica), vedere [aggiornare automaticamente le unità di messaggistica](automate-update-messaging-units.md).

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

Iniziare a usare la messaggistica Premium è estremamente semplice e il processo è simile a quello della messaggistica Standard. Iniziare [creando uno spazio dei nomi](service-bus-create-namespace-portal.md) nel [portale di Azure](https://portal.azure.com). Assicurarsi di selezionare **Premium** in piano **tariffario**. Fai clic su **Visualizza i dettagli sui prezzi completi** per vedere altre informazioni su ogni livello.

![create-premium-namespace][create-premium-namespace]

È anche possibile creare [spazi dei nomi Premium usando i modelli di Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-servicebus-pn-ar/).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla messaggistica del bus di servizio, vedi i collegamenti seguenti:

- [Aggiorna automaticamente le unità di messaggistica](automate-update-messaging-units.md).
- [Introduzione alla messaggistica Premium del bus di servizio di Azure (post di Blog)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
- [Introducing Azure Service Bus Premium Messaging](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging) (Introduzione alla messaggistica Premium del bus di servizio di Azure, Channel9)

<!--Image references-->

[create-premium-namespace]: ./media/service-bus-premium-messaging/select-premium-tier.png
