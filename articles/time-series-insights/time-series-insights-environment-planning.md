---
title: Pianificare l'ambiente Gen1-Azure Time Series Insights | Microsoft Docs
description: Procedure consigliate per preparare, configurare e distribuire l'ambiente di Azure Time Series Insights Gen1.
services: time-series-insights
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 546c53334b7700ab73c22edb2d82b324bfad61a9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91569447"
---
# <a name="plan-your-azure-time-series-insights-gen1-environment"></a>Pianificare l'ambiente Gen1 Azure Time Series Insights

> [!CAUTION]
> È un articolo di Gen1.

Questo articolo descrive come pianificare l'ambiente Gen1 Azure Time Series Insights in base alla velocità di ingresso prevista e ai requisiti di conservazione dei dati.

## <a name="video"></a>Video

**Guarda questo video per scoprire di più sulla conservazione dei dati in Azure Time Series Insights e su come pianificarlo**:<br /><br />

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>Procedure consigliate

Per iniziare a usare Azure Time Series Insights, è consigliabile conoscere la quantità di dati che si prevede di eseguire il push in base al minuto e il tempo necessario per archiviare i dati.  

Per ulteriori informazioni sulla capacità e la conservazione per gli SKU Azure Time Series Insights, vedere [prezzi Azure Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).

Per pianificare meglio l'ambiente di Azure Time Series Insights per il successo a lungo termine, considerare gli attributi seguenti:

