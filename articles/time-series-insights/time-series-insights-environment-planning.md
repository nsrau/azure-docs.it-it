---
title: Ridimensionare l'ambiente Time Series Insights di Azure | Microsoft Docs
description: Questo articolo viene descritto come seguire le procedure consigliate quando si pianifica un ambiente Azure Time Series Insights. Le aree che sono trattate includono la capacità di archiviazione, conservazione dei dati, capacità in ingresso, il monitoraggio e business continuità e ripristino di emergenza (BCDR).
services: time-series-insights
ms.service: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/29/2019
ms.custom: seodec18
ms.openlocfilehash: 2c11e3f623817894cea801173239cc386c6c3313
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165826"
---
# <a name="plan-your-azure-time-series-insights-ga-environment"></a>Pianificare l'ambiente di Azure ora Series Insights disponibile a livello generale

Questo articolo descrive come pianificare l'ambiente di disponibilità generale (GA) di Azure Time Series Insights in base la velocità in ingresso e i requisiti di conservazione dei dati.

## <a name="video"></a>Video

**Guardare questo video per altre informazioni sulla conservazione dei dati in Azure Time Series Insights e sulla pianificazione per tale**:<br /><br />

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>Procedure consigliate

Per iniziare a usare Time Series Insights, è consigliabile se si conoscono quanti dati si prevede di eseguire il push al minuto e quanto tempo occorre archiviare i dati.  

Per altre informazioni sulla capacità e sulla conservazione per entrambi gli SKU di Time Series Insights, vedere [Prezzi di Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).

Per pianificare meglio l'ambiente Time Series Insights per il successo a lungo termine, prendere in considerazione i seguenti attributi:

- <a href="#storage-capacity">Capacità di archiviazione</a>
- <a href="#data-retention">Periodo di conservazione dati</a>
- <a href="#ingress-capacity">Capacità in ingresso</a>
- <a href="#shape-your-events">Forme degli eventi</a>
- <a href="#ensure-that-you-have-reference-data">Accertarsi di disporre di dati di riferimento in posizione</a>

## <a name="storage-capacity"></a>Capacità di archiviazione

