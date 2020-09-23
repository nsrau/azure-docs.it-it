---
title: Monitoraggio di Azure per le reti (anteprima)
description: Una rapida panoramica di monitoraggio di Azure per la rete che fornisce una visualizzazione completa dell'integrità e delle metriche per tutte le risorse di rete distribuite senza alcuna configurazione.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/22/2020
ms.openlocfilehash: 88e69ac4156bb2bdfb217a4fc83ac2d798d08211
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90987927"
---
# <a name="azure-monitor-for-networks-preview"></a>Monitoraggio di Azure per le reti (anteprima)
Monitoraggio di Azure per la rete offre una panoramica completa dell' [integrità](https://docs.microsoft.com/azure/service-health/resource-health-checks-resource-types) e delle [metriche](../platform/metrics-supported.md) per tutte le risorse di rete distribuite senza alcuna configurazione.  Consente inoltre di accedere a tutte le funzionalità di monitoraggio della rete, ad esempio il [monitoraggio della connessione](../../network-watcher/connection-monitor-preview.md), [la registrazione dei flussi per i gruppi di sicurezza di rete (gruppi)](../../network-watcher/network-watcher-nsg-flow-logging-overview.md), [analisi del traffico](../../network-watcher/traffic-analytics.md)e altre funzionalità di [diagnostica](../../network-watcher/network-watcher-monitoring-overview.md#diagnostics) di rete.

