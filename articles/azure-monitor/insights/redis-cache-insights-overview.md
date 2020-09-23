---
title: Monitoraggio di Azure per cache di Azure per Redis | Microsoft Docs
description: Questo articolo descrive la funzionalità Monitoraggio di Azure per la cache di Azure per Redis, che offre ai proprietari di cache una rapida prospettiva dei problemi di prestazioni e utilizzo.
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/10/2020
ms.openlocfilehash: e7b8a8e131cf7e0aae051f42043abf0640c927e4
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90887013"
---
# <a name="explore-azure-monitor-for-azure-cache-for-redis"></a>Esplora monitoraggio di Azure per cache di Azure per Redis

Per tutte le risorse della cache di Azure per le risorse Redis, monitoraggio di Azure per cache di Azure per Redis offre una visualizzazione unificata e interattiva di:

- Prestazioni complessive
- Errori
- Capacità
- Integrità operativa

Questo articolo illustra i vantaggi di questa nuova esperienza di monitoraggio. Descrive inoltre come modificare e adattare l'esperienza in base alle esigenze specifiche dell'organizzazione.

## <a name="introduction"></a>Introduzione

Prima di iniziare l'esperienza, è necessario capire come vengono presentate visivamente le informazioni da Monitoraggio di Azure per la cache di Azure per Redis.

Offre:

- **Prospettiva su larga scala** delle risorse della cache di Azure per Redis in una singola posizione tra tutte le sottoscrizioni. È possibile definire selettivamente l'ambito includendo solo le sottoscrizioni e le risorse da valutare.

- **Analisi drill-down** di una particolare risorsa della cache di Azure per Redis. È possibile diagnosticare i problemi e vedere l'analisi dettagliata di utilizzo, errori, capacità e operazioni. Selezionare una di queste categorie per una visualizzazione approfondita delle informazioni pertinenti.  

- **Personalizzazione** di questa esperienza, basata sulla creazione di modelli di cartelle di lavoro di Monitoraggio di Azure. L'esperienza consente di cambiare le metriche visualizzate e modificare o impostare le soglie in linea con i limiti. È possibile salvare le modifiche in una cartella di lavoro personalizzata e quindi aggiungere i relativi grafici ai dashboard di Azure.

Questa funzionalità non richiede di abilitare o configurare alcunché. Le informazioni della cache di Azure per Redis vengono raccolte per impostazione predefinita.

