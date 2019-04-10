---
title: Le metriche in Monitoraggio di Azure | Microsoft Docs
description: Descrive le metriche in Monitoraggio di Azure che sono elementi leggeri in grado di supportare scenari in tempo reale quasi i dati di monitoraggio.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 2646941e2384acf6d303615f564b65d616931180
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2019
ms.locfileid: "59358881"
---
# <a name="metrics-in-azure-monitor"></a>Metriche in Monitoraggio di Azure

> [!NOTE]
> La piattaforma di dati di monitoraggio di Azure si basa su due tipi di dati fondamentali: Metriche e log. Questo articolo descrive le metriche. Fare riferimento a [log in Monitoraggio di Azure](data-platform-logs.md) per una descrizione dettagliata dei log e di ottenere [platforn i dati di monitoraggio di Azure](data-platform.md) per un confronto tra i due.


Le metriche in Monitoraggio di Azure sono leggeri e in grado di supportare scenari in tempo reale che lo rendono particolarmente utile per quasi gli avviso e veloce rilevamento dei problemi. Questo articolo descrive come sono strutturate le metriche, operazioni eseguibili con loro e identifica le origini dati diverse che archiviano i dati nelle metriche.

## <a name="what-are-metrics"></a>Cosa sono le metriche?
Le metriche sono valori numerici che descrivono alcuni aspetti di un sistema in un determinato momento, Le metriche vengono raccolte a intervalli regolari e sono utili per gli avvisi in quanto possono essere campionate frequentemente e un avviso può essere generato rapidamente con una logica relativamente semplice.

## <a name="what-can-you-do-with-azure-monitor-metrics"></a>Cosa si può fare con le metriche di monitoraggio di Azure?
La tabella seguente elenca i diversi modi, che è possibile usare i dati delle metriche in Monitoraggio di Azure.

