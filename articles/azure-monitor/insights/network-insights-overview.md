---
title: Monitoraggio di Azure per reti (anteprima)Azure Monitor for Networks (Preview)
description: Una rapida panoramica di Monitoraggio di Azure per rete che offre una visualizzazione completa dell'integrità e delle metriche per tutte le risorse di rete distribuite senza alcuna configurazione.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/07/2019
ms.openlocfilehash: 0f5b2fbd13cb9658e255fde727e115df748aaed9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77654869"
---
# <a name="azure-monitor-for-networks-preview"></a>Monitoraggio di Azure per reti (anteprima)Azure Monitor for Networks (Preview)
Monitoraggio di Azure per rete offre una visualizzazione completa dell'integrità e delle metriche per tutte le risorse di rete distribuite senza alcuna configurazione. La funzionalità di ricerca avanzata consente di identificare le dipendenze delle risorse, consentendo scenari quali l'identificazione delle risorse che ospitano il sito Web semplicemente cercando il nome del sito Web ospitato.

La pagina **Panoramica** di Monitoraggio di Azure per reti offre un modo semplice per visualizzare l'inventario delle risorse di rete insieme all'integrità delle risorse e agli avvisi. È diviso in quattro aree funzionali chiave:

- Ricerca e filtraggio
- Integrità delle risorse e metriche
- Avvisi 
- Visualizzazione delle dipendenze

![Pagina di panoramica](media/network-insights-overview/overview.png)

## <a name="search-and-filtering"></a>Ricerca e filtraggio
La visualizzazione dell'integrità delle risorse e degli avvisi può essere personalizzata utilizzando filtri come **Sottoscrizione,** **Gruppo di risorse** e Tipo di **risorsa**. La casella di ricerca consente di eseguire ricerche nelle proprietà delle risorse.

La casella di ricerca può essere utilizzata per cercare le risorse e le risorse associate. Ad esempio, un indirizzo IP pubblico è associato a un gateway applicazione. La ricerca del nome DNS IP pubblico identificherà sia l'indirizzo IP pubblico che il gateway applicazione associato.

![Ricerca](media/network-insights-overview/search.png)


## <a name="resource-health-and-metric"></a>Integrità delle risorse e metrica
Ogni riquadro rappresenta un tipo di risorsa, con il numero di istanze distribuite in tutte le sottoscrizioni selezionate insieme allo stato di integrità delle risorse. Nell'esempio seguente sono distribuiti 105 connessioni ER e VPN, 103 sono integre e 2 non disponibili.

![Resource Health](media/network-insights-overview/resource-health.png)

Facendo clic sulle due connessioni ER e VPN non disponibili, viene avviata una visualizzazione metrica. 

![Visualizzazione delle metriche](media/network-insights-overview/metric-view.png)

È possibile fare clic su ogni elemento nella vista griglia. Fare clic sull'icona Integrità per reindirizzare all'integrità delle risorse per la connessione. Fare clic su Avvisi per reindirizzare alla pagina Avvisi e metriche rispettivamente per tale connessione. 

## <a name="alerts"></a>Avvisi
La griglia **Avvisi** a destra fornisce una visualizzazione di tutti gli avvisi generati per le risorse selezionate in tutte le sottoscrizioni. Fare clic sui conteggi degli avvisi per passare alla pagina degli avvisi dettagliati.

## <a name="dependency-view"></a>Visualizzazione delle dipendenze
La visualizzazione **Dipendenza** consente di visualizzare la modalità di configurazione della risorsa. Attualmente la visualizzazione delle dipendenze è supportata solo per il gateway applicazione. È possibile accedere alla visualizzazione Delle dipendenze facendo clic sul nome della risorsa del gateway applicazione dalla visualizzazione griglia delle metriche.

![Visualizzazione Gateway applicazione](media/network-insights-overview/application-gateway.png)

La visualizzazione **Dipendenza** per il gateway applicazione offre una visualizzazione semplificata della modalità di connessione degli indirizzi IP front-end ai listener, alle regole e al pool back-end. I bordi di connessione sono codificati a colori e forniscono dettagli aggiuntivi in base all'integrità del pool back-end. La visualizzazione fornisce inoltre una visualizzazione dettagliata delle metriche e metriche del gateway applicazione per tutti i pool back-end correlati, ad esempio le istanze di VMSS e VM.

![Visualizzazione delle dipendenze](media/network-insights-overview/dependency-view.png)

Il grafico delle dipendenze consente di accedere facilmente alle impostazioni di configurazione. Fare clic con il pulsante destro del mouse su un pool back-end per accedere ad altre funzionalità. Ad esempio, se il pool back-end è una macchina virtuale, è possibile accedere direttamente alla risoluzione dei problemi relativi a VM Insights e Network Watcher per identificare i problemi di connettività.

![Menu della visualizzazione Delle dipendenze](media/network-insights-overview/dependency-view-menu.png)

La barra di ricerca e filtro nella visualizzazione delle dipendenze fornisce un modo semplice per eseguire la ricerca nel grafico. Ad esempio, la ricerca di *AppGWTestRule* nell'esempio seguente restringerà la visualizzazione grafica a tutti i nodi connessi tramite *AppGWTestRule*. 

![Esempio di ricerca](media/network-insights-overview/search-example.png)

Filtri diversi consentono di restringere il campo a un percorso e a uno stato specifici. Ad esempio, selezionare Solo *Non integro* nell'elenco a discesa **Stato integrità** per visualizzare tutti i bordi in cui lo stato è *Non integro*.

Fare clic su **Visualizzazione metrica dettagliata** per avviare una cartella di lavoro preconfigurata con metriche dettagliate per il gateway applicazione, tutte le risorse del pool back-end e gli indirizzi IP front-end. 

## <a name="next-steps"></a>Passaggi successivi 

- Per altre informazioni sul monitoraggio della rete, vedere Che cos'è [Azure Network Watcher?](/azure/network-watcher/network-watcher-monitoring-overview).
