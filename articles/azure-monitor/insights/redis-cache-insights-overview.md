---
title: Monitoraggio di Azure per cache di Azure per Redis (anteprima) | Microsoft Docs
description: Questo articolo descrive la funzionalità di monitoraggio di Azure per cache Redis che fornisce la cache di Azure per i proprietari di redis con una rapida comprensione dei problemi di prestazioni e utilizzo.
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/07/2020
ms.openlocfilehash: af32459ffcb50c9f1cfdc59d2c8d355d3551e230
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83210974"
---
# <a name="explore-azure-monitor-for-azure-cache-for-redis-preview"></a>Esplora monitoraggio di Azure per cache di Azure per Redis (anteprima)

Monitoraggio di Azure per cache di Azure per Redis (anteprima) offre una panoramica delle prestazioni, degli errori, della capacità e dello stato operativo generali di tutte le risorse di cache di Azure per le risorse di redis in un'esperienza interattiva unificata. Questo articolo consente di comprendere i vantaggi di questa nuova esperienza di monitoraggio e di come è possibile modificare e adattare l'esperienza per soddisfare le esigenze specifiche dell'organizzazione.

## <a name="introduction"></a>Introduzione

Prima di approfondire l'esperienza, è necessario comprenderne il modo in cui presenta e visualizza le informazioni.

Offre:

* Nel **punto di vista della scalabilità** della cache di Azure per le risorse Redis in tutte le sottoscrizioni in un'unica posizione, con la possibilità di definire l'ambito in modo selettivo solo per le sottoscrizioni e le risorse di cui si è interessati per la valutazione.

* Eseguire il **drill-down dell'analisi** di una particolare cache di Azure per la risorsa Redis per facilitare la diagnosi dei problemi o l'analisi dettagliata per utilizzo, errori, capacità e operazioni di categoria. Selezionando una di queste opzioni viene fornita una visualizzazione approfondita del pertinente.  

* **Personalizzabile** : questa esperienza è basata sui modelli di cartella di lavoro di monitoraggio di Azure che consentono di modificare le metriche visualizzate, modificare o impostare le soglie che si allineano ai limiti e quindi salvarle in una cartella di lavoro personalizzata. I grafici nelle cartelle di lavoro possono quindi essere aggiunti ai dashboard di Azure.  

Questa funzionalità non richiede l'abilitazione o la configurazione di alcun elemento. per impostazione predefinita, la cache di Azure per Redis viene raccolta.

>[!NOTE]
>Non sono previsti addebiti per accedere a questa funzionalità e verranno addebitate solo le funzionalità essenziali di monitoraggio di Azure configurate o abilitate, come descritto nella pagina [Dettagli prezzi di monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/) .

## <a name="view-utilization-and-performance-metrics-for-azure-cache-for-redis"></a>Visualizzare le metriche di utilizzo e delle prestazioni per cache di Azure per Redis

