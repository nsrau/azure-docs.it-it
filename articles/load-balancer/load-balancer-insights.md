---
title: Informazioni dettagliate per Azure Load Balancer
description: Usare il servizio di bilanciamento del carico per ottenere la localizzazione rapida degli errori e le decisioni di progettazione informate
services: load-balancer
documentationcenter: na
author: erichrt
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2020
ms.author: errobin
ms.openlocfilehash: d57dfd0a496e71c1f0e6ddea839723da35bc5f76
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686243"
---
# <a name="using-insights-to-monitor-and-configure-your-azure-load-balancer"></a>Uso di Insights per monitorare e configurare la Azure Load Balancer

Tramite monitoraggio di Azure per le reti sono disponibili visualizzazioni delle dipendenze funzionali e dashboard delle metriche preconfigurate per i bilanciamenti del carico. Questi oggetti visivi consentono di prendere decisioni di progettazione informate e di localizzare, diagnosticare e risolvere rapidamente eventuali errori.

>[!NOTE] 
>Si noti che questa funzionalità è in anteprima e la visualizzazione dipendenza funzionale e il dashboard preconfigurato possono cambiare per migliorare questa esperienza

>[!IMPORTANT]
>Il Load Balancer Standard è necessario per visualizzare le metriche dello spazio dei nomi Load Balancer nel dashboard delle metriche preconfigurate. Sarà comunque possibile visualizzare le metriche dalla VM, dal set di scalabilità di macchine virtuali e dagli spazi dei nomi di monitoraggio della connessione, ma è consigliabile eseguire l' [aggiornamento alla versione standard](https://docs.microsoft.com/azure/load-balancer/upgrade-basic-standard) per tutti i carichi di lavoro di produzione per sfruttare i vantaggi offerti dal set di metriche di Load Balancer affidabile.

## <a name="functional-dependency-view"></a>Visualizzazione dipendenza funzionale

La visualizzazione delle dipendenze funzionali consentirà di immaginare anche le configurazioni del bilanciamento del carico più complesse. Con i commenti visivi sull'ultima configurazione di Load Balancer, è possibile eseguire gli aggiornamenti tenendo presente la configurazione.

È possibile accedere a questa visualizzazione visitando il pannello Insights della risorsa Load Balancer in Azure.

:::image type="content" source="./media/load-balancer-insights/load-balancer-functional-dependency-visual.png" alt-text="Depecition della visualizzazione dipendenze funzionale. Il front-end del servizio di bilanciamento del carico può essere visualizzato per la connessione ai membri del pool back-end tramite le regole configurate. Per Load Balancer Standard, le righe dalle regole di bilanciamento del carico alle istanze del pool back-end vengono codificate a colori in base allo stato del probe di integrità." border="true":::

Per i servizi di bilanciamento del carico standard, le risorse del pool back-end sono codificate a colori con lo stato del probe di integrità che indica la disponibilità corrente del pool back-end per gestire il traffico. Insieme alla topologia sopra riportata, viene visualizzato un grafico temporizzato dello stato di integrità, che fornisce una visualizzazione snapshot dell'integrità dell'applicazione.

## <a name="metrics-dashboard"></a>Dashboard delle metriche

Dal pannello Insights del Load Balancer è possibile selezionare metriche più dettagliate per visualizzare una [cartella di lavoro di monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/workbooks-overview) preconfigurata contenente gli oggetti visivi delle metriche rilevanti per aspetti specifici del Load Balancer. Questo dashboard mostrerà lo stato del Load Balancer e i collegamenti alla documentazione pertinente nella parte superiore della pagina.

Inizialmente verrà visualizzata la scheda Panoramica. È possibile spostarsi tra le schede disponibili, ciascuna delle quali contiene oggetti visivi relativi a un aspetto specifico del Load Balancer. Le linee guida esplicite per ogni sono disponibili nel dashboard nella parte inferiore di ogni scheda.

Le schede del dashboard attualmente disponibili sono:
* Panoramica
* Disponibilità back-end e front-end
* Velocità effettiva dei dati
* Distribuzione del flusso
* Monitoraggi della connessione
* Definizioni delle metriche 

