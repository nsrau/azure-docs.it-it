---
title: 'Metriche in Monitor di Azure : Documenti Microsoft'
description: Descrive le metriche in Monitoraggio di Azure, che sono dati di monitoraggio leggeri in grado di supportare scenari quasi in tempo reale.
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: a02b3df02d455db8a7dfd21f63d659f75a66e446
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457315"
---
# <a name="metrics-in-azure-monitor"></a>Metriche in Monitoraggio di Azure

> [!NOTE]
> La piattaforma dati di Monitoraggio di Azure si basa su due tipi di dati fondamentali: Metriche e log. Questo articolo descrive le metriche. Fare riferimento a [Log in Monitoraggio di Azure](data-platform-logs.md) per una descrizione dettagliata dei log e alla piattaforma dati di Monitoraggio di [Azure](data-platform.md) per un confronto tra i due.

Le metriche in Monitoraggio di Azure sono leggere e in grado di supportare scenari quasi in tempo reale, rendendoli particolarmente utili per gli avvisi e il rilevamento rapido dei problemi. Questo articolo descrive come sono strutturate le metriche, cosa è possibile fare con esse e identifica diverse origini dati che archiviano i dati nelle metriche.

## <a name="what-are-metrics"></a>Cosa sono le metriche?
Le metriche sono valori numerici che descrivono alcuni aspetti di un sistema in un determinato momento, Le metriche vengono raccolte a intervalli regolari e sono utili per gli avvisi perché possono essere campionate di frequente e un avviso può essere generato rapidamente con una logica relativamente semplice.

## <a name="what-can-you-do-with-azure-monitor-metrics"></a>Cosa è possibile fare con le metriche di Monitoraggio di Azure?
The following table lists the different ways that you can use metric data in Azure Monitor.

