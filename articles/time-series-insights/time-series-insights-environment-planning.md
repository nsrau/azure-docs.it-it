---
title: Ridimensionare l'ambiente Time Series Insights di Azure | Microsoft Docs
description: In questo articolo viene descritto come seguire le procedure consigliate durante la pianificazione di un ambiente Azure Time Series Insights, ad esempio relativamente a capacità di archiviazione, conservazione dei dati, capacità in ingresso, monitoraggio e BCDR (business disaster recovery).
services: time-series-insights
ms.service: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/29/2019
ms.custom: seodec18
ms.openlocfilehash: 1c6e8ef9effdb5cf31aee1603de28bed0d894cbb
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239099"
---
# <a name="plan-your-azure-time-series-insights-ga-environment"></a>Pianificare l'ambiente di Azure ora Series Insights disponibile a livello generale

Questo articolo descrive come pianificare l'ambiente di disponibilità generale (GA) di Azure Time Series Insights in base la velocità in ingresso e i requisiti di conservazione dei dati.

## <a name="video"></a>Video

### <a name="learn-more-about-data-retention-in-azuretime-series-insights-and-how-to-plan-for-itbr"></a>Altre informazioni sulla conservazione dei dati in AzureTime Series Insights e sulla pianificazione per tale.</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>Procedure consigliate

Per iniziare a usare Time Series Insights, è prima opportuno valutare il volume di dati di cui si prevede di eseguire il push al minuto e la durata del periodo di archiviazione dei dati.  

Per altre informazioni sulla capacità e sulla conservazione per entrambi gli SKU di Time Series Insights, vedere [Prezzi di Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).

Prendere in considerazione gli attributi seguenti per ottimizzare la pianificazione a lungo termine dell'ambiente:

- <a href="#understand-storage-capacity">Capacità di archiviazione</a>
- <a href="#understand-data-retention">Periodo di conservazione dati</a>
- <a href="#understand-ingress-capacity">Capacità in ingresso</a>
- <a href="#shape-your-events">Forme degli eventi</a>
- <a href="#ensure-you-have-reference-data">Verificare che i dati di riferimento sia presente nella posizione</a>

## <a name="understand-storage-capacity"></a>Introduzione alla capacità di archiviazione

Per impostazione predefinita, Azure Time Series Insights consente di mantenere i dati in base alla quantità di spazio di archiviazione che è stato effettuato il provisioning (unità volte quantità di spazio di archiviazione per unità) e traffico in ingresso.

## <a name="understand-data-retention"></a>Informazioni sulla conservazione dei dati

Per l'ambiente Time Series Insights è possibile configurare il **periodo di conservazione dei dati** fino a un massimo di 400 giorni. Time Series Insights dispone di due modalità: la prima consente l'ottimizzazione dell'ambiente per garantire che siano inclusi i dati più aggiornati (impostazione predefinita), mentre la seconda consente l'ottimizzazione dell'ambiente per garantire la conformità con i limiti di conservazione e sospendere il traffico in ingresso non appena viene raggiunta la capacità di archiviazione complessiva definita.  È possibile adeguare il periodo di conservazione e passare alternativamente tra le due modalità nella pagina di configurazione dell'ambiente nel portale di Azure.

Nell'ambiente Time Series Insights è possibile configurare un massimo di 400 giorni di conservazione dei dati.

### <a name="configure-data-retention"></a>Configurare la conservazione dei dati

