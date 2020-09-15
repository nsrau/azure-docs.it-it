---
title: Monitoraggio di Azure per le reti (anteprima)
description: Una rapida panoramica di monitoraggio di Azure per la rete che fornisce una visualizzazione completa dell'integrità e delle metriche per tutte le risorse di rete distribuite senza alcuna configurazione.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/07/2019
ms.openlocfilehash: 5a85efc5de0d99904da568c97672adf40ce8a414
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90088919"
---
# <a name="azure-monitor-for-networks-preview"></a>Monitoraggio di Azure per le reti (anteprima)
Monitoraggio di Azure per la rete offre una panoramica completa dell'integrità e delle metriche per tutte le risorse di rete distribuite senza alcuna configurazione. La funzionalità di ricerca avanzata consente di identificare le dipendenze delle risorse che consentono scenari come l'identificazione delle risorse che ospitano il sito web cercando il nome del sito Web ospitato.

La pagina **Panoramica** di monitoraggio di Azure per le reti fornisce un modo semplice per visualizzare l'inventario delle risorse di rete insieme all'integrità delle risorse e agli avvisi. È divisa in quattro aree funzionali principali:

- Ricerca e filtro
- Integrità risorse e metriche
- Avvisi 
- Visualizzazione dipendenze

![Pagina di panoramica](media/network-insights-overview/overview.png)

## <a name="search-and-filtering"></a>Ricerca e filtro
La vista integrità risorse e avvisi può essere personalizzata usando filtri come la **sottoscrizione**, il **gruppo di risorse** e il tipo di **risorsa**. Nella casella di ricerca è disponibile la funzionalità per la ricerca nelle proprietà delle risorse.

La casella di ricerca può essere usata per cercare le risorse e le risorse associate. Un indirizzo IP pubblico, ad esempio, è associato a un gateway applicazione. La ricerca del nome DNS degli indirizzi IP pubblici identificherà sia l'IP pubblico che il gateway applicazione associato.

![Screenshot mostra i risultati di una ricerca di un nome DNS.](media/network-insights-overview/search.png)


## <a name="resource-health-and-metric"></a>Integrità risorse e metrica
Ogni riquadro rappresenta un tipo di risorsa con il numero di istanze distribuite in tutte le sottoscrizioni selezionate insieme allo stato di integrità delle risorse. Nell'esempio seguente sono state distribuite 105 ER e connessioni VPN, 103 sono integre e 2 non disponibili.

![Resource Health](media/network-insights-overview/resource-health.png)

Facendo clic sulle due connessioni ER e VPN non disponibili, viene avviata una visualizzazione metrica. 

![Visualizzazione delle metriche](media/network-insights-overview/metric-view.png)

È possibile fare clic su ogni elemento nella visualizzazione griglia. Fare clic sull'icona integrità per reindirizzare a integrità risorse per tale connessione. Fare clic su avvisi per reindirizzare alla pagina avvisi e metriche rispettivamente per tale connessione. 

## <a name="alerts"></a>Avvisi
La griglia degli **avvisi** a destra fornisce una visualizzazione di tutti gli avvisi generati per le risorse selezionate tra tutte le sottoscrizioni. Fare clic sui conteggi degli avvisi per passare alla pagina avvisi dettagliati.

## <a name="dependency-view"></a>Visualizzazione dipendenze
La visualizzazione delle **dipendenze** consente di visualizzare il modo in cui la risorsa è configurata. Attualmente la visualizzazione dipendenze è supportata per il gateway applicazione, la rete WAN virtuale e la Load Balancer. Ad esempio, nel caso del gateway applicazione, è possibile accedere alla visualizzazione dipendenze facendo clic sul nome della risorsa del gateway applicazione nella visualizzazione griglia metriche. Questo vale anche per la rete WAN virtuale e la Load Balancer. 

![Visualizzazione del gateway applicazione](media/network-insights-overview/application-gateway.png)

La visualizzazione delle **dipendenze** per il gateway applicazione offre una visualizzazione semplificata del modo in cui gli indirizzi IP front-end sono connessi ai listener, alle regole e al pool back-end. I bordi di connessione sono codificati a colori e forniscono dettagli aggiuntivi in base all'integrità del pool back-end. La vista fornisce anche una visualizzazione dettagliata delle metriche e delle metriche del gateway applicazione per tutti i pool back-end correlati, ad esempio il set di scalabilità di macchine virtuali e le istanze di macchine virtuali.

![Visualizzazione dipendenze](media/network-insights-overview/dependency-view.png)

Il grafico dipendenze consente di spostarsi agevolmente sulle impostazioni di configurazione. Fare clic con il pulsante destro del mouse su un pool back-end per accedere ad altre funzionalità. Ad esempio, se il pool back-end è una macchina virtuale, è possibile accedere direttamente a Virtual Machine Insights e alla connessione Network Watcher per risolvere i problemi e identificare i problemi di connettività.

![Menu visualizzazione dipendenze](media/network-insights-overview/dependency-view-menu.png)

La barra di ricerca e di filtro nella visualizzazione dipendenze offre un modo semplice per eseguire ricerche nel grafico. Ad esempio, la ricerca di *AppGWTestRule* nell'esempio seguente consente di limitare la visualizzazione grafica a tutti i nodi connessi tramite *AppGWTestRule*. 

![Esempio di ricerca](media/network-insights-overview/search-example.png)

Filtri diversi consentono di restringere a un percorso e a uno stato specifici. Ad esempio, selezionare solo non *integro* dall'elenco a discesa **stato integrità** per visualizzare tutti i bordi in cui lo stato è non *integro*.

Fare clic su **visualizzazione metrica dettagliata** per avviare una cartella di lavoro preconfigurata con metriche dettagliate per il gateway applicazione, tutte le risorse del pool back-end e gli indirizzi IP front-end. 

## <a name="next-steps"></a>Passaggi successivi 

- Per altre informazioni sul monitoraggio della rete, vedere informazioni su [Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md).
