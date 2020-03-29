---
title: Pianificare l'ambiente GA - Approfondimenti sulle serie temporali di Azure Documenti Microsoft
description: Procedure consigliate per la preparazione, la configurazione e la distribuzione dell'ambiente Azure Time Series Insights GA.
services: time-series-insights
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: 972bb2a804057037deedb448674abafcc175b21f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76314811"
---
# <a name="plan-your-azure-time-series-insights-ga-environment"></a>Pianificare l'ambiente GA di Azure Time Series InsightsPlan your Azure Time Series Insights GA environment

Questo articolo descrive come pianificare l'ambiente di disponibilità generale (GA) di Azure Time Series Insights in base alla frequenza di ingresso prevista e ai requisiti di conservazione dei dati.

## <a name="video"></a>Video

**Guardare questo video per altre informazioni sulla conservazione dei dati in Azure Time Series Insights e su come pianificarlo:**<br /><br />

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>Procedure consigliate

Per iniziare a usare Azure Time Series Insights, è consigliabile sapere quanti dati si prevede di eseguire il push in base al minuto e per quanto tempo è necessario archiviare i dati.  

Per altre informazioni sulla capacità e la conservazione per gli SKU di Time Series Insights, vedere Prezzi di [Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).

Per pianificare al meglio l'ambiente Time Series Insights per il successo a lungo termine, considerare gli attributi seguenti:To best plan your Time Series Insights environment for long-term success, consider the following attributes:

- [Capacità di archiviazione](#storage-capacity)
- [Periodo di conservazione dei dati](#data-retention)
- [Capacità in ingresso](#ingress-capacity)
- [Forme degli eventi](#shape-your-events)
- [Garantire la sicurezza dei dati di riferimento](#ensure-that-you-have-reference-data)

## <a name="storage-capacity"></a>Capacità di archiviazione

Per impostazione predefinita, Time Series Insights conserva i dati in base alla quantità di spazio di archiviazione di cui si esegue il provisioning (unità &#215; la quantità di spazio di archiviazione per unità) e in ingresso.

## <a name="data-retention"></a>Conservazione dei dati

È possibile modificare l'impostazione Tempo di conservazione dei dati nell'ambiente Azure Time Series Insights.You can change the **Data retention time** setting in your Azure Time Series Insights environment. È possibile abilitare fino a 400 giorni di conservazione. 

Azure Time Series Insights ha due modalità:Azure Time Series Insights has two modes:

* Una modalità consente di ottimizzare i dati più aggiornati. Applica un criterio per eliminare i **dati obsoleti** lasciando disponibili i dati recenti con l'istanza. Questa modalità è attivata, per impostazione predefinita. 
* L'altro ottimizza i dati per rimanere al di sotto dei limiti di conservazione configurati. **L'ingresso** di pausa impedisce l'ingresso dei nuovi dati quando viene selezionato come **comportamento superato**dal limite di archiviazione .

È possibile regolare la conservazione e passare tra le due modalità nella pagina di configurazione dell'ambiente nel portale di Azure.You can adjust retention and toggle between the two modes on the environment's configuration page in the Azure portal.

> [!IMPORTANT]
> È possibile configurare un massimo di 400 giorni di conservazione dei dati nell'ambiente Azure Time Series Insights GA.

### <a name="configure-data-retention"></a>Configurare la conservazione dei dati

1. Nel [portale di Azure](https://portal.azure.com) selezionare l'ambiente Time Series Insights desiderato.

1. Nel riquadro **dell'ambiente Time Series Insights,** in **Impostazioni**, selezionare **Configurazione archiviazione**.

1. Nella casella **Tempo di conservazione dati (in giorni)** immettere un valore compreso tra 1 e 400.

   [![Configurare la conservazione](media/data-retention/configure-data-retention.png)](media/data-retention/configure-data-retention.png#lightbox)

> [!TIP]
> Per ulteriori informazioni su come implementare criteri di conservazione dei dati appropriati, vedere [Come configurare la conservazione](./time-series-insights-how-to-configure-retention.md).

## <a name="ingress-capacity"></a>Capacità in ingresso

[!INCLUDE [Azure Time Series Insights GA limits](../../includes/time-series-insights-ga-limits.md)]

### <a name="environment-planning"></a>Pianificazione dell'ambiente

La seconda area su cui concentrarsi per la pianificazione dell'ambiente Time Series Insights è la capacità in ingresso. La capacità di ingresso è un derivato dell'allocazione al minuto.

Dal punto di vista della limitazione delle richieste, un pacchetto di dati in ingresso con una dimensione del pacchetto di 32 KB viene considerato come 32 eventi, ognuno di 1 KB di dimensione. La dimensione massima consentita per gli eventi è 32 KB. I pacchetti di dati di dimensioni superiori a 32 KB vengono troncati.

È possibile aumentare la capacità di uno SKU S1 o S2 a 10 unità in un unico ambiente. Non è possibile eseguire la migrazione da un ambiente S1 a un S2. Non è possibile eseguire la migrazione da un ambiente S2 a un S1.

Per la capacità di ingresso, determinare innanzitutto l'ingresso totale richiesto su base mensile. Successivamente, determinare quali sono le esigenze per minuto. 

Limitazione e latenza svolgono un ruolo nella capacità al minuto. Se si dispone di un picco nell'ingresso dei dati che dura meno di 24 ore, Time Series Insights può "recuperare" a una velocità di ingresso di due volte le tariffe elencate nella tabella precedente.

Ad esempio, se si dispone di un singolo SKU S1, si ingresso i dati a una velocità di 720 eventi al minuto e i picchi di velocità dati per meno di un'ora a una velocità di 1.440 eventi o meno, non vi è alcuna latenza evidente nell'ambiente. Tuttavia, se si superano 1.440 eventi al minuto per più di un'ora, è probabile che si verifichi una latenza nei dati visualizzati e disponibili per le query nell'ambiente.

Potresti non sapere in anticipo quanti dati ci si aspetta di spingere. In questo caso, è possibile trovare i dati di telemetria per [l'hub IoT](../iot-hub/iot-hub-metrics.md) di Azure e gli hub eventi di Azure nella sottoscrizione al portale di Azure.In this case, you can find data telemetry for Azure IoT Hub and [Azure Event Hubs](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/) in your Azure portal subscription. I dati di telemetria consentono di determinare come eseguire il provisioning dell'ambiente. Usare il riquadro **Metriche** nel portale di Azure per l'origine eventi rispettiva per visualizzare i dati di telemetria. Dopo aver analizzato attentamente le metriche relative all'origine evento, è possibile eseguire in modo più efficiente la pianificazione e il provisioning dell'ambiente Time Series Insights.

### <a name="calculate-ingress-requirements"></a>Calcolare i requisiti del traffico in ingresso

Per calcolare i requisiti in ingresso:

- Verificare che la capacità in ingresso sia superiore alla tariffa media al minuto e che l'ambiente sia sufficientemente grande da gestire l'ingresso previsto equivalente a due volte la capacità per meno di un'ora.

- Se si verificano picchi in ingresso che durano più di 1 ora, utilizzare la frequenza di picco come media. Effettuare il provisioning di un ambiente con la capacità di gestire la frequenza di picco.

### <a name="mitigate-throttling-and-latency"></a>Ridurre la limitazione e la latenza

Per informazioni su come impedire la limitazione delle richieste e la latenza, vedere [Mitigate latency and throttling](time-series-insights-environment-mitigate-latency.md).

## <a name="shape-your-events"></a>Dare forma agli eventi

È importante assicurarsi che il modo in cui si inviano gli eventi a Time Series Insights supporti le dimensioni dell'ambiente di cui si sta esegue il provisioning. Al contrario, è possibile mappare le dimensioni dell'ambiente al numero di eventi letti da Time Series Insights e alle dimensioni di ogni evento. È inoltre importante considerare gli attributi che è possibile utilizzare per sezionare e filtrare quando si eseguono query sui dati.

> [!TIP]
> Esaminare la documentazione relativa al modello JSON in [Eventi di invio](time-series-insights-send-events.md).

## <a name="ensure-that-you-have-reference-data"></a>Assicurarsi di disporre dei dati di riferimento

Un set di dati di *riferimento* è una raccolta di elementi che aumentano gli eventi dall'origine eventi. Il motore di ingresso time Series Insights unisce ogni evento dall'origine eventi con la riga di dati corrispondente nel set di dati di riferimento. L'evento aumentato è quindi disponibile per la query. Il join è basato sulle colonne **chiave primaria** definite nel set di dati di riferimento.

> [!NOTE]
> I dati di riferimento non vengono uniti retroattivamente. Solo i dati in ingresso correnti e futuri vengono associati al set di dati di riferimento dopo la configurazione e il caricamento. Se prevedi di inviare una grande quantità di dati cronologici a Time Series Insights e non carichi o crei prima i dati di riferimento in Time Series Insights, potrebbe essere necessario rieseguire il lavoro (suggerimento: non divertente).  

Per ulteriori informazioni su come creare, caricare e gestire i dati di riferimento in Time Series Insights, leggere la [documentazione sul set](time-series-insights-add-reference-data-set.md)di dati di riferimento .

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Passaggi successivi

- Iniziare creando [un nuovo ambiente Time Series Insights nel portale](time-series-insights-get-started.md)di Azure.

- Scopri come [aggiungere un'origine eventi Hub eventi](time-series-insights-how-to-add-an-event-source-eventhub.md) a Time Series Insights.

- Informazioni su come [configurare un'origine eventi dell'hub IoT](time-series-insights-how-to-add-an-event-source-iothub.md).