|  |  |
|:---|:---|
| Analizzare | Uso [Esplora metriche](metrics-charts.md) per analizzare le metriche raccolte in un grafico e confrontare le metriche da diverse risorse. |
| Visualizzazione | Aggiungere un grafico da Esplora metriche per un [dashboard Azure](../learn/tutorial-app-dashboards.md).<br>Creare un [cartella di lavoro](../app/usage-workbooks.md) da combinare con più set di dati in un report interattivo. Esportare i risultati di una query sul [Grafana](grafana-plugin.md) per sfruttare la creazione di dashboard e si combina con altre origini dati. |
| Avviso | Configurare un [regola di avviso metrica](alerts-metric.md) che invia una notifica o esegue [azione automatica](action-groups.md) quando il valore della metrica supera una soglia. |
| Automatizzare |  Uso [scalabilità automatica](autoscale-overview.md) per aumentare o ridurre le risorse in base a un valore della metrica che supera una soglia. |
| Esportazione | [Indirizzare le metriche a log](diagnostic-logs-stream-log-store.md) per analizzare i dati in metriche di monitoraggio di Azure insieme ai dati nei log di monitoraggio di Azure e per archiviare i valori delle metriche per più di 93 giorni.<br>Stream le metriche a un [Hub eventi](stream-monitoring-data-event-hubs.md) reindirizzarli verso sistemi esterni. |
| Recupero | Accedere ai valori delle metriche da una riga di comando usando [i cmdlet di PowerShell](https://docs.microsoft.com/powershell/module/az.applicationinsights)<br>Accedere ai valori delle metriche dall'uso dell'applicazione personalizzata [API REST](rest-api-walkthrough.md).<br>Accedere ai valori delle metriche da una riga di comando usando [CLI](/cli/azure/monitor/metrics). |
| Archiviazione | [Archiviare](..//learn/tutorial-archive-data.md) la cronologia relativa alle prestazioni o all'integrità della risorsa a scopi di conformità, verifica o creazione di report offline. |


## <a name="how-is-data-in-azure-monitor-metrics-structured"></a>Quali sono i dati in metriche di monitoraggio di Azure strutturata?
Dati raccolti dalle metriche di monitoraggio di Azure vengono archiviati in un database di serie temporali che è ottimizzato per l'analisi dei dati con data / ora. Ogni set di valori della metrica è una serie temporale con le proprietà seguenti:

* L'ora che il valore raccolto
* Il valore della risorsa è associata
* Uno spazio dei nomi che agisce come una categoria per la metrica
* Un nome di metrica
* Il valore stesso
* Alcune metriche possono avere più dimensioni, come descritto in [metriche multidimensionali](#multi-dimensional-metrics). Le metriche personalizzate possono avere fino a 10 dimensioni.

Le metriche in Azure vengono archiviate per 93 giorni. È possibile [inviare le metriche di piattaforma per le risorse di monitoraggio di Azure a un'area di lavoro di Log Analitica](diagnostic-logs-stream-log-store.md) per l'analisi delle tendenze a lungo termine.

## <a name="multi-dimensional-metrics"></a>Metriche multidimensionali
Una delle sfide per i dati di metrica è che spesso contiene informazioni limitate per fornire un contesto per i valori raccolti. Monitoraggio di Azure affronta questa sfida con le metriche multidimensionali. Le dimensioni di una metrica sono coppie nome/valore contenenti dati aggiuntivi per descrivere il valore della metrica. Ad esempio, una metrica _lo spazio su disco disponibile_ potrebbe avere una dimensione denominata _unità_ con i valori _c:_, _unità d:_, che consente la visualizzazione l'unità di spazio disponibile su disco in tutti o per ogni unità singolarmente.

L'esempio seguente mostra due set di dati per un'ipotetica metrica denominata _Velocità effettiva di rete_. Il primo set di dati non ha dimensioni. Il secondo mostra i valori con due dimensioni, _Indirizzo IP_ e _Direzione_:

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

## <a name="interacting-with-azure-monitor-metrics"></a>L'interazione con le metriche di monitoraggio di Azure
Uso [Esplora metriche](metrics-charts.md) per analizzare i dati nel database delle metriche e di grafico i valori di più metriche nel tempo in modo interattivo. È possibile aggiungere i grafici a un dashboard per visualizzarli con altre visualizzazioni. È anche possibile recuperare le metriche usando l'[API REST di monitoraggio di Azure](rest-api-walkthrough.md).

![Esplora metriche](media/data-platform/metrics-explorer.png)

## <a name="sources-of-azure-monitor-metrics"></a>Origini delle metriche di monitoraggio di Azure
Sono disponibili tre origini principali delle metriche raccolte da Monitoraggio di Azure. Dopo che queste metriche vengono raccolti nel database delle metriche di monitoraggio di Azure, essi possono essere valutati contemporaneamente indipendentemente dalla relativa origine.

Le **metriche della piattaforma** vengono create da risorse di Azure e offrono visibilità riguardo a integrità e prestazioni. Ogni tipo di risorsa crea un [set distinto di metriche](metrics-supported.md) senza che sia necessaria alcuna configurazione. Piattaforma metriche vengono raccolte dalle risorse di Azure con frequenza di un minuto se non diversamente specificato nella definizione della metrica. 

**Metriche del sistema operativo guest** vengono raccolti dal sistema operativo guest di una macchina virtuale. Abilitare le metriche del sistema operativo guest per macchine virtuali Windows con [estensione di diagnostica Windows (WAD)](../platform/diagnostics-extension-overview.md) e per le macchine virtuali Linux con [InfluxData Telegraf agente](https://www.influxdata.com/time-series-platform/telegraf/).

Le **metriche dell'applicazione** vengono create da Application Insights per le applicazioni monitorate e permettono di rilevare i problemi di prestazioni e tenere traccia delle tendenze relative all'uso dell'applicazione. Sono inclusi valori come il _tempo di risposta del server_ e le _eccezioni del browser_.

**Metriche personalizzate** sono le metriche che definiscono oltre alle metriche standard che sono automaticamente disponibili. È possibile [definiscono metriche personalizzate nell'applicazione](../app/api-custom-events-metrics.md) che viene monitorato da Application Insights o creare metriche personalizzate per un servizio di Azure usando la [metriche personalizzate API](metrics-store-custom-rest-api.md).


## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sul [piattaforma dati di monitoraggio di Azure](data-platform.md).
- Scopri [registrare i dati in Monitoraggio di Azure](data-platform-logs.md).
- Informazioni sui [dati di monitoraggio disponibili](data-sources.md) per diverse risorse in Azure.