|  |  |
|:---|:---|
| Analisi | Usa [Esplora metriche](metrics-charts.md) per analizzare le metriche raccolte in un grafico e confrontare le metriche di risorse diverse. |
| Visualizzazione | Aggiungere un grafico da Esplora metriche a un dashboard di Azure.Pin a chart from metrics explorer to an [Azure dashboard.](../learn/tutorial-app-dashboards.md)<br>Creare una [cartella di lavoro](../app/usage-workbooks.md) da combinare con più set di dati in un report interattivo. Esportare i risultati di una query in [Grafana](grafana-plugin.md) per sfruttare il dashboard e combinarle con altre origini dati. |
| Avviso | Configurare una regola di [avviso metrica](alerts-metric.md) che invia una notifica o esegue [un'azione automatica](action-groups.md) quando il valore della metrica supera una soglia. |
| Automatizzare |  Usare [la scalabilità automatica](autoscale-overview.md) per aumentare o ridurre le risorse in base a un valore metrico che supera una soglia. |
| Esportazione | [Instradare le metriche ai log](resource-logs-collect-storage.md) per analizzare i dati in Analisi delle metriche di Azure insieme ai dati nei log di monitoraggio di Azure e per archiviare i valori delle metriche per più di 93 giorni.<br>Eseguire lo streaming delle metriche a un [hub eventi](stream-monitoring-data-event-hubs.md) per instradarle a sistemi esterni. |
| Recupero | Accedere ai valori delle metriche da una riga di comando tramite i cmdlet di PowerShellAccess metric values from a command line [using PowerShell cmdlets](https://docs.microsoft.com/powershell/module/az.applicationinsights)<br>Accedere ai valori delle metriche da un'applicazione personalizzata tramite [l'API REST.](rest-api-walkthrough.md)<br>Accedere ai valori delle metriche da una riga di comando utilizzando [CLI](/cli/azure/monitor/metrics). |
| Archiviazione | [Archiviare](..//learn/tutorial-archive-data.md) la cronologia relativa alle prestazioni o all'integrità della risorsa a scopi di conformità, verifica o creazione di report offline. |

## <a name="how-is-data-in-azure-monitor-metrics-structured"></a>Come sono strutturati i dati in Metriche di Monitoraggio di Azure?
I dati raccolti dalle metriche di Monitoraggio di Azure vengono archiviati in un database di serie temporali ottimizzato per l'analisi dei dati con timestamp. Ogni set di valori di metrica è una serie temporale con le proprietà seguenti:Each set of metric values is a time series with the following properties:

* Il tempo in cui il valore è stato raccolto
* Risorsa a cui è associato il valore
* Uno spazio dei nomi che agisce come una categoria per la metrica
* Un nome di metrica
* Il valore stesso
* Alcune metriche possono avere più dimensioni, come descritto in [Metriche multidimensionali](#multi-dimensional-metrics). Le metriche personalizzate possono avere fino a 10 dimensioni.

## <a name="multi-dimensional-metrics"></a>Metriche multidimensionali
Una delle sfide per i dati delle metriche è che spesso dispone di informazioni limitate per fornire il contesto per i valori raccolti. Monitoraggio di Azure risolve questa sfida con metriche multidimensionali. Le dimensioni di una metrica sono coppie nome/valore contenenti dati aggiuntivi per descrivere il valore della metrica. Ad esempio, una metrica _Spazio disponibile su disco_ potrebbe avere una dimensione denominata _Unità_ con valori _C:_, _D:_, che consente di visualizzare lo spazio disponibile su disco su tutte le unità o per ogni unità singolarmente.

L'esempio seguente mostra due set di dati per un'ipotetica metrica denominata _Velocità effettiva di rete_. Il primo set di dati non ha dimensioni. Il secondo set di dati mostra i valori con due dimensioni, _Indirizzo IP_ e _Direzione_:

### <a name="network-throughput"></a>Velocità effettiva della rete

| Timestamp     | Valore della metrica |
| ------------- |:-------------|
| 8/9/2017 8:14 | 1.331,8 Kbps |
| 8/9/2017 8:15 | 1.141,4 Kbps |
| 8/9/2017 8:16 | 1.110,2 Kbps |

Questa metrica non dimensionale può solo rispondere a una domanda di base come "Qual è la velocità effettiva della rete in un determinato momento?"

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>Velocità effettiva della rete + due dimensioni ("IP" e "Direzione")

| Timestamp     | Dimensione "IP"   | Dimensione "Direzione" | Valore della metrica|
| ------------- |:-----------------|:------------------- |:-----------|
| 8/9/2017 8:14 | IP = "192.168.5.2" | Direzione = "Invio"    | 646,5 Kbps |
| 8/9/2017 8:14 | IP = "192.168.5.2" | Direzione = "Ricezione" | 420,1 Kbps |
| 8/9/2017 8:14 | IP = "10.24.2.15"  | Direzione = "Invio"    | 150,0 Kbps |
| 8/9/2017 8:14 | IP = "10.24.2.15"  | Direzione = "Ricezione" | 115,2 Kbps |
| 8/9/2017 8:15 | IP = "192.168.5.2" | Direzione = "Invio"    | 515,2 Kbps |
| 8/9/2017 8:15 | IP = "192.168.5.2" | Direzione = "Ricezione" | 371,1 Kbps |
| 8/9/2017 8:15 | IP = "10.24.2.15"  | Direzione = "Invio"    | 155,0 Kbps |
| 8/9/2017 8:15 | IP = "10.24.2.15"  | Direzione = "Ricezione" | 100,1 Kbps |

Questa metrica può rispondere a domande del tipo "Qual è la velocità effettiva di rete per ogni indirizzo IP?" e "Quanti dati sono stati inviati rispetto a quelli ricevuti?" Le metriche multidimensionali presentano valori analitici e diagnostici aggiuntivi rispetto alle metriche dimensionali.

## <a name="interacting-with-azure-monitor-metrics"></a>Interazione con le metriche di Monitoraggio di Azure
Usare [Esplora metriche](metrics-charts.md) per analizzare in modo interattivo i dati nel database delle metriche e visualizzare graficamente i valori di più metriche nel tempo. È possibile aggiungere i grafici a una dashboard per visualizzarli con altre visualizzazioni. È anche possibile recuperare le metriche usando l'[API REST di monitoraggio di Azure](rest-api-walkthrough.md).

![Esplora metriche](media/data-platform/metrics-explorer.png)

## <a name="sources-of-azure-monitor-metrics"></a>Origini metriche di Monitoraggio di AzureSources of Azure Monitor Metrics
Sono disponibili tre origini principali delle metriche raccolte da Monitoraggio di Azure. Una volta raccolte nel database delle metriche di Monitoraggio di Azure, queste possono essere valutate insieme indipendentemente dall'origine.

Le **metriche della piattaforma** vengono create da risorse di Azure e offrono visibilità riguardo a integrità e prestazioni. Ogni tipo di risorsa crea un [set distinto di metriche](metrics-supported.md) senza che sia necessaria alcuna configurazione. Le metriche della piattaforma vengono raccolte dalle risorse di Azure con una frequenza di un minuto, a meno che non venga specificato diversamente nella definizione della metrica. 

**Le metriche** del sistema operativo guest vengono raccolte dal sistema operativo guest di una macchina virtuale. Abilitare le metriche del sistema operativo guest per le macchine virtuali Windows con [l'estensione di diagnostica di Windows (WAD)](../platform/diagnostics-extension-overview.md) e per le macchine virtuali Linux con [InfluxData Telegraf Agent](https://www.influxdata.com/time-series-platform/telegraf/).

Le **metriche dell'applicazione** vengono create da Application Insights per le applicazioni monitorate e permettono di rilevare i problemi di prestazioni e tenere traccia delle tendenze relative all'uso dell'applicazione. Sono inclusi valori come il _tempo di risposta del server_ e le _eccezioni del browser_.

**Le metriche personalizzate** sono metriche definite in aggiunta alle metriche standard disponibili automaticamente. È possibile [definire metriche personalizzate nell'applicazione](../app/api-custom-events-metrics.md) monitorate da Application Insights o creare metriche personalizzate per un servizio di Azure usando l'API [delle metriche personalizzate.](metrics-store-custom-rest-api.md)

## <a name="retention-of-metrics"></a>Conservazione delle metriche
Per la maggior parte delle risorse in Azure, le metriche vengono archiviate per 93 giorni. Ci sono alcune eccezioni:

**Metriche del sistema operativo guest**
-   **Metriche classiche del sistema operativo guest**. Si tratta di contatori delle prestazioni raccolti [dall'estensione di diagnostica di Windows (WAD)](../platform/diagnostics-extension-overview.md) o dall'estensione diagnostica Linux (LAD) e instradati a un account di archiviazione di Azure.These are performance counters collected by the Windows Diagnostic Extension (WAD) or the Linux Diagnostic Extension [(LAD)](../../virtual-machines/extensions/diagnostics-linux.md) and routed to an Azure storage account. La conservazione per queste metriche è di 14 giorni.
-   **Metriche del sistema operativo guest inviate alle metriche di Monitoraggio di Azure.** Si tratta di contatori delle prestazioni raccolti [dall'estensione diagnostica di Windows (WAD)](diagnostics-extension-overview.md) e inviati al sink di dati di Monitoraggio di Azure o tramite l'agente InfluxData Telegraf su computer Linux.These are performance counters collected by the Windows Diagnostic Extension (WAD) and sent to the Azure Monitor data [sink](diagnostics-extension-overview.md#data-destinations), or via [influxData Telegraf Agent](https://www.influxdata.com/time-series-platform/telegraf/) on Linux machines. La conservazione per queste metriche è di 93 giorni.
-   **Metriche del sistema operativo guest raccolte dall'agente di Log Analytics.** Si tratta di contatori delle prestazioni raccolti dall'agente di Log Analytics e inviati a un'area di lavoro di Log Analytics.These are performance counters collected by the Log Analytics agent and sent to a Log Analytics workspace. La conservazione per queste metriche è di 31 giorni e può essere estesa fino a 2 anni.

**Metriche basate su log di Application Insights**. 
- Dietro la scena, [le metriche basate su log](../app/pre-aggregated-metrics-log-metrics.md) si traducono in query di log. La conservazione corrisponde alla conservazione degli eventi nei registri sottostanti. Per le risorse di Application Insights, i log vengono archiviati per 90 giorni.


> [!NOTE]
> È possibile [inviare metriche della piattaforma per](resource-logs-collect-storage.md) le risorse di Monitoraggio di Azure a un'area di lavoro di Log Analytics per tendenze a lungo termine.





## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulla piattaforma dati di Monitoraggio di Azure .Learn more about the [Azure Monitor data platform](data-platform.md).
- Informazioni sui dati di [log in Monitoraggio di Azure](data-platform-logs.md).
- Informazioni sui [dati di monitoraggio disponibili](data-sources.md) per diverse risorse in Azure.