Per impostazione predefinita, Time Series Insights consente di mantenere i dati in base alla quantità di spazio di archiviazione viene effettuato il provisioning (unità &#215; la quantità di spazio di archiviazione per unità) e traffico in ingresso.

## <a name="data-retention"></a>Conservazione dei dati

È possibile modificare il **tempo di conservazione dati** impostazione nell'ambiente Time Series Insights. È possibile abilitare fino a 400 giorni di conservazione. 

Time Series Insights sono disponibili due modalità. Consente di ottimizzare una modalità per garantire che l'ambiente comprende i dati più aggiornati. Questa modalità è on, per impostazione predefinita. 

Consente di ottimizzare l'altra modalità per garantire che siano soddisfatti i limiti di conservazione. Nella seconda modalità, traffico in ingresso viene sospeso se viene raggiunta la capacità di archiviazione complessivo dell'ambiente. 

È possibile regolare conservazione e passare alternativamente tra le due modalità nella pagina di configurazione dell'ambiente nel portale di Azure.

Nell'ambiente Time Series Insights è possibile configurare un massimo di 400 giorni di conservazione dei dati.

### <a name="configure-data-retention"></a>Configurare la conservazione dei dati

1. Nel [portale di Azure](https://portal.azure.com) selezionare l'ambiente Time Series Insights desiderato.

1. Nel **ambiente Time Series Insights** riquadro, di sotto **impostazioni**, selezionare **configura**.

1. Nel **periodo di conservazione dei dati (in giorni)** immettere un valore compreso tra 1 e 400.

   [![Configurare la conservazione](media/environment-mitigate-latency/configure-retention.png)](media/environment-mitigate-latency/configure-retention.png#lightbox)

> [!TIP]
> Per altre informazioni su come implementare un criterio di conservazione dati appropriati, vedere [come configurare la conservazione](./time-series-insights-how-to-configure-retention.md).

## <a name="ingress-capacity"></a>Capacità in ingresso

Il secondo fattore da considerare per la pianificazione dell'ambiente Time Series Insights è capacità in ingresso. Capacità in ingresso è un derivato dell'allocazione al minuto.

Dal punto di vista della limitazione, un pacchetto di dati in ingresso che ha dimensioni pari a 32 KB viene considerato come 32 eventi, ogni 1 KB di dimensioni. Le dimensioni eventi massime consentite sono 32 KB. I pacchetti di dati superiori a 32 KB vengono troncati.

La tabella seguente riepiloga la capacità in ingresso per unità per ogni SKU di Time Series Insights:

|SKU  |Conteggio di eventi al mese  |Dimensioni eventi al mese  |Conteggio di eventi al minuto  |Dimensioni eventi al minuto  |
|---------|---------|---------|---------|---------|
|S1     |   30 milioni     |  30 GB     |  720    |  720 KB   |
|S2     |   300 milioni    |   300 GB   | 7\.200   | 7\.200 KB  |

È possibile aumentare la capacità di uno SKU S1 o S2 a 10 unità in un unico ambiente. È possibile eseguire la migrazione da un ambiente S1 a un livello S2. È possibile eseguire la migrazione da un ambiente S2 per un S1.

Per la capacità in ingresso, determinare innanzitutto l'ingresso totale che richiesta su base mensile. Successivamente, determinare quali esigenze sono al minuto. 

La limitazione delle richieste e la latenza svolgono un ruolo nella capacità al minuto. Se si verifica un picco nel traffico in ingresso dei dati che dura meno di 24 ore, Time Series Insights possibile "aggiornare" con una frequenza in ingresso di due volte le tariffe elencate nella tabella precedente.

Ad esempio, se si dispone di un singolo SKU S1, i dati in ingresso a una velocità di 720 eventi al minuto, e la velocità dei dati picchi per meno di un'ora con una frequenza di 1.440 eventi o meno, non è presente alcuna latenza evidente nell'ambiente in uso. Tuttavia, se si superano 1.440 eventi al minuto per più di un'ora, probabilmente si verificherà latenza nei dati visualizzati e disponibili per la query nell'ambiente in uso.

Potrebbe non essere noto in anticipo la quantità di dati di cui si prevede di eseguire il push. In questo caso, è possibile trovare dati di telemetria di dati per [IoT Hub di Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics) e [hub eventi di Azure](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/) nella sottoscrizione del portale di Azure. I dati di telemetria consentono di determinare come eseguire il provisioning dell'ambiente. Usare la **metriche** riquadro nel portale di Azure per l'origine dell'evento desiderata visualizzare i dati di telemetria. Dopo aver analizzato attentamente le metriche relative all'origine evento, è possibile eseguire in modo più efficiente la pianificazione e il provisioning dell'ambiente Time Series Insights.

### <a name="calculate-ingress-requirements"></a>Calcolare i requisiti del traffico in ingresso

Per calcolare i requisiti di traffico in ingresso:

- Verificare che la capacità in ingresso è sopra la tariffa al minuto medio e che l'ambiente sia sufficiente per gestire il traffico in ingresso previsto a due volte alla capacità per meno di un'ora.

- Se si verificano picchi di traffico in ingresso che ultimo per più di 1 ora, usare la velocità di picco come Media. Effettuare il provisioning di un ambiente con la capacità per gestire la velocità di picco.

### <a name="mitigate-throttling-and-latency"></a>Ridurre la limitazione e la latenza

Per informazioni su come evitare la limitazione e la latenza, vedere [Ridurre la limitazione e la latenza](time-series-insights-environment-mitigate-latency.md).

## <a name="shape-your-events"></a>Dare forma agli eventi

È importante garantire che la modalità di che invio degli eventi a Time Series Insights supporta le dimensioni dell'ambiente si esegue il provisioning. (Al contrario, è possibile mappare le dimensioni dell'ambiente Time Series Insights legge il numero di eventi e le dimensioni di ogni evento.) È anche importante considerare gli attributi che è possibile usare per sezionare e filtrare in base quando si esegue una query dei dati.

> [!TIP]
> Esaminare il codice JSON nella documentazione di data shaping [l'invio di eventi](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-send-events).

## <a name="ensure-that-you-have-reference-data"></a>Assicurarsi di avere i dati di riferimento

Oggetto *fanno riferimento a set di dati* è una raccolta di elementi che aumentano gli eventi dall'origine evento. Il motore in ingresso di Time Series Insights unisce ogni evento dell'origine evento con la riga di dati corrispondente del set di dati di riferimento. L'evento aumentato sarà quindi disponibile per query. Il join è basato sul **Primary Key** colonne definite nel set di dati riferimento.

> [!NOTE]
> I dati di riferimento non viene uniti in maniera retroattiva. Solo i dati in ingresso correnti e futuri sono matched e aggiunto al set di dati di riferimento dopo averlo configurato e caricato. Se si intende inviare una grande quantità di dati cronologici a Time Series Insights e non primo caricamento o creare i dati di riferimento in Time Series Insights, è necessario eseguire il rollforward del lavoro (Suggerimento: non è stato divertente).  

Per altre informazioni su come creare, caricare e gestire i dati di riferimento in Time Series Insights, vedere la [documentazione di set di dati di riferimento](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Passaggi successivi

- Iniziare creando [un nuovo ambiente Time Series Insights nel portale di Azure](time-series-insights-get-started.md).

- Informazioni su come [aggiungere un'origine evento hub eventi](time-series-insights-how-to-add-an-event-source-eventhub.md) di Time Series Insights.

- Ottenere informazioni su come [configurare un'origine evento IoT Hub](time-series-insights-how-to-add-an-event-source-iothub.md).