1. Nel [portale di Azure](https://portal.azure.com) selezionare l'ambiente Time Series Insights desiderato.

1. Nella **pagina dell'ambiente Time Series Insights**, sotto il titolo **Impostazioni** selezionare **Configura**.

1. Nella casella **Periodo di conservazione dei dati (in giorni)** immettere un valore compreso tra 1 e 400.

   [![Configurare la conservazione](media/environment-mitigate-latency/configure-retention.png)](media/environment-mitigate-latency/configure-retention.png#lightbox)

> [!TIP]
> Altre informazioni sull'implementazione di un criterio di conservazione dati appropriati esaminando [come configurare la conservazione](./time-series-insights-how-to-configure-retention.md).

## <a name="understand-ingress-capacity"></a>Informazioni sulla capacità in ingresso

L'altro fattore da considerare durante la pianificazione è la capacità in ingresso, il cui valore deriva dall'allocazione al minuto.

Dal punto di vista della limitazione, un pacchetto di dati in ingresso la cui dimensione è 32 KB viene interpretato come 32 eventi, ciascuno con dimensioni pari a 1 KB. La dimensione massima consentita per ogni evento è pari a 32 KB. Pertanto, i pacchetti di dati con dimensioni maggiori di 32 KB vengono troncati.

Nella tabella seguente è riportata la capacità in ingresso per ogni SKU:

|SKU  |Numero di eventi / mese / unità  |Dimensioni eventi / mese / unità  |Conteggio di eventi al minuto / unità  | Le dimensioni al minuto / unità   |
|---------|---------|---------|---------|---------|
|S1     |   30 milioni     |  30 GB     |  720    |  720 KB   |
|S2     |   300 milioni    |   300 GB   | 7.200   | 7.200 KB  |

È possibile aumentare la capacità di uno SKU S1 o S2 a 10 unità in un unico ambiente. Non è possibile eseguire la migrazione da un ambiente S1 a un ambiente S2 o viceversa.

Per la capacità in ingresso, è prima necessario determinare la capacità in ingresso totale richiesta al mese. Determinare quindi la capacità in ingresso richiesta al minuto, poiché questo dato è interessato dalla limitazione e dalla latenza.

Se si verifica un picco del traffico di dati in ingresso che dura meno di 24 ore, Time Series Insights può gestirlo a una velocità in ingresso doppia rispetto alle velocità sopra elencate.

Ad esempio, se è presente un'unica SKU S1 e i dati in ingresso hanno una velocità pari a 720 eventi al minuto, in presenza di un picco che dura meno di un'ora a una velocità di 1440 eventi o meno, non si noterà alcuna latenza evidente a livello di ambiente. Tuttavia, se si superano 1440 eventi al minuto per più di un'ora, è possibile che nell'ambiente venga rilevata latenza a livello di dati visualizzati e disponibili per le query.

Non è possibile determinare in anticipo la quantità di dati di cui si prevede di eseguire il push. In questo caso è possibile fare riferimento ai dati di telemetria relativi a [Hub IoT di Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics) e [Hub eventi di Azure](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/) nel portale di Azure. Questi dati di telemetria consentono di determinare come eseguire il provisioning dell'ambiente. Nel portale di Azure usare la pagina **Metriche** relativa all'origine dell'evento desiderata per visualizzare i dati di telemetria corrispondenti. Dopo aver analizzato attentamente le metriche relative all'origine evento, è possibile eseguire in modo più efficiente la pianificazione e il provisioning dell'ambiente Time Series Insights.

### <a name="calculate-ingress-requirements"></a>Calcolare i requisiti del traffico in ingresso

- Verificare se la capacità in ingresso è superiore alla velocità media al minuto e se le dimensioni dell'ambiente sono sufficienti per la gestione del traffico in ingresso previsto a una capacità doppia per meno di un'ora.

- Se si verificano picchi del traffico in ingresso che durano più di un'ora, usare la velocità di picco come media ed eseguire il provisioning di un ambiente usando tale capacità per gestire la velocità di picco.

### <a name="mitigate-throttling-and-latency"></a>Ridurre la limitazione e la latenza

Per informazioni su come evitare la limitazione delle richieste e la latenza, leggere l'argomento sul [ridurre la latenza e la limitazione delle richieste](time-series-insights-environment-mitigate-latency.md).

## <a name="shape-your-events"></a>Dare forma agli eventi

È importante assicurarsi che il modo in cui si inviano eventi a Time Series Insights supporta le dimensioni dell'ambiente si esegue il provisioning (al contrario, è possibile mappare le dimensioni dell'ambiente Time Series Insights legge il numero di eventi e le dimensioni di ogni evento). Analogamente, è importante tenere conto degli attributi che potrebbe essere necessario sezionare e filtrare durante l'esecuzione di query sui dati.

> [!TIP]
> Esaminare il codice JSON nella documentazione di data shaping [l'invio di eventi](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-send-events).

## <a name="ensure-you-have-reference-data"></a>Assicurarsi di che avere dati di riferimento

Oggetto **Set di dati di riferimento** è una raccolta di elementi che aumentano gli eventi dall'origine evento. Il motore in ingresso di Time Series Insights crea un join tra ogni evento dell'origine evento e la riga di dati corrispondente nel set di dati di riferimento. Questo evento aumentato sarà quindi disponibile per le query. Questo join è basato sulla colonna o le colonne di chiavi primarie definite nel set di dati di riferimento.

Si noti che non viene creato un join dei dati di riferimento in maniera retroattiva. Questo significa che solo i dati in ingresso correnti e futuri vengono uniti in join al set di dati di riferimento, appena viene configurato e caricato.  Se si prevede di inviare molti dati cronologici a Time Series Insights e non si caricano o creano i dati di riferimento in Time Series Insights prima di tutto, potrebbe essere necessario rieseguire il lavoro.  

Per altre informazioni su come creare, caricare e gestire i dati di riferimento in Time Series Insights, vedere la [documentazione dedicata ai dati di riferimento](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Passaggi successivi

- Iniziare creando [un nuovo ambiente Time Series Insights nel portale di Azure](time-series-insights-get-started.md).

- Informazioni su come [aggiungere un'origine evento Hub eventi](time-series-insights-how-to-add-an-event-source-eventhub.md) di Time Series Insights.

- Ottenere informazioni su come [configurare un'origine evento IoT Hub](time-series-insights-how-to-add-an-event-source-iothub.md).