Monitoraggio di Azure per le reti è strutturato attorno ai componenti chiave di monitoraggio seguenti:
- [Metriche e integrità della rete](#networkhealth)
- [Connettività](#connectivity)
- [Traffico](#traffic)
- [Toolkit di diagnostica](#diagnostictoolkit)

## <a name="network-health-and-metrics"></a><a name="networkhealth"></a>Metriche e integrità della rete

La pagina **Panoramica** di monitoraggio di Azure per le reti fornisce un modo semplice per visualizzare l'inventario delle risorse di rete insieme all'integrità delle risorse e agli avvisi. È divisa in quattro aree funzionali principali: ricerca e filtro, Integrità risorse e metriche, avvisi. e vista dipendenze

![Pagina di panoramica](media/network-insights-overview/overview.png)

### <a name="search-and-filtering"></a>Ricerca e filtro
La vista integrità risorse e avvisi può essere personalizzata usando filtri come la **sottoscrizione**, il **gruppo di risorse** e il tipo di **risorsa**. Nella casella di ricerca è disponibile la funzionalità per la ricerca nelle proprietà delle risorse.

La casella di ricerca può essere usata per cercare le risorse e le risorse associate. Un indirizzo IP pubblico, ad esempio, è associato a un gateway applicazione. La ricerca del nome DNS degli indirizzi IP pubblici identificherà sia l'IP pubblico che il gateway applicazione associato.

![Monitoraggio di Azure per le reti-ricerca](media/network-insights-overview/search.png)


### <a name="resource-health-and-metric"></a>Integrità risorse e metrica
Ogni riquadro rappresenta un tipo di risorsa con il numero di istanze distribuite in tutte le sottoscrizioni selezionate insieme allo stato di integrità delle risorse. Nell'esempio seguente sono state distribuite 45 ER e connessioni VPN, 44 sono integre e 1 non disponibile.

![Monitoraggio di Azure per reti-integrità risorse](media/network-insights-overview/resource-health.png)

Facendo clic sull'opzione non disponibile per le connessioni VPN, viene avviata una visualizzazione metrica. 

![Monitoraggio di Azure per reti-visualizzazione metrica](media/network-insights-overview/metric-view.png)

È possibile fare clic su ogni elemento nella visualizzazione griglia. Fare clic sull'icona integrità per reindirizzare a integrità risorse per tale connessione. Fare clic su avvisi per reindirizzare alla pagina avvisi e metriche rispettivamente per tale connessione. 

### <a name="alerts"></a>Avvisi
La griglia degli **avvisi** a destra fornisce una visualizzazione di tutti gli avvisi generati per le risorse selezionate tra tutte le sottoscrizioni. Fare clic sui conteggi degli avvisi per passare alla pagina avvisi dettagliati.

### <a name="dependency-view"></a>Visualizzazione dipendenze
La visualizzazione delle **dipendenze** consente di visualizzare il modo in cui la risorsa è configurata. Attualmente la visualizzazione dipendenze è supportata per il gateway applicazione, la rete WAN virtuale e la Load Balancer. Ad esempio, nel caso del gateway applicazione, è possibile accedere alla visualizzazione dipendenze facendo clic sul nome della risorsa del gateway applicazione nella visualizzazione griglia metriche. Questo vale anche per la rete WAN virtuale e la Load Balancer.

![Monitoraggio di Azure per reti-visualizzazione del gateway applicazione](media/network-insights-overview/application-gateway.png)

La visualizzazione delle **dipendenze** per il gateway applicazione offre una visualizzazione semplificata del modo in cui gli indirizzi IP front-end sono connessi ai listener, alle regole e al pool back-end. I bordi di connessione sono codificati a colori e forniscono dettagli aggiuntivi in base all'integrità del pool back-end. La vista fornisce anche una visualizzazione dettagliata delle metriche e delle metriche del gateway applicazione per tutti i pool back-end correlati, ad esempio il set di scalabilità di macchine virtuali e le istanze di VM.

![Monitoraggio di Azure per le reti-visualizzazione dipendenze](media/network-insights-overview/dependency-view.png)

Il grafico dipendenze consente di spostarsi agevolmente sulle impostazioni di configurazione. Fare clic con il pulsante destro del mouse su un pool back-end per accedere ad altre funzionalità. Ad esempio, se il pool back-end è una macchina virtuale, è possibile accedere direttamente a VM Insights e Network Watcher risoluzione dei problemi di connessione per identificare i problemi di connettività.

![Monitoraggio di Azure per reti-menu visualizzazione dipendenze](media/network-insights-overview/dependency-view-menu.png)

La barra di ricerca e di filtro nella visualizzazione dipendenze offre un modo semplice per eseguire ricerche nel grafico. Ad esempio, la ricerca di *AppGWTestRule* nell'esempio seguente consente di limitare la visualizzazione grafica a tutti i nodi connessi tramite *AppGWTestRule*.

![Monitoraggio di Azure per reti-esempio di ricerca](media/network-insights-overview/search-example.png)

Filtri diversi consentono di restringere a un percorso e a uno stato specifici. Ad esempio, selezionare solo non *integro* dall'elenco a discesa **stato integrità** per visualizzare tutti i bordi in cui lo stato è non *integro*.

Fare clic su **visualizzazione metrica dettagliata** per avviare una cartella di lavoro preconfigurata con metriche dettagliate per il gateway applicazione, tutte le risorse del pool back-end e gli indirizzi IP front-end. 

## <a name="connectivity"></a><a name="connectivity"></a>Connettività

La scheda **connettività** consente di visualizzare in modo semplice tutti i test configurati utilizzando monitoraggio connessione e [monitoraggio connessione (anteprima)](../../network-watcher/connection-monitor-preview.md) per il set di sottoscrizioni selezionato.

![Scheda connettività in monitoraggio di Azure per le reti](media/network-insights-overview/azure-monitor-for-networks-connectivity-tab.png)

I test sono raggruppati in base alle riquadri origini e destinazioni e visualizzano lo stato di raggiungibilità per ogni test. Le impostazioni raggiungibili forniscono un accesso facile per configurare i criteri di raggiungibilità in base ai controlli non riusciti (%) e RTT (MS). Una volta impostati i valori, lo stato di ogni test viene aggiornato in base ai criteri di selezione.

![Test di connettività in monitoraggio di Azure per le reti](media/network-insights-overview/azure-monitor-for-networks-connectivity-tests.png)

Quando si fa clic su un riquadro di origine o di destinazione viene avviata una visualizzazione metrica.

![Metriche di connettività in monitoraggio di Azure per le reti](media/network-insights-overview/azure-monitor-for-networks-connectivity-metrics.png)


È possibile fare clic su ogni elemento nella visualizzazione griglia. Fare clic sull'icona **raggiungibilità** per reindirizzare alla pagina del portale di **monitoraggio della connessione** per visualizzare la topologia hop per hop e la connettività che influisca sui problemi identificati. Fare clic su **avvisi** per reindirizzare gli avvisi e **controllare la percentuale di tempo di andata e ritorno** per il reindirizzamento alla pagina metrica per il monitoraggio della connessione selezionato.

La griglia degli **avvisi**   a destra fornisce una visualizzazione di tutti gli avvisi generati per i test di connettività configurati in tutte le sottoscrizioni. Fare clic sui conteggi degli avvisi per passare alla pagina avvisi dettagliati.

## <a name="traffic"></a><a name="traffic"></a>Traffico
Scheda traffico consente di accedere a tutti i gruppi configurati per i [log di flusso di NSG](../../network-watcher/network-watcher-nsg-flow-logging-overview.md) e [analisi del traffico](../../network-watcher/traffic-analytics.md) per il set selezionato di sottoscrizioni e raggruppati per località. La funzionalità di ricerca fornita in questa scheda consente di identificare il gruppi configurato per l'indirizzo IP cercato. È possibile cercare qualsiasi indirizzo IP nell'ambiente e la visualizzazione a livello di area affiancata visualizzerà tutti gruppi insieme ai log dei flussi NSG e allo stato di configurazione di analisi del traffico.

![Visualizzazione traffico in monitoraggio di Azure per le reti](media/network-insights-overview/azure-monitor-for-networks-traffic-view.png)

Facendo clic su un riquadro qualsiasi area viene avviata una visualizzazione griglia che consente di visualizzare e configurare facilmente i log di flusso NSG e Analisi del traffico.  

![Visualizzazione dell'area di traffico in monitoraggio di Azure per le reti](media/network-insights-overview/azure-monitor-for-networks-traffic-region-view.png)

È possibile fare clic su ogni elemento nella visualizzazione griglia. Fare clic su stato configurazione per modificare il log del flusso di NSG e la configurazione Analisi del traffico. Fare clic su avvisi per reindirizzare gli avvisi di traffico configurati per il NSG selezionato. Analogamente, è possibile passare alla visualizzazione Analisi del traffico facendo clic sull'area di lavoro.  

La griglia degli **avvisi**   a destra fornisce una visualizzazione di tutti gli avvisi basati su analisi del traffico area di lavoro in tutte le sottoscrizioni. Fare clic sui conteggi degli avvisi per passare alla pagina avvisi dettagliati.

## <a name="diagnostic-toolkit"></a><a name="diagnostictoolkit"></a> Toolkit di diagnostica
Il Toolkit di diagnostica fornisce l'accesso a tutte le funzionalità di diagnostica disponibili per la risoluzione dei problemi di rete. Da questo elenco a discesa è possibile accedere a funzionalità quali l' [acquisizione di pacchetti](../../network-watcher/network-watcher-packet-capture-overview.md), la [risoluzione dei problemi relativi alla VPN](../../network-watcher/network-watcher-troubleshoot-overview.md), la [risoluzione dei problemi relativi alla connessione](../../network-watcher/network-watcher-connectivity-overview.md), la verifica del [flusso IP](../../network-watcher/network-watcher-ip-flow-verify-overview.md) [e](../../network-watcher/network-watcher-next-hop-overview.md)

![Scheda del Toolkit di diagnostica](media/network-insights-overview/azure-monitor-for-networks-diagnostic-toolkit.png)

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sul monitoraggio della rete, vedere informazioni su [Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md).
