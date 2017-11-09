---
title: Visualizzare i dati di analisi di App Web di Azure | Microsoft Docs
description: "È possibile usare la soluzione Analisi app Web di Azure per ottenere informazioni dettagliate su App Web di Azure, raccogliendo metriche diverse in tutte le risorse app Web di Azure."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 20ff337f-b1a3-4696-9b5a-d39727a94220
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2017
ms.author: banders
ms.openlocfilehash: 9ef26d4b6bfd92925a70b7bbdf8979e287c73445
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="view-analytic-data-for-metrics-across-all-your-azure-web-app-resources"></a>Visualizzare i dati di analisi per le metriche di tutte le risorse app Web di Azure

![Simbolo di App Web](./media/log-analytics-azure-web-apps-analytics/azure-web-apps-analytics-symbol.png)  
La soluzione Analisi app Web di Azure (anteprima) fornisce informazioni dettagliate su [App Web di Azure](../app-service/app-service-web-overview.md), raccogliendo metriche diverse in tutte le risorse app Web di Azure. Con questa soluzione, è possibile analizzare e cercare i dati relativi alle metriche delle risorse app Web.

La soluzione consente di visualizzare:

- Principali app Web con tempi di risposta più alti
- Numero di richieste tra le app Web, incluse le richieste riuscite e non riuscite
- Principali app Web con il maggior traffico in ingresso e in uscita
- Principali piani di servizio con utilizzo elevato di memoria e CPU
- Operazioni del log attività di App Web di Azure

## <a name="connected-sources"></a>Origini connesse

A differenza della maggior parte delle altre soluzioni di Log Analytics, i dati per App Web di Azure non vengono raccolti dagli agenti. Tutti i dati usati dalla soluzione provengono direttamente da Azure.

| Origine connessa | Supportato | Descrizione |
| --- | --- | --- |
| [Agenti di Windows](log-analytics-windows-agents.md) | No | La soluzione non raccoglie le informazioni dagli agenti di Windows. |
| [Agenti Linux](log-analytics-linux-agents.md) | No | La soluzione non raccoglie le informazioni dagli agenti di Linux. |
| [Gruppo di gestione SCOM](log-analytics-om-agents.md) | No | La soluzione non raccoglie le informazioni dagli agenti in un gruppo di gestione SCOM connesso. |
| [Account di archiviazione di Azure](log-analytics-azure-storage.md) | No | La soluzione non raccoglie le informazioni da Archiviazione di Azure. |

## <a name="prerequisites"></a>Prerequisiti

- Per accedere alle informazioni relative alle metriche delle risorse app Web di Azure, è necessaria una sottoscrizione di Azure.

## <a name="configuration"></a>Configurazione

Seguire questa procedura per configurare la soluzione Analisi app Web di Azure per le aree di lavoro.

