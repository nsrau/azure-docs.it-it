---
title: Monitoraggio di Azure per le reti
description: Panoramica di monitoraggio di Azure per le reti, che offre una visualizzazione completa dell'integrità e delle metriche per tutte le risorse di rete distribuite senza alcuna configurazione.
ms.subservice: ''
ms.topic: conceptual
author: KumudD
ms.author: kumud
ms.date: 11/25/2020
ms.openlocfilehash: 2f262b328efcc341c93f1e5c31cab8cbd3ecbf6b
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184638"
---
# <a name="azure-monitor-for-networks"></a>Monitoraggio di Azure per le reti
Monitoraggio di Azure per le reti offre una panoramica completa dell' [integrità](../../service-health/resource-health-checks-resource-types.md) e delle [metriche](../platform/metrics-supported.md) per tutte le risorse di rete distribuite, senza richiedere alcuna configurazione. Fornisce inoltre l'accesso alle funzionalità di monitoraggio della rete, come il [monitoraggio della connessione](../../network-watcher/connection-monitor-preview.md), [la registrazione dei flussi per i gruppi di sicurezza di rete (gruppi)](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)e [analisi del traffico](../../network-watcher/traffic-analytics.md). E fornisce altre funzionalità di [diagnostica](../../network-watcher/network-watcher-monitoring-overview.md#diagnostics) di rete.

Il monitoraggio di Azure per le reti è strutturato attorno a questi componenti chiave del monitoraggio:
- [Metriche e integrità della rete](#networkhealth)
- [Connettività](#connectivity)
- [Traffico](#traffic)
- [Toolkit di diagnostica](#diagnostictoolkit)

## <a name="network-health-and-metrics"></a><a name="networkhealth"></a>Metriche e integrità della rete

La pagina **Panoramica** di monitoraggio di Azure per le reti fornisce un modo semplice per visualizzare l'inventario delle risorse di rete, insieme all'integrità delle risorse e agli avvisi. È divisa in quattro aree funzionali principali: ricerca e filtro, integrità delle risorse e metriche, avvisi e visualizzazione dipendenze.

[ ![ Screenshot che mostra la pagina Panoramica](media/network-insights-overview/overview.png)](media/network-insights-overview/overview.png)#lightbox)

### <a name="search-and-filtering"></a>Ricerca e filtro
È possibile personalizzare la vista integrità risorse e avvisi usando filtri quali **sottoscrizione**, **gruppo di risorse** e **tipo**.

È possibile usare la casella di ricerca per cercare le risorse e le risorse associate. Un indirizzo IP pubblico, ad esempio, è associato a un gateway applicazione. Una ricerca del nome DNS dell'indirizzo IP pubblico restituirà sia l'IP pubblico che il gateway applicazione associato:

[![Screenshot che mostra il monitoraggio di Azure per le reti risultati della ricerca.](media/network-insights-overview/search.png)](media/network-insights-overview/search.png#lightbox)


### <a name="resource-health-and-metrics"></a>Integrità delle risorse e metriche
Nell'esempio seguente ogni sezione rappresenta un tipo di risorsa. Il riquadro Visualizza il numero di istanze di quel tipo di risorsa distribuito in tutte le sottoscrizioni selezionate. Viene inoltre visualizzato lo stato di integrità della risorsa. In questo esempio sono state distribuite 105 connessioni ER e VPN. 103 sono integri e 2 non sono disponibili.

![Screenshot che mostra l'integrità delle risorse e le metriche in monitoraggio di Azure per le reti.](media/network-insights-overview/resource-health.png)

Se si selezionano le connessioni ER e VPN non disponibili, verrà visualizzata una visualizzazione metrica: 

![Screenshot che mostra la visualizzazione delle metriche in monitoraggio di Azure per le reti.](media/network-insights-overview/metric-view.png)

È possibile selezionare qualsiasi elemento nella visualizzazione griglia. Selezionare l'icona nella colonna **stato** per ottenere integrità risorse per tale connessione. Selezionare il valore nella colonna **avviso** per passare alla pagina avvisi e metriche per la connessione. 

### <a name="alerts"></a>Avvisi
La casella **avvisi** sul lato destro della pagina fornisce una visualizzazione di tutti gli avvisi generati per le risorse selezionate in tutte le sottoscrizioni. Selezionare i conteggi degli avvisi per passare a una pagina di avvisi dettagliati.

### <a name="dependency-view"></a>Visualizzazione dipendenze
La visualizzazione delle dipendenze consente di visualizzare la configurazione di una risorsa. La visualizzazione delle dipendenze è attualmente disponibile per applicazione Azure gateway, una rete WAN virtuale di Azure e Azure Load Balancer. Per il gateway applicazione, ad esempio, è possibile accedere alla visualizzazione dipendenze selezionando il nome della risorsa del gateway applicazione nella visualizzazione griglia metriche. È possibile eseguire la stessa operazione per la rete WAN virtuale e la Load Balancer.

![Sreenshot che mostra la visualizzazione del gateway applicazione in monitoraggio di Azure per le reti.](media/network-insights-overview/application-gateway.png)

La visualizzazione delle dipendenze per il gateway applicazione offre una visualizzazione semplificata del modo in cui gli indirizzi IP front-end sono connessi ai listener, alle regole e al pool back-end. Le linee di connessione sono codificate a colori e forniscono dettagli aggiuntivi in base all'integrità del pool back-end. La vista fornisce anche una visualizzazione dettagliata delle metriche e delle metriche del gateway applicazione per tutti i pool back-end correlati, ad esempio il set di scalabilità di macchine virtuali e le istanze di VM.

[![Screenshot che mostra la visualizzazione delle dipendenze in monitoraggio di Azure per le reti.](media/network-insights-overview/dependency-view.png)](media/network-insights-overview/dependency-view.png#lightbox)

Il grafico dipendenze consente di spostarsi agevolmente sulle impostazioni di configurazione. Per accedere ad altre informazioni, fare clic con il pulsante destro del mouse su un pool Ad esempio, se il pool back-end è una macchina virtuale, è possibile accedere direttamente a VM Insights e alla risoluzione dei problemi di connessione di Azure Network Watcher per identificare i problemi di connettività:

![Screenshot che mostra il menu visualizzazione dipendenze in monitoraggio di Azure per le reti.](media/network-insights-overview/dependency-view-menu.png)

La barra di ricerca e filtro nella visualizzazione dipendenze fornisce un modo semplice per eseguire ricerche nel grafico. Se, ad esempio, si cerca **AppGWTestRule** nell'esempio precedente, la vista viene ridotta a tutti i nodi connessi tramite AppGWTestRule:

![Screenshot che mostra un esempio di ricerca in monitoraggio di Azure per le reti.](media/network-insights-overview/search-example.png)

Diversi filtri consentono di ridurre a un percorso e a uno stato specifici. Selezionare, ad esempio, solo non **integro** nell'elenco **stato integrità** per visualizzare tutti i bordi per i quali lo stato non è integro.

Selezionare **Visualizza metriche dettagliate** per aprire una cartella di lavoro preconfigurata che fornisce metriche dettagliate per il gateway applicazione, tutte le risorse del pool back-end e gli IP front-end. 

## <a name="connectivity"></a><a name="connectivity"></a>Connettività

La scheda **connettività** fornisce un modo semplice per visualizzare tutti i test configurati tramite [monitoraggio connessione](../../network-watcher/connection-monitor-overview.md) e monitoraggio connessione (classico) per il set di sottoscrizioni selezionato.

![Screenshot che mostra la scheda connettività in monitoraggio di Azure per le reti.](media/network-insights-overview/azure-monitor-for-networks-connectivity-tab.png)

I test sono raggruppati in base alle riquadri **origini** e **destinazioni** e visualizzano lo stato di raggiungibilità per ogni test. Le impostazioni raggiungibili consentono di accedere facilmente alle configurazioni per i criteri di raggiungibilità, in base ai controlli non riusciti (%) e RTT (MS). Dopo aver impostato i valori, lo stato di ogni test viene aggiornato in base ai criteri di selezione.

[![Screenshot che mostra i test di connettività in monitoraggio di Azure per le reti.](media/network-insights-overview/azure-monitor-for-networks-connectivity-tests.png)](media/network-insights-overview/azure-monitor-for-networks-connectivity-tests.png#lightbox)

È possibile selezionare qualsiasi riquadro di origine o di destinazione per aprire una visualizzazione metrica:

[![Screenshot che mostra le metriche di connettività in monitoraggio di Azure per le reti.](media/network-insights-overview/azure-monitor-for-networks-connectivity-metrics.png)](media/network-insights-overview/azure-monitor-for-networks-connectivity-metrics.png#lightbox)


È possibile selezionare qualsiasi elemento nella visualizzazione griglia. Selezionare l'icona nella colonna **raggiungibilità** per passare alla pagina del portale di monitoraggio della connessione e visualizzare la topologia hop-by-hop e la connettività che interessano i problemi identificati. Selezionare il valore nella colonna **avviso** per passare ad avvisi. Selezionare i grafici nelle colonne **Verifica percentuale non riuscita** e **tempo di round trip (MS)** per passare alla pagina metriche per il monitoraggio della connessione selezionato.

La casella **avvisi** sul lato destro della pagina fornisce una visualizzazione di tutti gli avvisi generati per i test di connettività configurati in tutte le sottoscrizioni. Selezionare i conteggi degli avvisi per passare a una pagina di avvisi dettagliati.

## <a name="traffic"></a><a name="traffic"></a>Traffico
La scheda **traffico** fornisce l'accesso a tutti i gruppi configurati per i [log dei flussi NSG](../../network-watcher/network-watcher-nsg-flow-logging-overview.md) e [analisi del traffico](../../network-watcher/traffic-analytics.md) per il set selezionato di sottoscrizioni, raggruppate per località. La funzionalità di ricerca fornita in questa scheda consente di identificare il gruppi configurato per l'indirizzo IP cercato. È possibile cercare qualsiasi indirizzo IP nell'ambiente in uso. La visualizzazione a livello di area affiancata visualizzerà tutti gruppi insieme ai log di flusso NSG e Analisi del traffico lo stato della configurazione.

[![Screenshot che mostra la scheda traffico in monitoraggio di Azure per le reti.](media/network-insights-overview/azure-monitor-for-networks-traffic-view.png)](media/network-insights-overview/azure-monitor-for-networks-traffic-view.png#lightbox)

Se si seleziona un riquadro dell'area, viene visualizzata una visualizzazione griglia. La griglia fornisce i log di flusso NSG e Analisi del traffico in una vista facile da leggere e configurare:  

[![Screenshot che mostra la visualizzazione dell'area di traffico in monitoraggio di Azure per le reti.](media/network-insights-overview/azure-monitor-for-networks-traffic-region-view.png)](media/network-insights-overview/azure-monitor-for-networks-traffic-region-view.png#lightbox)

È possibile selezionare qualsiasi elemento nella visualizzazione griglia. Selezionare l'icona nella colonna **stato configurazione Flowlog** per modificare il log del flusso di NSG e analisi del traffico la configurazione. Selezionare il valore nella colonna **avviso** per passare agli avvisi di traffico configurati per il NSG selezionato. Analogamente, è possibile passare alla visualizzazione Analisi del traffico selezionando l' **area di lavoro analisi del traffico**.  

La casella **avvisi** sul lato destro della pagina fornisce una visualizzazione di tutti gli avvisi analisi del traffico basati sull'area di lavoro in tutte le sottoscrizioni. Selezionare i conteggi degli avvisi per passare a una pagina di avvisi dettagliati.

## <a name="diagnostic-toolkit"></a><a name="diagnostictoolkit"></a> Toolkit di diagnostica
Il Toolkit di diagnostica fornisce l'accesso a tutte le funzionalità di diagnostica disponibili per la risoluzione dei problemi di rete. È possibile usare questo elenco a discesa per accedere a funzionalità quali l' [acquisizione di pacchetti](../../network-watcher/network-watcher-packet-capture-overview.md), la [risoluzione dei problemi della VPN](../../network-watcher/network-watcher-troubleshoot-overview.md), la [risoluzione dei problemi di connessione](../../network-watcher/network-watcher-connectivity-overview.md), l' [hop successivo](../../network-watcher/network-watcher-next-hop-overview.md)e la [Verifica del flusso IP](../../network-watcher/network-watcher-ip-flow-verify-overview.md):

![Screenshot che mostra la scheda del Toolkit di diagnostica.](media/network-insights-overview/azure-monitor-for-networks-diagnostic-toolkit.png)

## <a name="troubleshooting"></a>Risoluzione dei problemi 

Per indicazioni generali sulla risoluzione dei problemi, vedere l'articolo dedicato alla [risoluzione dei](troubleshoot-workbooks.md)problemi di Insights basato sulla cartella di lavoro.

Questa sezione consente di diagnosticare e risolvere alcuni problemi comuni che possono verificarsi quando si usa monitoraggio di Azure per le reti. 

### <a name="how-do-i-resolve-performance-problems-or-failures"></a>Ricerca per categorie risolvere problemi di prestazioni o errori?

Per informazioni sulla risoluzione dei problemi correlati alla rete identificati con monitoraggio di Azure per le reti, vedere la documentazione relativa alla risoluzione dei problemi per la risorsa che non funziona correttamente. 

Di seguito sono riportati alcuni collegamenti ad articoli per la risoluzione dei problemi relativi ai servizi usati di frequente. Per ulteriori informazioni sulla risoluzione dei problemi relativi a questi servizi, vedere gli altri articoli nella sezione risoluzione dei problemi del sommario per il servizio.
* [Rete virtuale di Azure](../../virtual-network/virtual-network-troubleshoot-peering-issues.md)
* [Gateway applicazione di Azure](../../application-gateway/create-gateway-internal-load-balancer-app-service-environment.md)
* [Gateway VPN di Azure](../../vpn-gateway/vpn-gateway-troubleshoot.md)
* [Azure ExpressRoute](../../expressroute/expressroute-troubleshooting-expressroute-overview.md) 
* [Azure Load Balancer](../../load-balancer/load-balancer-troubleshoot.md) 

### <a name="why-dont-i-see-the-resources-for-all-the-subscriptions-ive-selected"></a>Perché non vengono visualizzate le risorse per tutte le sottoscrizioni selezionate?

Network Insights può visualizzare le risorse solo per cinque sottoscrizioni alla volta. 

### <a name="how-do-i-make-changes-or-add-visualizations-to-network-insights"></a>Ricerca per categorie apportare modifiche o aggiungere visualizzazioni a Network Insights?

Per apportare modifiche, selezionare **modifica modalità** per modificare la cartella di lavoro. È quindi possibile salvare le modifiche come una nuova cartella di lavoro associata a una sottoscrizione designata e a un gruppo di risorse.

### <a name="whats-the-time-grain-after-i-pin-any-part-of-the-workbooks"></a>Qual è il tempo di granularità dopo l'aggiunta di qualsiasi parte delle cartelle di lavoro?

Network Insights usa il tempo di granularità **automatico** , quindi l'intervallo di tempo è basato sull'intervallo di tempo selezionato.

### <a name="whats-the-time-range-when-any-part-of-a-workbook-is-pinned"></a>Qual è l'intervallo di tempo in cui una parte di una cartella di lavoro è bloccata?

L'intervallo di tempo dipende dalle impostazioni del dashboard.

### <a name="what-if-i-want-to-see-other-data-or-make-my-own-visualizations-how-can-i-make-changes-to-network-insights"></a>È possibile vedere altri dati o creare visualizzazioni personali? Come è possibile apportare modifiche a Network Insights?

È possibile modificare la cartella di lavoro visualizzata in qualsiasi pannello laterale o in una visualizzazione dettagliata della metrica usando la modalità di modifica. È quindi possibile salvare le modifiche come una nuova cartella di lavoro.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sul monitoraggio della rete: informazioni su [Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md)
- Informazioni sulle cartelle di lavoro degli scenari sono progettate per supportare, come creare report e personalizzare i report esistenti e altro ancora: [creare report interattivi con le cartelle di lavoro di monitoraggio di Azure](../platform/workbooks-overview.md)