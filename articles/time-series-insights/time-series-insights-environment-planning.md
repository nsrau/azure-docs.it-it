---
title: Ridimensionare l'ambiente Time Series Insights di Azure | Microsoft Docs
description: "In questo articolo viene descritto come seguire le procedure consigliate durante la pianificazione di un ambiente Azure Time Series Insights, ad esempio relativamente a capacità di archiviazione, conservazione dei dati, capacità in ingresso e monitoraggio."
services: time-series-insights
ms.service: time-series-insights
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: 5fb158ba162dd199f419f9568de08a7a18c833dd
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
---
# <a name="plan-your-azure-time-series-insights-environment"></a>Pianificare l'ambiente Azure Time Series Insights

In questo articolo viene descritto come pianificare l'ambiente Azure Time Series Insights in base alla velocità in ingresso e ai requisiti di conservazione dei dati.

## <a name="best-practices"></a>Procedure consigliate

Per iniziare a usare Time Series Insights, è prima opportuno valutare il volume di dati di cui si prevede di eseguire il push al minuto e la durata del periodo di archiviazione dei dati.  

Per altre informazioni sulla capacità e sulla conservazione per entrambi gli SKU di Time Series Insights, vedere [Prezzi di Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).

Prendere in considerazione gli attributi seguenti per ottimizzare la pianificazione a lungo termine dell'ambiente: 
- Capacità di archiviazione
- Periodo di conservazione dei dati
- Capacità in ingresso 

## <a name="understand-storage-capacity"></a>Introduzione alla capacità di archiviazione
Per impostazione predefinita, Time Series Insights conserva i dati in base alla quantità di spazio di archiviazione di cui è stato eseguito il provisioning (unità moltiplicate per la quantità di spazio di archiviazione per unità) e al traffico in ingresso.

## <a name="understand-data-retention"></a>Informazioni sulla conservazione dei dati
Per l'ambiente Time Series Insights è possibile configurare il **periodo di conservazione dei dati** fino a un massimo di 400 giorni.  Time Series Insights dispone di due modalità: la prima consente l'ottimizzazione dell'ambiente per garantire che siano inclusi i dati più aggiornati (impostazione predefinita), mentre la seconda consente l'ottimizzazione dell'ambiente per garantire la conformità con i limiti di conservazione e sospendere il traffico in ingresso non appena viene raggiunta la capacità di archiviazione complessiva definita.  È possibile adeguare il periodo di conservazione e passare alternativamente tra le due modalità nella pagina di configurazione dell'ambiente nel portale di Azure.

Nell'ambiente Time Series Insights è possibile configurare un massimo di 400 giorni di conservazione dei dati.

## <a name="configure-data-retention"></a>Configurare la conservazione dei dati

1. Nel [portale di Azure](https://portal.azure.com) selezionare l'ambiente Time Series Insights desiderato.

2. Nella **pagina dell'ambiente Time Series Insights**, sotto il titolo **Impostazioni** selezionare **Configura**. 

3. Nella casella **Periodo di conservazione dei dati (in giorni)** immettere un valore compreso tra 1 e 400.

   ![Configurare la conservazione](media/environment-mitigate-latency/configure-retention.png)

## <a name="understand-ingress-capacity"></a>Informazioni sulla capacità in ingresso

L'altro fattore da considerare durante la pianificazione è la capacità in ingresso, il cui valore deriva dall'allocazione al minuto. 

Dal punto di vista della limitazione, un pacchetto di dati in ingresso la cui dimensione è 32 KB viene interpretato come 32 eventi, ciascuno con dimensioni pari a 1 KB. La dimensione massima consentita per ogni evento è pari a 32 KB. Pertanto, i pacchetti di dati con dimensioni maggiori di 32 KB vengono troncati.

Nella tabella seguente è riportata la capacità in ingresso per ogni SKU:

|SKU  |Conteggio degli eventi al mese, per unità  |Dimensioni degli eventi al mese, per unità  |Conteggio degli eventi al minuto, per unità  | Dimensioni al minuto, per unità   |
|---------|---------|---------|---------|---------|
|S1     |   30 milioni     |  30 GB     |  700    |  700 KB   |
|S2     |   300 milioni    |   300 GB   | 7.000   | 7.000 KB  |

È possibile aumentare la capacità di uno SKU S1 o S2 a 10 unità in un unico ambiente. Non è possibile eseguire la migrazione da un ambiente S1 a un ambiente S2 o viceversa. 

Per la capacità in ingresso, è prima necessario determinare la capacità in ingresso totale richiesta al mese. Determinare quindi la capacità in ingresso richiesta al minuto, poiché questo dato è interessato dalla limitazione e dalla latenza.

Se si verifica un picco del traffico di dati in ingresso che dura meno di 24 ore, Time Series Insights può gestirlo a una velocità in ingresso doppia rispetto alle velocità sopra elencate. 

Ad esempio, se è presente un'unica SKU S1 e i dati in ingresso hanno una velocità pari a 700 eventi al minuto, in presenza di un picco che dura meno di un'ora a una velocità di 1400 eventi o meno, non si noterà alcuna latenza evidente a livello di ambiente. Tuttavia, se si superano 1400 eventi al minuto per più di un'ora, è possibile che nell'ambiente venga rilevata latenza a livello di dati visualizzati e disponibili per le query. 

Non è possibile determinare in anticipo la quantità di dati di cui si prevede di eseguire il push. In questo caso è possibile fare riferimento ai dati di telemetria relativi a [Hub IoT di Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics) e [Hub eventi di Azure](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/) nel portale di Azure. Questi dati di telemetria consentono di determinare come eseguire il provisioning dell'ambiente. Nel portale di Azure usare la pagina **Metriche** relativa all'origine dell'evento desiderata per visualizzare i dati di telemetria corrispondenti. Dopo aver analizzato attentamente le metriche relative all'origine evento, è possibile eseguire in modo più efficiente la pianificazione e il provisioning dell'ambiente Time Series Insights.

## <a name="calculate-ingress-requirements"></a>Calcolare i requisiti del traffico in ingresso

- Verificare se la capacità in ingresso è superiore alla velocità media al minuto e se le dimensioni dell'ambiente sono sufficienti per la gestione del traffico in ingresso previsto a una capacità doppia per meno di un'ora.

- Se si verificano picchi del traffico in ingresso che durano più di un'ora, usare la velocità di picco come media ed eseguire il provisioning di un ambiente usando tale capacità per gestire la velocità di picco.
 
## <a name="mitigate-throttling-and-latency"></a>Ridurre la limitazione e la latenza

Per informazioni su come evitare la limitazione e la latenza, vedere [Ridurre la limitazione e la latenza](time-series-insights-environment-mitigate-latency.md). 

## <a name="next-steps"></a>Passaggi successivi
- [Come aggiungere un'origine evento di un hub eventi](time-series-insights-how-to-add-an-event-source-eventhub.md)
- [Come aggiungere un'origine evento di un hub IoT](time-series-insights-how-to-add-an-event-source-iothub.md)