1. Abilitare la soluzione Analisi app Web di Azure da [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureWebAppsAnalyticsOMS?tab=Overview) o seguendo la procedura illustrata in [Aggiungere soluzioni di gestione di Log Analytics di Azure all'area di lavoro](log-analytics-add-solutions.md).
2. [Abilitare la registrazione delle metriche delle risorse di Azure in OMS tramite PowerShell](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell).

La soluzione Analisi app Web di Azure raccoglie due set di metriche da Azure:

- Metriche di App Web di Azure
  - Working set della memoria medio
  - Tempo medio di risposta
  - Byte ricevuti/inviati
  - Tempo CPU
  - Richieste
  - Working set della memoria
  - Httpxxx
- Metriche del piano di servizio app
  - Byte ricevuti/inviati
  - Percentuale CPU
  - Lunghezza coda disco
  - Lunghezza coda HTTP
  - Percentuale memoria

Le metriche del piano di servizio app vengono raccolte solo se si usa un piano di servizio dedicato. Ciò non si applica ai piani di servizio app gratuiti o condivisi.

Se si aggiunge la soluzione usando il portale di OMS, verrà visualizzato il riquadro seguente. È necessario [abilitare la registrazione delle metriche delle risorse di Azure in OMS tramite PowerShell](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell).

![Notifica di esecuzione della valutazione](./media/log-analytics-azure-web-apps-analytics/performing-assessment.png)

Dopo aver configurato la soluzione, il flusso di dati nell'area di lavoro deve iniziare entro 15 minuti.

## <a name="using-the-solution"></a>Uso della soluzione

Quando si aggiunge la soluzione Analisi app Web di Azure all'area di lavoro, il riquadro **Analisi app Web di Azure** viene aggiunto al dashboard Panoramica. Questo riquadro mostra il numero di app Web di Azure a cui la soluzione ha accesso nella sottoscrizione di Azure.

![Riquadro di Analisi app Web di Azure](./media/log-analytics-azure-web-apps-analytics/azure-web-apps-analytics-tile.png)

### <a name="view-azure-web-apps-analytics-information"></a>Visualizzare le informazioni di Analisi app Web di Azure

Fare clic sul riquadro **Analisi app Web di Azure** per aprire il dashboard di **Analisi app Web di Azure**. Il dashboard include i pannelli nella tabella seguente. Ogni panello elenca fino a dieci elementi corrispondenti ai criteri del pannello per l'ambito e l'intervallo di tempo specificati. È possibile eseguire una ricerca log per ottenere tutti i record facendo clic su **Vedi tutto** nella parte inferiore del pannello o facendo clic sull'intestazione del pannello.

[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

| Colonna | Descrizione |
| --- | --- |
| App Web di Azure |   |
| Tendenze richieste app Web | Mostra un grafico a linee relativo alla tendenza delle richieste delle app Web per l'intervallo di date selezionato e un elenco delle prime dieci richieste Web. Fare clic sul grafico a linee per eseguire una ricerca log per <code>Type=AzureMetrics ResourceId=*"/MICROSOFT.WEB/SITES/"* (MetricName=Requests OR MetricName=Http*) &#124; measure avg(Average) by MetricName interval 1HOUR</code> <br>Fare clic su una voce di richiesta Web per eseguire una ricerca log per la tendenza delle metriche per la richiesta Web. |
| Tempo di risposta app Web | Mostra un grafico a linee relativo al tempo di risposta delle app Web per l'intervallo di date selezionato. Mostra anche un elenco dei primi dieci tempi di risposta delle app Web. Fare clic sul grafico per eseguire una ricerca log di <code>Type:AzureMetrics ResourceId=*"/MICROSOFT.WEB/SITES/"* MetricName="AverageResponseTime" &#124; measure avg(Average) by Resource interval 1HOUR</code><br> Fare clic su un'app Web per eseguire una ricerca log che restituisce i tempi di risposta per l'app Web. |
| Traffico app Web | Mostra un grafico a linee relativo al traffico delle app Web, in MB, ed elenca le app Web con più traffico. Fare clic sul grafico per eseguire una ricerca log di <code>Type:AzureMetrics ResourceId=*"/MICROSOFT.WEB/SITES/"*  MetricName=BytesSent OR BytesReceived &#124; measure sum(Average) by Resource interval 1HOUR</code><br> Mostra tutte le app Web con traffico nell'ultimo minuto. Fare clic su un'app Web per eseguire una ricerca log che mostra i byte ricevuti e inviati per l'app Web. |
| Piani di servizio app di Azure |   |
| Piani di servizio app con utilizzo della CPU &gt; 80% | Mostra il numero totale di piani di servizio app che presentano un utilizzo della CPU superiore all'80% ed elenca i primi dieci piani di servizio app in base all'utilizzo della CPU. Fare clic sull'area del totale per eseguire una ricerca log per <code>Type=AzureMetrics ResourceId=*"/MICROSOFT.WEB/SERVERFARMS/"* MetricName=CpuPercentage &#124; measure Avg(Average) by Resource</code><br> Mostra un elenco dei piani di servizio app con l'utilizzo medio della CPU. Fare clic su un piano di servizio app per eseguire una ricerca log che mostra il relativo utilizzo medio della CPU. |
| Piani di servizio app con utilizzo della memoria &gt; 80% | Mostra il numero totale di piani di servizio app che presentano un utilizzo della memoria superiore all'80% ed elenca i primi dieci piani di servizio app in base all'utilizzo della memoria. Fare clic sull'area del totale per eseguire una ricerca log per <code>Type=AzureMetrics ResourceId=*"/MICROSOFT.WEB/SERVERFARMS/"* MetricName=MemoryPercentage &#124; measure Avg(Average) by Resource</code><br> Mostra un elenco dei piani di servizio app con l'utilizzo medio della memoria. Fare clic su un piano di servizio app per eseguire una ricerca log che mostra il relativo utilizzo medio della memoria. |
| Log attività di App Web di Azure |   |
| Audit attività di App Web di Azure | Indica il numero totale di app Web con [log attività](log-analytics-activity.md) ed elenca le prime dieci operazioni del log attività. Fare clic sull'area del totale per eseguire una ricerca log per <code>Type=AzureActivity ResourceProvider= "Azure Web Sites" &#124; measure count() by OperationName</code><br> Mostra un elenco di operazioni del log attività. Fare clic su un'operazione del log attività per eseguire una ricerca log che elenca i record per l'operazione. |



### <a name="azure-web-apps"></a>App Web di Azure 

Nel dashboard è possibile eseguire il drill-down per ottenere informazioni più dettagliate sulle metriche delle app Web. Il primo set di pannelli mostra la tendenza delle richieste delle app Web, il numero di errori (ad esempio, HTTP404), il traffico e il tempo di risposta medio nel tempo. Mostra anche una suddivisione delle metriche per le diverse app Web.

![v (App Web di Azure)](./media/log-analytics-azure-web-apps-analytics/web-apps-dash01.png)

Un motivo importante per visualizzare i dati è la possibilità di identificare un'app Web con tempi di risposta elevati, per poterla analizzare e individuare la causa principale. Viene applicato anche un limite di soglia per aiutare a identificare più facilmente i casi problematici.

- Le app Web in rosso hanno tempi di risposta superiori a 1 secondo.
- Le app Web in arancione hanno tempi di risposta superiori a 0,7 secondi e inferiori a 1 secondo.
- Le app Web in verde hanno tempi di risposta inferiori a 0,7 secondi.

Nell'immagine di esempio di ricerca log seguente è possibile vedere che l'app Web *anugup3* ha un tempo di risposta molto più elevato rispetto alle altre app Web.

![Esempio di ricerca log](./media/log-analytics-azure-web-apps-analytics/web-app-search-example.png)

### <a name="app-service-plans"></a>Piani di servizio app

Se si usano piani di servizio dedicati, è anche possibile raccogliere le metriche per i piani di servizio app. Questa vista mostra i piani di servizio app con utilizzo elevato di memoria o CPU (&gt; 80%). Mostra anche i principali servizi app con utilizzo elevato di memoria o CPU. Analogamente, viene applicato un limite di soglia per aiutare a identificare più facilmente i casi problematici.

- I piani di servizio app in rosso hanno un utilizzo di CPU/memoria superiore all'80%.
- I piani di servizio app in arancione hanno un utilizzo di CPU/memoria superiore al 60% e inferiore all'80%.
- I piani di servizio app in verde hanno un utilizzo di CPU/memoria inferiore al 60%.

![Pannelli della sezione Azure App Service Plans (Piani di servizio app di Azure)](./media/log-analytics-azure-web-apps-analytics/web-apps-dash02.png)

## <a name="azure-web-apps-log-searches"></a>Ricerche log di App Web di Azure

L'**elenco delle query di ricerca più comuni di App Web di Azure** mostra tutti i log attività correlati per le app Web, con informazioni dettagliate sulle operazioni eseguite sulle risorse app Web. Elenca anche tutte le operazioni correlate e il numero di volte in cui sono state eseguite.

Usando una query di ricerca log come punto di partenza, è possibile creare facilmente un avviso. È ad esempio possibile creare un avviso per quando il tempo di risposta medio di una metrica è superiore a un secondo.

## <a name="next-steps"></a>Passaggi successivi

- Creare un [avviso](log-analytics-alerts-creating.md) per una metrica specifica.
- Usare [Ricerca Log](log-analytics-log-searches.md) per visualizzare le informazioni dettagliate dai log attività.
