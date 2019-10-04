---
title: Metriche in monitoraggio di Azure | Microsoft Docs
description: Descrive le metriche in monitoraggio di Azure, che sono dati di monitoraggio leggeri in grado di supportare scenari quasi in tempo reale.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: e534754e46e6f2ad9b99b67d24d9f7da63a51a4f
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71258372"
---
# <a name="metrics-in-azure-monitor"></a>Metriche in Monitoraggio di Azure

> [!NOTE]
> La piattaforma dati di monitoraggio di Azure si basa su due tipi di dati fondamentali: Metriche e log. Questo articolo descrive le metriche. Vedere [log in monitoraggio di Azure](data-platform-logs.md) per una descrizione dettagliata dei log e della [piattaforma dati di monitoraggio di Azure](data-platform.md) per un confronto tra i due.

Le metriche in monitoraggio di Azure sono leggere e in grado di supportare scenari quasi in tempo reale, rendendoli particolarmente utili per gli avvisi e il rilevamento rapido dei problemi. Questo articolo descrive come sono strutturate le metriche, cosa è possibile fare con loro e identifica origini dati diverse che archiviano i dati nelle metriche.

## <a name="what-are-metrics"></a>Cosa sono le metriche?
Le metriche sono valori numerici che descrivono alcuni aspetti di un sistema in un determinato momento, Le metriche vengono raccolte a intervalli regolari e sono utili per gli avvisi perché possono essere campionate spesso e un avviso può essere generato rapidamente con una logica relativamente semplice.

## <a name="what-can-you-do-with-azure-monitor-metrics"></a>Che cosa è possibile fare con le metriche di monitoraggio di Azure?
La tabella seguente elenca i diversi modi in cui è possibile usare i dati delle metriche in monitoraggio di Azure.