- [Capacità di archiviazione](#storage-capacity)
- [Periodo di conservazione dei dati](#data-retention)
- [Capacità in ingresso](#ingress-capacity)
- [Forme degli eventi](#shape-your-events)
- [Assicurarsi di disporre di dati di riferimento sul posto](#ensure-that-you-have-reference-data)

## <a name="storage-capacity"></a>Capacità di archiviazione

Per impostazione predefinita, Azure Time Series Insights mantiene i dati in base alla quantità di spazio di archiviazione di cui viene effettuato il provisioning (unità &#215; quantità di spazio di archiviazione per unità) e in ingresso.

## <a name="data-retention"></a>Conservazione dei dati

È possibile modificare l'impostazione relativa al **tempo di conservazione dei dati** nell'ambiente Azure Time Series Insights. È possibile abilitare fino a 400 giorni di conservazione.

Azure Time Series Insights dispone di due modalità:

- Una modalità ottimizza i dati più aggiornati. Impone un criterio per **eliminare i dati obsoleti** lasciando disponibili i dati recenti con l'istanza. Questa modalità è abilitata per impostazione predefinita.
- L'altro ottimizza i dati in modo che rimangano al di sotto dei limiti di conservazione configurati. **Sospendere** il traffico in ingresso impedisce che i nuovi dati vengano immessi quando viene selezionato perché il **limite di archiviazione ha superato il comportamento**.

È possibile modificare la conservazione e passare tra le due modalità nella pagina di configurazione dell'ambiente nel portale di Azure.

> [!IMPORTANT]
> È possibile configurare un massimo di 400 giorni di conservazione dei dati nell'ambiente di Azure Time Series Insights Gen1.

### <a name="configure-data-retention"></a>Configurare la conservazione dei dati

1. Nel [portale di Azure](https://portal.azure.com) selezionare l'ambiente Time Series Insights desiderato.

1. Nel riquadro **Time Series Insights ambiente** , in **Impostazioni**, selezionare **Configurazione archiviazione**.

1. Nella casella **tempo di conservazione dati (in giorni)** immettere un valore compreso tra 1 e 400.

   [![Configurare la conservazione](media/data-retention/configure-data-retention.png)](media/data-retention/configure-data-retention.png#lightbox)

> [!TIP]
> Per altre informazioni su come implementare un criterio di conservazione dei dati appropriato, vedere [come configurare la conservazione](./time-series-insights-how-to-configure-retention.md).

## <a name="ingress-capacity"></a>Capacità in ingresso

[!INCLUDE [Azure Time Series Insights Gen1 limits](../../includes/time-series-insights-ga-limits.md)]

### <a name="environment-planning"></a>Pianificazione dell'ambiente

La seconda area in cui concentrarsi per la pianificazione dell'ambiente Azure Time Series Insights è la capacità in ingresso. La capacità di archiviazione e di eventi in ingresso giornaliera viene misurata al minuto, in blocchi da 1 KB. La dimensione massima consentita per i pacchetti è 32 KB. I pacchetti di dati di dimensioni superiori a 32 KB vengono troncati.

È possibile aumentare la capacità di uno SKU S1 o S2 a 10 unità in un unico ambiente. Non è possibile eseguire la migrazione da un ambiente S1 a un S2. Non è possibile eseguire la migrazione da un ambiente S2 a un S1.

Per la capacità in ingresso, determinare innanzitutto il traffico in ingresso totale richiesto su base mensile. Determinare quindi quali sono le esigenze al minuto.

La limitazione e la latenza svolgono un ruolo nella capacità al minuto. Se si ha un picco nel traffico di dati in ingresso che dura meno di 24 ore, Azure Time Series Insights possibile "recuperare" in una velocità in ingresso di due volte le tariffe elencate nella tabella precedente.

Se, ad esempio, si dispone di un singolo SKU S1, i dati vengono ingresati a una velocità di 720 eventi al minuto e la velocità dei dati aumenta per meno di un'ora a una frequenza di 1.440 eventi o meno, non esiste una latenza evidente nell'ambiente. Tuttavia, se si superano 1.440 eventi al minuto per più di un'ora, è probabile che si verifichi una latenza nei dati visualizzati e disponibili per le query nell'ambiente in uso.

Potrebbe non essere possibile conoscere in anticipo la quantità di dati che si prevede di inserire. In questo caso, è possibile trovare i dati di telemetria per l' [Hub di Azure](../iot-hub/iot-hub-metrics.md) e gli hub [eventi di Azure](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/) nella sottoscrizione di portale di Azure. I dati di telemetria consentono di determinare come eseguire il provisioning dell'ambiente. Usare il riquadro **metriche** nel portale di Azure per la rispettiva origine evento per visualizzare i dati di telemetria. Se si conoscono le metriche dell'origine evento, è possibile pianificare ed eseguire il provisioning dell'ambiente Azure Time Series Insights in modo più efficace.

### <a name="calculate-ingress-requirements"></a>Calcolare i requisiti del traffico in ingresso

Per calcolare i requisiti di ingresso:

- Verificare che la capacità in ingresso si trovi al di sopra della Tariffa media al minuto e che l'ambiente sia sufficiente per gestire l'ingresso previsto equivalente a due volte la capacità per meno di un'ora.

- Se si verificano picchi di ingresso che durano più di un'ora, usare la velocità di picco come media. Eseguire il provisioning di un ambiente con la capacità di gestire la velocità di picco.

### <a name="mitigate-throttling-and-latency"></a>Ridurre la limitazione e la latenza

Per informazioni su come evitare la limitazione e la latenza, vedere [attenuare la latenza e la limitazione delle richieste](time-series-insights-environment-mitigate-latency.md).

## <a name="shape-your-events"></a>Dare forma agli eventi

È importante assicurarsi che il modo in cui si inviano gli eventi a Azure Time Series Insights supporti le dimensioni dell'ambiente di cui si esegue il provisioning. Viceversa, è possibile eseguire il mapping delle dimensioni dell'ambiente al numero di eventi Azure Time Series Insights letture e alle dimensioni di ogni evento. È anche importante considerare gli attributi che è possibile usare per sezionare e filtrare in base al momento in cui si eseguono query sui dati.

> [!TIP]
> Esaminare la documentazione di data shaping in JSON per [l'invio di eventi](time-series-insights-send-events.md).

## <a name="ensure-that-you-have-reference-data"></a>Assicurarsi di disporre di dati di riferimento

Un *set di dati di riferimento* è una raccolta di elementi che aumentano gli eventi dall'origine evento. Il motore di ingresso Azure Time Series Insights unisce ogni evento dell'origine evento con la riga di dati corrispondente nel set di dati di riferimento. L'evento aumentato è quindi disponibile per la query. Il join è basato sulle colonne **chiave primaria** definite nel set di dati di riferimento.

> [!NOTE]
> I dati di riferimento non vengono uniti retroattivamente. Solo i dati in ingresso correnti e futuri vengono abbinati e aggiunti al set di dati di riferimento dopo che sono stati configurati e caricati. Se si prevede di inviare una grande quantità di dati cronologici a Azure Time Series Insights senza prima caricare o creare dati di riferimento in Azure Time Series Insights, potrebbe essere necessario ripetere il lavoro (Suggerimento: non divertente).  

Per altre informazioni su come creare, caricare e gestire i dati di riferimento in Azure Time Series Insights, vedere la [documentazione del set](time-series-insights-add-reference-data-set.md)di dati di riferimento.

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare, creare [un nuovo ambiente di Azure Time Series Insights nel portale di Azure](time-series-insights-get-started.md).

- Informazioni su come [aggiungere un'origine evento di hub eventi](time-series-insights-how-to-add-an-event-source-eventhub.md) a Azure Time Series Insights.

- Leggere le informazioni su come [configurare un'origine evento dell'hub](time-series-insights-how-to-add-an-event-source-iothub.md)Internet.
