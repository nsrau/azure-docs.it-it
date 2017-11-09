---
title: Come monitorare Cache Redis di Azure | Microsoft Docs
description: "Informazioni su come monitorare l'integrità e le prestazioni delle istanze di Cache Redis di Azure"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 7e70b153-9c87-4290-85af-2228f31df118
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: sdanie
ms.openlocfilehash: 8996f5ce03e39557d9cc9c3de1ec214f5cd664b4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-monitor-azure-redis-cache"></a>Come monitorare Cache Redis di Azure
La Cache Redis di Azure usa [Monitoraggio di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) per offrire alcune opzioni per il monitoraggio delle istanze della cache. È possibile visualizzare le metriche, aggiungere i grafici delle metriche alla Schermata iniziale, personalizzare l'intervallo di data e ora per il monitoraggio dei grafici, aggiungere e rimuovere metriche dai grafici e impostare avvisi per le situazioni in cui vengono soddisfatte determinate condizioni. Questi strumenti permettono di monitorare l'integrità delle istanze della Cache Redis di Azure e semplificano la gestione delle applicazioni di memorizzazione nella cache.

Le metriche per le istanze della Cache Redis di Azure vengono raccolte tramite il comando [INFO](http://redis.io/commands/info) di Redis circa due volte al minuto e vengono archiviate automaticamente per 30 giorni (vedere [Esportare le metriche della cache](#export-cache-metrics) per configurare criteri di conservazione diversi), in modo che sia possibile visualizzarle nei grafici relativi alle metriche e valutarle in base alla regole di avviso. Per ulteriori informazioni sui diversi valori INFO usati per ciascuna metrica della cache, vedere [Metriche disponibili e intervalli di report](#available-metrics-and-reporting-intervals).

<a name="view-cache-metrics"></a>

Per visualizzare le metriche della cache, [cercare](cache-configure.md#configure-redis-cache-settings) l'istanza della cache nel [portale di Azure](https://portal.azure.com).  La Cache Redis di Azure offre alcuni grafici incorporati nel pannello **Panoramica** e nel pannello **Metriche di Redis**. Ogni grafico può essere personalizzata aggiungendo o rimuovendo le metriche e modificando l'intervallo di report.

![Metriche Redis](./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png)

## <a name="view-pre-configured-metrics-charts"></a>Visualizzare i grafici preconfigurati relativi alle metriche

Il pannello **Panoramica** include i grafici preconfigurati seguenti relativi al monitoraggio.

* [Grafici di monitoraggio](#monitoring-charts)
* [Grafici di utilizzo](#usage-charts)

### <a name="monitoring-charts"></a>Grafici di monitoraggio
La sezione **Monitoraggio** del pannello **Panoramica** include i grafici **Riscontri e mancati riscontri**, **Recuperi e impostazioni**, **Connessioni** e **Totale comandi**.

![Grafici di monitoraggio](./media/cache-how-to-monitor/redis-cache-monitoring-part.png)

### <a name="usage-charts"></a>Grafici di utilizzo
La sezione **Utilizzo** del pannello **Panoramica** include i grafici **Carico server Redis**, **Utilizzo memoria**, **Larghezza di banda di rete** e **Utilizzo CPU** nonché **Piano tariffario** per l'istanza della cache.

![Grafici di utilizzo](./media/cache-how-to-monitor/redis-cache-usage-part.png)

In **Livello di prezzo** è possibile visualizzare il livello di prezzo della cache nonché [scalare](cache-how-to-scale.md) la cache a un livello di prezzo diverso.

## <a name="view-metrics-with-azure-monitor"></a>Visualizzare le metriche con Monitoraggio di Azure
Per visualizzare le metriche di Redis e creare grafici personalizzati usando Monitoraggio di Azure, scegliere **Metriche** dal menu **Risorsa**, quindi personalizzare il grafico usando le metriche desiderate, l'intervallo di report, il tipo di grafico e altri elementi appropriati per le esigenze specifiche.

![Metriche Redis](./media/cache-how-to-monitor/redis-cache-monitor.png)

Per altre informazioni sull'uso delle metriche con Monitoraggio di Azure, vedere [Panoramica delle metriche in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

<a name="how-to-view-metrics-and-customize-chart"></a>
<a name="enable-cache-diagnostics"></a>
## <a name="export-cache-metrics"></a>Esportare le metriche della cache
Per impostazione predefinita, le metriche relative alla cache in Monitoraggio di Azure vengono [archiviate per 30 giorni](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md#store-and-archive) e quindi vengono eliminate. Per salvare in modo permanente le metriche della cache per un periodo superiore a 30 giorni, è possibile [designare un account di archiviazione](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md) e specificare un criterio di **Conservazione (giorni)** per le metriche della cache. 

Per configurare un account di archiviazione per le metriche della cache:

1. Scegliere **Diagnostica** dal menu **Risorsa** nel pannello **Cache Redis**.
2. Fare clic su **Sì**.
3. Selezionare **Archivia in un account di archiviazione**.
4. Selezionare l'account di archiviazione in cui archiviare le metriche della cache.
5. Selezionare la casella di controllo **1 minuto** e specificare un criterio di **Conservazione (giorni)**. Se non si vogliono applicare criteri di conservazione e conservare i dati senza scadenza, impostare **Conservazione (giorni)** su **0**.
6. Fare clic su **Salva**.

![Diagnostica di Redis](./media/cache-how-to-monitor/redis-cache-diagnostics.png)

>[!NOTE]
>Oltre ad archiviare le metriche della cache, è possibile [eseguirne lo streaming a un hub eventi o inviarli a Log Analytics](../monitoring-and-diagnostics/monitoring-overview-metrics.md#export-metrics).
>
>

Per accedere alle metriche, è possibile visualizzarli nel portale di Azure, come illustrato in precedenza in questo articolo, e anche accedervi tramite l'[API REST Monitoraggio di Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md#access-metrics-via-the-rest-api).

> [!NOTE]
> Se si modificano gli account di archiviazione, i dati nell'account di archiviazione configurato in precedenza rimangono disponibili per il download, ma non vengono visualizzati nel portale di Azure.  
> 
> 

## <a name="available-metrics-and-reporting-intervals"></a>Metriche disponibili e intervalli di report
Le metriche della cache vengono segnalate usando diversi intervalli di report, tra cui **Ultima ora**, **Oggi**, **Ultima settimana** e **Personalizza**. Nel pannello **Metrica** per ogni grafico delle metriche vengono visualizzati i valori medi, minimi e massimi per ogni metrica nel grafico e alcune metriche visualizzano un totale per l'intervallo di report. 

In ogni metrica sono incluse due versioni. Una metrica misura la prestazione per l'intera cache e, per le cache che usano il [clustering](cache-how-to-premium-clustering.md), una seconda versione della metrica che include `(Shard 0-9)` nel nome misura le prestazioni per una singola partizione in una cache. Se ad esempio una cache ha 4 partizioni, `Cache Hits` è la quantità totale di riscontri per l'intera cache e `Cache Hits (Shard 3)` rappresenta le occorrenze di tale partizionamento della cache.

> [!NOTE]
> Anche quando la cache è inattiva senza applicazioni client attive connesse, è possibile osservare una certa attività della cache, ad esempio client connessi, uso della memoria e operazioni eseguite. Questa attività è normale durante il funzionamento di un'istanza di Cache Redis di Azure.
> 
> 

| Metrica | Descrizione |
| --- | --- |
| Riscontri cache |Il numero di ricerche chiave riuscite durante l'intervallo di report specificato. Questo `keyspace_hits` è associato al comando [INFO](http://redis.io/commands/info) di Redis. |
| Mancati riscontri nella cache |Il numero di ricerche chiave non riuscite durante l'intervallo di report specificato. Questo `keyspace_misses` è associato al comando INFO di Redis. I mancati riscontri nella cache non indicano necessariamente un problema con la cache. Ad esempio, quando si utilizza il modello di programmazione cache-aside, un'applicazione esegue la ricerca di un elemento innanzitutto nella cache. Se l'elemento non è presente (mancato riscontro nella cache), viene recuperato dal database e aggiunto alla cache per la volta successiva. I mancati riscontri nella cache sono un comportamento normale per il modello di programmazione cache-aside. Se il numero di mancati riscontri nella cache è superiore al previsto, esaminare la logica dell'applicazione che consente di popolare ed eseguire le lettura dalla cache. Se gli elementi vengono rimossi dalla cache a causa della pressione della memoria, si possono verificare alcuni mancati riscontri nella cache, ma una metrica migliore da monitorare relativamente alla pressione della memoria sarebbe `Used Memory` o `Evicted Keys`. |
| Client connessi |Il numero di connessioni client alla cache durante l'intervallo di report specificato. Questo `connected_clients` è associato al comando INFO di Redis. Una volta raggiunto il [limite delle connessioni](cache-configure.md#default-redis-server-configuration) , i successivi tentativi di connessione alla cache avranno esito negativo. Si noti che anche in assenza di applicazione client attive, è possibile che siano presenti alcune istanze di client connesse a causa di processi e connessioni interni. |
| Chiavi rimosse |Il numero di elementi rimossi dalla cache durante l'intervallo di report specificato a causa del limite `maxmemory` . Questo `evicted_keys` è associato al comando INFO di Redis. |
| Chiavi scadute |Il numero di elementi nella cache durante l'intervallo di report specificato. Questo valore esegue il mapping a `expired_keys` dal comando INFO di Redis. |
| Totale chiavi  | Il numero massimo di chiavi nella cache durante l'ultimo periodo di report. Questo `keyspace` è associato al comando INFO di Redis. A causa di una limitazione del sistema di metriche sottostante, per le cache con il clustering abilitato, il valore Totale chiavi restituisce il numero massimo di chiavi della partizione che includeva il numero massimo di chiavi durante l'intervallo di report.  |
| Operazioni Get |Il numero di operazioni Get nella cache durante l'intervallo di report specificato. Questo valore è la somma dei seguenti valori del comando INFO di Redis (tutto): `cmdstat_get`, `cmdstat_hget`, `cmdstat_hgetall`, `cmdstat_hmget`, `cmdstat_mget`, `cmdstat_getbit` e `cmdstat_getrange`, ed è equivalente alla somma dei riscontri e dei mancati riscontri nella cache durante l'intervallo di report. |
| Carico server Redis |La percentuale di cicli in cui il server di Redis è impegnato nell’elaborare e non inattivo in attesa di messaggi. Se il contatore raggiunge 100 significa che il server di Redis ha raggiunto un limite massimo delle prestazioni e la CPU non può elaborare il lavoro più velocemente. Se si osserva un elevato carico del server Redis si vedranno le eccezioni di timeout nel client. In tal caso, è necessario prendere in considerazione il dimensionamento o il partizionamento dei dati in più cache. |
| Operazioni Set |Il numero di operazioni Set nella cache durante l'intervallo di report specificato. Questo valore è la somma dei seguenti valori del comando INFO di Redis (tutto): `cmdstat_set`, `cmdstat_hset`, `cmdstat_hmset`, `cmdstat_hsetnx`, `cmdstat_lset`, `cmdstat_mset`, `cmdstat_msetnx`, `cmdstat_setbit`, `cmdstat_setex`, `cmdstat_setrange` e `cmdstat_setnx`. |
| Totale operazioni |Numero totale di comandi elaborati dal server di cache durante l'intervallo di report specificato. Questo valore esegue il mapping a `total_commands_processed` dal comando INFO di Redis. Si noti che quando Cache Redis di Azure viene utilizzata esclusivamente per la pubblicazione o la sottoscrizione non saranno disponibili metriche per `Cache Hits`, `Cache Misses`, `Gets` o `Sets`, ma saranno disponibili metriche `Total Operations` che riflettono l'utilizzo della cache per le operazioni di pubblicazione/sottoscrizione. |
| Memoria utilizzata |La quantità di memoria cache utilizzata per le coppie chiave/valore nella cache in MB durante l'intervallo di report specificato. Questo valore esegue il mapping a `used_memory` dal comando INFO di Redis. Non include i metadati o la frammentazione. |
| Memoria utilizzata RSS |La quantità di memoria cache utilizzata in MB durante l'intervallo di report specificato, comprese la frammentazione e i metadati. Questo valore esegue il mapping a `used_memory_rss` dal comando INFO di Redis. |
| CPU |L'utilizzo della CPU del server Cache Redis di Azure come percentuale durante l'intervallo di report specificato. Questo valore è associato al contatore delle prestazioni `\Processor(_Total)\% Processor Time` del sistema operativo. |
| Lettura da cache |La quantità di dati letti dalla cache in megabyte al secondo (MB/s) durante l'intervallo di report specificato. Questo valore è derivato dalle schede di interfaccia di rete che supportano la macchina virtuale che ospita la cache, e non è specifico di Redis. **Questo valore corrisponde alla larghezza di banda della rete usata da questa cache. Se si desidera impostare avvisi per i limiti della larghezza di banda della rete lato server, usare questo contatore `Cache Read`. Per i limiti della larghezza di banda osservati nei diversi piani tariffari e nelle varie dimensioni della cache, vedere [questa tabella](cache-faq.md#cache-performance).** |
| Scrittura nella cache |La quantità di dati scritti nella cache in megabyte al secondo (MB/s) durante l'intervallo di report specificato. Questo valore è derivato dalle schede di interfaccia di rete che supportano la macchina virtuale che ospita la cache, e non è specifico di Redis. Questo valore corrisponde alla larghezza di banda della rete relativa ai dati inviati alla cache dal client. |

<a name="operations-and-alerts"></a>
## <a name="alerts"></a>Avvisi
È possibile configurare un avviso basato sulle metriche e sui log attività. Monitoraggio di Azure consente di configurare un avviso in modo che, se attivato, esegua queste operazioni:

* Inviare una notifica via posta elettronica
* Chiamare un webhook
* Richiamare un'app per la logica di Azure

Per configurare le regole di avviso per la cache, scegliere **Regole di avviso** dal menu **Risorsa**.

![Monitoraggio](./media/cache-how-to-monitor/redis-cache-monitoring.png)

Per altre informazioni sulla configurazione e sull'uso degli avvisi, vedere [Panoramica degli avvisi](../monitoring-and-diagnostics/insights-alerts-portal.md).

## <a name="activity-logs"></a>Log attività
I log attività offrono informazioni dettagliate sulle operazioni eseguite nelle istanze della Cache Redis di Azure. In precedenza erano noti come "log di controllo" o "log operativi". L'uso del log attività consente di acquisire informazioni dettagliate su qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita sulle istanze della Cache Redis di Azure. 

> [!NOTE]
> I log attività non includono le operazioni di lettura (GET).
>
>

Per visualizzare i log attività per la cache, scegliere **Log attività** dal menu **Risorsa**.

Per altre informazioni sui log attività, vedere [Panoramica del log attività di Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).