|  |  |
|:---|:---|
| Analyze | Usare [Esplora metriche](metrics-charts.md) per analizzare le metriche raccolte in un grafico e confrontare le metriche da diverse risorse. |
| Visualizza | Aggiungere un grafico da Esplora metriche a un [dashboard di Azure](../learn/tutorial-app-dashboards.md).<br>Creare una [cartella di lavoro](../app/usage-workbooks.md) per combinare più set di dati in un report interattivo. Esportare i risultati di una query in [Grafana](grafana-plugin.md) per sfruttare il dashboard e combinarli con altre origini dati. |
| Avviso | Configurare una [regola di avviso](alerts-metric.md) per la metrica che invia una notifica o esegue un' [azione automatica](action-groups.md) quando il valore della metrica supera una soglia. |
| Automatizza |  Usare la [scalabilità](autoscale-overview.md) automatica per aumentare o diminuire le risorse in base a un valore della metrica che supera una soglia. |
| Esporta | [Instrada le metriche ai log](resource-logs-collect-storage.md) per analizzare i dati nelle metriche di monitoraggio di Azure insieme ai dati nei log di monitoraggio di Azure e archiviare i valori delle metriche per più di 93 giorni.<br>Trasmettere le metriche a un [Hub eventi](stream-monitoring-data-event-hubs.md) per indirizzarle a sistemi esterni. |
| Recupera | Accedere ai valori delle metriche da una riga di comando usando i [cmdlet di PowerShell](https://docs.microsoft.com/powershell/module/az.applicationinsights)<br>Accedere ai valori delle metriche dall'applicazione personalizzata usando l' [API REST](rest-api-walkthrough.md).<br>Accedere ai valori delle metriche dalla riga di comando usando l' [interfaccia](/cli/azure/monitor/metrics)della riga di comando. |
| Archivio | [Archiviare](..//learn/tutorial-archive-data.md) la cronologia relativa alle prestazioni o all'integrità della risorsa a scopi di conformità, verifica o creazione di report offline. |

## <a name="how-is-data-in-azure-monitor-metrics-structured"></a>Come sono strutturati i dati delle metriche di monitoraggio di Azure?
I dati raccolti dalle metriche di monitoraggio di Azure vengono archiviati in un database di serie temporali ottimizzato per l'analisi dei dati con timestamp. Ogni set di valori di metrica è una serie temporale con le proprietà seguenti:

* Ora di raccolta del valore
* Risorsa a cui è associato il valore
* Uno spazio dei nomi che funge da categoria per la metrica
* Nome della metrica
* Il valore stesso
* Alcune metriche possono avere più dimensioni, come descritto in [metriche multidimensionali](#multi-dimensional-metrics). Le metriche personalizzate possono avere fino a 10 dimensioni.

## <a name="multi-dimensional-metrics"></a>Metriche multidimensionali
Una delle difficoltà per i dati delle metriche è che spesso contiene informazioni limitate per fornire il contesto per i valori raccolti. Monitoraggio di Azure risolve questo problema con le metriche multidimensionali. Le dimensioni di una metrica sono coppie nome/valore contenenti dati aggiuntivi per descrivere il valore della metrica. Ad esempio, uno _spazio su disco disponibile_ per la metrica potrebbe avere una dimensione denominata _Drive_ con i valori _C:_ , _D:_ , che consentono di visualizzare lo spazio su disco disponibile in tutte le unità o per ogni singola unità.

L'esempio seguente mostra due set di dati per un'ipotetica metrica denominata _Velocità effettiva di rete_. Il primo set di dati non ha dimensioni. Il secondo mostra i valori con due dimensioni, _Indirizzo IP_ e _Direzione_:

### <a name="network-throughput"></a>Velocità effettiva rete

| Timestamp     | Valore della metrica |
| ------------- |:-------------|
| 8/9/2017 8:14 | 1\.331,8 Kbps |
| 8/9/2017 8:15 | 1\.141,4 Kbps |
| 8/9/2017 8:16 | 1\.110,2 Kbps |

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

## <a name="interacting-with-azure-monitor-metrics"></a>Interazione con le metriche di monitoraggio di Azure
Usare [Esplora metriche](metrics-charts.md) per analizzare in modo interattivo i dati nel database delle metriche e per tracciare i valori di più metriche nel tempo. È possibile aggiungere i grafici a un dashboard per visualizzarli con altre visualizzazioni. È anche possibile recuperare le metriche usando l'[API REST di monitoraggio di Azure](rest-api-walkthrough.md).

![Esplora metriche](media/data-platform/metrics-explorer.png)

## <a name="sources-of-azure-monitor-metrics"></a>Origini delle metriche di monitoraggio di Azure
Sono disponibili tre origini principali delle metriche raccolte da Monitoraggio di Azure. Una volta raccolte le metriche nel database delle metriche di monitoraggio di Azure, è possibile valutarle insieme indipendentemente dalla relativa origine.

Le **metriche della piattaforma** vengono create da risorse di Azure e offrono visibilità riguardo a integrità e prestazioni. Ogni tipo di risorsa crea un [set distinto di metriche](metrics-supported.md) senza che sia necessaria alcuna configurazione. Le metriche della piattaforma vengono raccolte dalle risorse di Azure con una frequenza di un minuto, a meno che non sia specificato diversamente nella definizione della metrica. 

Le **metriche del sistema operativo guest** vengono raccolte dal sistema operativo guest di una macchina virtuale. Abilitare le metriche del sistema operativo guest per le macchine virtuali Windows con l' [estensione diagnostica Windows (WAD)](../platform/diagnostics-extension-overview.md) e per le macchine virtuali Linux con [l'agente Telegraf InfluxData](https://www.influxdata.com/time-series-platform/telegraf/).

Le **metriche dell'applicazione** vengono create da Application Insights per le applicazioni monitorate e permettono di rilevare i problemi di prestazioni e tenere traccia delle tendenze relative all'uso dell'applicazione. Sono inclusi valori come il _tempo di risposta del server_ e le _eccezioni del browser_.

Le **metriche personalizzate** sono metriche definite in aggiunta alle metriche standard disponibili automaticamente. È possibile [definire metriche personalizzate nell'applicazione](../app/api-custom-events-metrics.md) monitorata da Application Insights o creare metriche personalizzate per un servizio di Azure usando l' [API metrica personalizzata](metrics-store-custom-rest-api.md).

## <a name="retention-of-metrics"></a>Conservazione delle metriche
Per la maggior parte delle risorse in Azure, le metriche vengono archiviate per 93 giorni. Esistono alcune eccezioni:

**Metriche del sistema operativo guest**
-   **Metriche del sistema operativo guest classiche**. Si tratta di contatori delle prestazioni raccolti dall' [estensione diagnostica Windows (WAD)](../platform/diagnostics-extension-overview.md) o dall' [estensione diagnostica di Linux (LAD)](../../virtual-machines/extensions/diagnostics-linux.md) e indirizzati a un account di archiviazione di Azure. La conservazione per queste metriche è di 14 giorni.
-   **Metriche del sistema operativo guest inviate alle metriche di monitoraggio di Azure**. Si tratta di contatori delle prestazioni raccolti dall'estensione diagnostica Windows (WAD) e inviati al [sink di monitoraggio di Azure](diagnostics-extension-overview.md#data-storage)o tramite l' [agente Telegraf InfluxData](https://www.influxdata.com/time-series-platform/telegraf/) nei computer Linux. La conservazione per queste metriche è di 93 giorni.
-   **Metriche del sistema operativo guest raccolte da log Analytics Agent**. Si tratta di contatori delle prestazioni raccolti dall'agente Log Analytics e inviati a un'area di lavoro di Log Analytics. La conservazione per queste metriche è di 31 giorni e può essere estesa fino a 2 anni.

**Application Insights metriche basate su log**. 
- Dietro le quinte, le [metriche basate su log](../app/pre-aggregated-metrics-log-metrics.md) vengono convertite in query di log. La conservazione corrisponde alla conservazione degli eventi nei log sottostanti. Per Application Insights risorse, i log vengono archiviati per 90 giorni.


> [!NOTE]
> È possibile [inviare le metriche della piattaforma per le risorse di monitoraggio di Azure a un'area di lavoro log Analytics per la](resource-logs-collect-storage.md) tendenza a lungo termine.





## <a name="next-steps"></a>Passaggi successivi

- Scopri di più sulla [piattaforma dati di monitoraggio di Azure](data-platform.md).
- Informazioni sui [dati di log in monitoraggio di Azure](data-platform-logs.md).
- Informazioni sui [dati di monitoraggio disponibili](data-sources.md) per diverse risorse in Azure.