>[!NOTE]
>Non è previsto alcun addebito per accedere a questa funzionalità. Verranno addebitate solo le funzionalità essenziali di Monitoraggio di Azure configurate o abilitate, come descritto nella pagina di [dettagli sui prezzi di Monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="view-utilization-and-performance-metrics-for-azure-cache-for-redis"></a>Visualizzare le metriche su utilizzo e prestazioni per la cache di Azure per Redis

Per visualizzare i dati su utilizzo e prestazioni degli account di archiviazione in tutte le sottoscrizioni, procedere come segue:

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Cercare **Monitoraggio** e selezionare **Monitoraggio**.

    ![Casella di ricerca con la parola "Monitoraggio" e menu a discesa che indica "Monitoraggio" dei servizi con il simbolo di un tachimetro](./media/cosmosdb-insights-overview/search-monitor.png)

1. Selezionare **cache di Azure per Redis**. Se questa opzione non è presente, selezionare **Altro** > **Cache di Azure per Redis**.

### <a name="overview"></a>Panoramica

In **Panoramica** la tabella visualizza le metriche interattive della cache di Azure per Redis. È possibile filtrare i risultati in base alle opzioni selezionate dagli elenchi a discesa seguenti:

- **Sottoscrizioni**: sono elencate solo le sottoscrizioni con una risorsa della cache di Azure per Redis.  

- **Cache di Azure per Redis**: è possibile selezionare tutte le risorse della cache di Azure per Redis, un sottoinsieme o una singola risorsa.

- **Intervallo di tempo**: per impostazione predefinita, la tabella visualizza le ultime 4 ore di informazioni in base alle selezioni corrispondenti.

Sotto gli elenchi a discesa è presente il riquadro di un contatore. Il riquadro mostra il numero totale di risorse della cache di Azure per Redis incluse nelle sottoscrizioni selezionate. I codici a colori o le mappe termiche condizionali per le colonne della cartella di lavoro indicano le metriche delle transazioni. Il colore più scuro rappresenta il valore massimo. I colori più chiari rappresentano valori più bassi.

Selezionando la freccia dell'elenco a discesa accanto a una delle risorse della cache di Azure per Redis viene visualizzata una suddivisione delle metriche delle prestazioni a livello di singola risorsa.

![Screenshot dell'esperienza di panoramica](./media/redis-cache-insights-overview/overview.png)

Quando si seleziona il nome della risorsa della cache di Azure per Redis evidenziato in blu, viene visualizzata la tabella **Panoramica** predefinita per l'account associato. Sono incluse queste colonne:

- **Memoria usata**
- **Percentuale memoria usata**
- **Carico server**
- **Sequenza temporale carico server**
- **CPU**
- **Client connessi**
- **Mancati riscontri nella cache**
- **Errori (max)**

### <a name="operations"></a>Operazioni

Se si seleziona **Operazioni** nella parte superiore della pagina, si apre la tabella **Operazioni** del modello di cartella di lavoro. Sono incluse queste colonne:

- **Totale operazioni**
- **Sequenza temporale totale operazioni**
- **Operazioni al secondo**
- **Operazioni Get**
- **Operazioni Set**

![Screenshot dell'esperienza di operazioni](./media/redis-cache-insights-overview/operations.png)

### <a name="usage"></a>Uso

Se si seleziona **Utilizzo** nella parte superiore della pagina, si apre la tabella **Utilizzo** del modello di cartella di lavoro. Sono incluse queste colonne:

- **Lettura da cache**
- **Sequenza temporale lettura da cache**
- **Scrittura nella cache**
- **Riscontri cache**
- **Mancati riscontri nella cache**

![Screenshot dell'esperienza di utilizzo](./media/redis-cache-insights-overview/usage.png)

### <a name="failures"></a>Errori

Se si seleziona **Errori** nella parte superiore della pagina, si apre la tabella **Errori** del modello di cartella di lavoro. Sono incluse queste colonne:

- **Totale errori**
- **Failover/errori**
- **UnresponsiveClient/errori**
- **RDB/errori**
- **AOF/errori**
- **Export/errori**
- **Dataloss/errori**
- **Import/errori**

![Screenshot degli errori con suddivisione per tipo di richiesta HTTP](./media/redis-cache-insights-overview/failures.png)

### <a name="metric-definitions"></a>Definizioni delle metriche

Per un elenco completo delle definizioni delle metriche che formano queste cartelle di lavoro, vedere [l'articolo sulle metriche e gli intervalli di segnalazione disponibili](../../azure-cache-for-redis/cache-how-to-monitor.md#available-metrics-and-reporting-intervals).

## <a name="view-from-an-azure-cache-for-redis-resource"></a>Visualizzazione da una risorsa della cache di Azure per Redis

Per accedere a Monitoraggio di Azure per la cache di Azure per Redis direttamente da una singola risorsa:

1. Nel portale di Azure selezionare Cache di Azure per Redis.

2. Scegliere nell'elenco una singola risorsa della cache di Azure per Redis. Nella sezione monitoraggio scegliere Insights.

    ![Screenshot delle opzioni di menu con le parole "Insights" evidenziate in una casella rossa](./media/redis-cache-insights-overview/insights.png)

Queste visualizzazioni sono accessibili anche selezionando il nome di una risorsa della cache di Azure per Redis nella cartella di lavoro a livello di Monitoraggio di Azure.

### <a name="resource-level-overview"></a>Panoramica a livello di risorsa

Nella cartella di lavoro **Panoramica** per la cache di Azure per Redis, vengono visualizzate diverse metriche delle prestazioni che forniscono l'accesso a:

- Grafici interattivi delle prestazioni, che mostrano i dettagli più importanti relativi alle prestazioni della cache di Azure per Redis.

- Riquadri di metriche e stato che evidenziano le prestazioni delle partizioni, il numero totale di client connessi e la latenza complessiva.

![Screenshot del dashboard di panoramica che visualizza informazioni su prestazioni della CPU, memoria usata, client connessi, errori, chiavi scadute e chiavi rimosse](./media/redis-cache-insights-overview/resource-overview.png)

Selezionando una delle altre schede per **Prestazioni** o **Operazioni** vengono aperte le rispettive cartelle di lavoro.

### <a name="resource-level-performance"></a>Prestazioni a livello di risorsa

![Screenshot dei grafici delle prestazioni delle risorse](./media/redis-cache-insights-overview/resource-performance.png)

### <a name="resource-level-operations"></a>Operazioni a livello di risorsa

![Screenshot dei grafici delle operazioni sulle risorse](./media/redis-cache-insights-overview/resource-operations.png)

## <a name="pin-export-and-expand"></a>Aggiungere, esportare ed espandere

Per aggiungere una sezione di metriche a un [dashboard di Azure](../../azure-portal/azure-portal-dashboards.md), selezionare il simbolo della puntina da disegno nell'angolo in alto a destra della sezione.

![Sezione di metriche con il simbolo della puntina da disegno evidenziato](./media/cosmosdb-insights-overview/pin.png)

Per esportare i dati in formato Excel, selezionare l'icona della freccia in basso a sinistra dell'icona della puntina da disegno.

![Simbolo evidenziato di esportazione della cartella di lavoro](./media/cosmosdb-insights-overview/export.png)

Per espandere o comprimere tutte le visualizzazioni di una cartella di lavoro, selezionare il simbolo di espansione a sinistra del simbolo di esportazione.

![Simbolo evidenziato di espansione della cartella di lavoro](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cache-for-redis"></a>Personalizzare monitoraggio di Azure per cache di Azure per Redis

Poiché questa esperienza è basata sui modelli di cartelle di lavoro di Monitoraggio di Azure, è possibile selezionare **Personalizza** > **Modifica** > **Salva** per salvare una copia della versione modificata di una cartella di lavoro personalizzata.

![Barra dei comandi con l'opzione Personalizza evidenziata](./media/cosmosdb-insights-overview/customize.png)

Le cartelle di lavoro vengono salvate in un gruppo di risorse nella sezione **Report personali** o nella sezione **Report condivisi**. La sezione **Report personali** è disponibile solo per il proprietario. La sezione **Report condivisi** è disponibile per chiunque abbia accesso al gruppo di risorse.

Dopo aver salvato la cartella di lavoro personalizzata, passare alla raccolta di cartelle di lavoro per aprirla.

![Barra dei comandi con l'opzione Raccolta evidenziata](./media/cosmosdb-insights-overview/gallery.png)

## <a name="troubleshooting"></a>Risoluzione dei problemi

Per informazioni aggiuntive sulla risoluzione dei problemi, vedere l'articolo dedicato alla [risoluzione dei problemi](troubleshoot-workbooks.md)di Insights basato sulla cartella di lavoro.

## <a name="next-steps"></a>Passaggi successivi

* Configurare gli [avvisi sulle metriche](../platform/alerts-metric.md) e le [notifiche sull'integrità del servizio](../../service-health/alerts-activity-log-service-notifications-portal.md) per impostare avvisi automatici che facilitano il rilevamento dei problemi.

* Per informazioni sugli scenari supportati dalle cartelle di lavoro, su come creare o personalizzare report e su altro ancora, vedere [Creare report interattivi con le cartelle di lavoro di Monitoraggio di Azure](../platform/workbooks-overview.md).