### <a name="overview-tab"></a>Scheda Panoramica
La scheda Panoramica contiene una griglia ricercabile con lo stato complessivo di disponibilità del percorso dati e del probe di integrità per ogni IP front-end collegato alla Load Balancer. Queste metriche indicano se l'IP front-end è reattivo e le istanze di calcolo nel pool back-end rispondono individualmente alle connessioni in ingresso.

È anche possibile visualizzare la velocità effettiva complessiva dei dati per ogni IP front-end in questa pagina per avere la possibilità di produrre e ricevere livelli di traffico previsti. Il materiale sussidiario nella parte inferiore della pagina consente di visualizzare i valori irregolari nella scheda appropriata.

### <a name="frontend-and-backend-availability-tab"></a>Scheda disponibilità front-end e back-end
Le schede disponibilità front-end e back-end mostrano la velocità effettiva del percorso dati e le metriche dello stato del probe di integrità presentate in alcune visualizzazioni utili. Il primo grafico mostra il valore di aggregazione in modo che sia possibile determinare se è presente un problema. Il resto dei grafici Mostra queste metriche divise per diverse dimensioni in modo da poter risolvere i problemi e identificare le origini di eventuali problemi di disponibilità in ingresso.

Un flusso di lavoro per la visualizzazione di questi grafici è disponibile nella parte inferiore della pagina, con le cause più comuni di diversi sintomi. 

### <a name="data-throughput-tab"></a>Scheda velocità effettiva dati
La scheda velocità effettiva dati consente di esaminare la velocità effettiva in ingresso e in uscita per identificare se i modelli di traffico sono quelli previsti. Verrà visualizzata la velocità effettiva dei dati in ingresso e in uscita divisa per l'IP front-end e la porta front-end, in modo da poter identificare se i servizi in esecuzione eseguono singolarmente.

### <a name="flow-distribution"></a>Distribuzione del flusso
La scheda distribuzione flusso consente di visualizzare e gestire il numero di flussi che le istanze back-end ricevono e producono. Mostra la velocità di creazione del flusso e il numero di flussi per il traffico in ingresso e in uscita, nonché il traffico di rete ricevuto da ogni macchina virtuale e istanza del set di scalabilità di macchine virtuali. 

Queste visualizzazioni possono fornire commenti e suggerimenti su come la configurazione del Load Balancer o i modelli di traffico stiano causando il traffico sbilanciato. Se, ad esempio, è configurata l'affinità di sessione e un singolo client sta effettuando un numero sproporzionato di richieste. Consente inoltre di verificare se si sta per raggiungere il [limite di flusso per VM](https://docs.microsoft.com/azure/virtual-network/virtual-machine-network-throughput#flow-limits-and-recommendations) per le dimensioni del computer.

### <a name="connection-monitors"></a>Monitoraggi della connessione
Nella scheda monitoraggi connessione viene visualizzata la latenza di round trip su una mappa globale per tutti i [monitoraggi di connessione](https://docs.microsoft.com/azure/network-watcher/connection-monitor)  configurati. Questi oggetti visivi forniscono informazioni utili per i servizi con requisiti di latenza rigidi. Per soddisfare i requisiti, potrebbe essere necessario aggiungere altre distribuzioni a livello di area o passare a un modello di [bilanciamento del carico tra](https://docs.microsoft.com/azure/load-balancer/cross-region-overview) più aree

### <a name="metric-definitions"></a>Definizioni delle metriche
La scheda definizioni metrica contiene tutte le informazioni mostrate nell' [articolo sulle metriche multidimensionali](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#multi-dimensional-metrics).

## <a name="next-steps"></a>Passaggi successivi
* Esaminare il dashboard e inviare commenti e suggerimenti usando il collegamento seguente se è possibile migliorare
* [Esaminare la documentazione relativa alle metriche per assicurarsi di comprendere il modo in cui ogni metrica viene calcolata](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#multi-dimensional-metrics)
* [Creare monitoraggi connessione per il Load Balancer](https://docs.microsoft.com/azure/network-watcher/connection-monitor)
* [Creare cartelle di lavoro personalizzate](https://docs.microsoft.com/azure/azure-monitor/platform/workbooks-overview). è possibile fare clic sul pulsante modifica nel dashboard dettagliato della metrica.