Per visualizzare l'utilizzo e le prestazioni degli account di archiviazione in tutte le sottoscrizioni, seguire questa procedura.

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Cercare **monitoraggio** e selezionare **monitoraggio**.

    ![Casella di ricerca con la parola "monitor" e un elenco a discesa che indica i servizi "monitoraggio" con un'immagine di stile tachimetro](./media/cosmosdb-insights-overview/search-monitor.png)

3. Selezionare **cache di Azure per Redis (anteprima)**.

Se questa opzione non è presente, fare clic su **altro** e selezionare **cache di Azure per Redis**.

### <a name="overview"></a>Panoramica

In **Panoramica**la tabella Visualizza la cache interattiva di Azure per le metriche di Redis. È possibile filtrare i risultati in base alle opzioni selezionate dagli elenchi a discesa seguenti:

* Sono elencate solo le **sottoscrizioni con** una risorsa cache di Azure per Redis.  

* **Cache di Azure per Redis** : è possibile selezionare tutti, un subset o una singola cache di Azure per la risorsa Redis.

* **Intervallo di tempo** : per impostazione predefinita, Visualizza le ultime 4 ore di informazioni in base alle selezioni corrispondenti effettuate.

Il riquadro del contatore sotto gli elenchi a discesa esegue il rollup del numero totale di cache di Azure per le risorse di redis nelle sottoscrizioni selezionate. Per le colonne nella cartella di lavoro è disponibile una codifica dei colori condizionale o Heatmaps che segnala le metriche delle transazioni. Il colore più profondo ha il valore più alto e un colore più chiaro è basato sui valori più bassi.

Selezionando una freccia a discesa accanto a una cache di Azure per le risorse Redis verrà rilevata una suddivisione delle metriche delle prestazioni a livello di singola risorsa:

![Screenshot dell'esperienza panoramica](./media/redis-cache-insights-overview/overview.png)

Selezionando il nome della risorsa cache di Azure per Redis evidenziato in blu verrà visualizzata la **Panoramica** predefinita per l'account associato. Mostra `Used Memory` ,, `Used Memory Percentage` `Server Load` , `Server Load Timeline` , `CPU` , `Connected Clients` , `Cache Misses` , `Errors (Max)` .

### <a name="operations"></a>Operazioni

Selezionare **operazioni** nella parte superiore della pagina e verrà visualizzata la parte **operazioni** del modello di cartella di lavoro. Mostra `Total Operations` ,, `Total Operations Timeline` `Operations Per Second` , `Gets` , `Sets` .

![Screenshot dell'esperienza panoramica](./media/redis-cache-insights-overview/operations.png)

### <a name="usage"></a>Utilizzo

Selezionare **utilizzo** nella parte superiore della pagina. viene visualizzata la parte relativa all' **utilizzo** del modello di cartella di lavoro. Mostra `Cache Read` ,, `Cache Read Timeline` `CacheWrite` , `CacheHits` , `Cache Misses` .

![Screenshot dell'esperienza panoramica](./media/redis-cache-insights-overview/usage.png)

Selezionare **errori** nella parte superiore della pagina e viene visualizzata la parte relativa agli **errori** del modello di cartella di lavoro. Mostra `Total Errors` ,, `Failover/Errors` `UnresponsiveClient/Errors` , `RDB/Errors` , `AOF/Errors` , `Export/Errors` , `Dataloss/Errors` , `Import/Errors` .

![Screenshot degli errori con suddivisione in base al tipo di richiesta HTTP](./media/redis-cache-insights-overview/failures.png)

### <a name="metric-definitions"></a>Definizioni delle metriche

Per un elenco completo delle definizioni delle metriche che comprendono queste cartelle di lavoro, vedere l' [articolo metriche disponibili e intervalli di report](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-monitor#available-metrics-and-reporting-intervals).

## <a name="pin-export-and-expand"></a>Aggiungi, esporta ed Espandi

È possibile aggiungere una delle sezioni della metrica a un [dashboard di Azure](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) selezionando l'icona a puntina da disegno in alto a destra nella sezione.

![Esempio di aggiunta della sezione metrica al dashboard](./media/cosmosdb-insights-overview/pin.png)

Per esportare i dati nel formato di Excel, fare clic sull'icona a forma di freccia rivolta verso il basso a sinistra dell'icona a forma di puntina da disegno.

![Icona Esporta cartella di lavoro](./media/cosmosdb-insights-overview/export.png)

Per espandere o comprimere tutte le visualizzazioni a discesa nella cartella di lavoro, selezionare l'icona di espansione a sinistra dell'icona Esporta:

![Icona Espandi cartella di lavoro](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cache-for-redis-preview"></a>Personalizzare monitoraggio di Azure per cache di Azure per Redis (anteprima)

Poiché questa esperienza è basata sui modelli di cartella di lavoro di monitoraggio di Azure, è possibile **personalizzare**la  >  **modifica** e **salvare** una copia della versione modificata in una cartella di lavoro personalizzata. 

![Personalizza barra](./media/cosmosdb-insights-overview/customize.png)

Le cartelle di lavoro vengono salvate in un gruppo di risorse, nella sezione **report personali** riservata all'utente o nella sezione **report condivisi** accessibile a tutti gli utenti con accesso al gruppo di risorse. Dopo avere salvato la cartella di lavoro personalizzata, è necessario passare alla raccolta di cartelle di lavoro per avviarla.

![Avvia raccolta cartelle di lavoro dalla barra dei comandi](./media/cosmosdb-insights-overview/gallery.png)

## <a name="next-steps"></a>Passaggi successivi

* Configurare gli [avvisi delle metriche](../platform/alerts-metric.md) e le notifiche sull'integrità del [servizio](../../service-health/alerts-activity-log-service-notifications.md) per impostare avvisi automatici per facilitare il rilevamento dei problemi.

* Informazioni sulle cartelle di lavoro degli scenari sono progettate per supportare, come creare nuovi report esistenti e personalizzarli e altro ancora esaminando [creare report interattivi con le cartelle di lavoro di monitoraggio di Azure](../app/usage-workbooks.md).
