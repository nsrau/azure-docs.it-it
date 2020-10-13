---
title: Monitorare Azure Cosmos DB con Monitoraggio di Azure per Cosmos DB | Microsoft Docs
description: L'articolo descrive la funzionalità Monitoraggio di Azure per Cosmos DB che consente ai proprietari di Cosmos DB di comprendere rapidamente problemi di prestazioni e uso con gli account Cosmos DB.
author: mrbullwinkle
ms.author: mbullwin
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: dcd64cf779b931dbf3b4f743844071835e0b8c91
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91932057"
---
# <a name="explore-azure-monitor-for-azure-cosmos-db"></a>Esplorare Monitoraggio di Azure per Azure Cosmos DB

Monitoraggio di Azure per Azure Cosmos DB offre una visualizzazione delle prestazioni complessive, degli errori, della capacità e dell'integrità operativa di tutte le risorse Azure Cosmos DB in un'esperienza interattiva unificata. Questo articolo consente di comprendere i vantaggi di questa nuova esperienza di monitoraggio e di come è possibile modificarla e adattarla per soddisfare le esigenze specifiche dell'organizzazione.   

## <a name="introduction"></a>Introduzione

Prima di approfondire l'esperienza è necessario comprendere il modo in cui le informazioni vengono presentate e visualizzate. 

Offre:

* **Prospettiva su larga scala** delle risorse di Azure Cosmos DB in tutte le sottoscrizioni in un'unica posizione, con la possibilità di definire l'ambito in modo selettivo solo per le sottoscrizioni e le risorse che si vogliono valutare.

* **Analisi del drill-down** di una particolare risorsa di Azure CosmosDB per diagnosticare problemi o eseguire analisi dettagliate per categorie, come uso, errori, capacità e operazioni. Selezionando una delle opzioni si ottiene una visualizzazione di profondità delle metriche Azure Cosmos DB rilevanti.  

* **Personalizzabile**: questa esperienza è basata sui modelli delle cartelle di lavoro di Monitoraggio di Azure che consentono di modificare le metriche visualizzate, modificare o impostare soglie allineate ai limiti e quindi salvarle in una cartella di lavoro personalizzata. I grafici nelle cartelle di lavoro possono quindi essere aggiunti ai dashboard di Azure.  

Per questa funzionalità non è necessario abilitare o configurare elementi, queste metriche di Azure Cosmos DB vengono raccolte per impostazione predefinita.

>[!NOTE]
>Non sono previsti addebiti per accedere a questa funzionalità. Verranno addebitate solo le funzionalità essenziali di Monitoraggio di Azure configurate o abilitate, come descritto nella pagina relativa ai [dettagli sui prezzi di Monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="view-utilization-and-performance-metrics-for-azure-cosmos-db"></a>Visualizzare le metriche di uso e prestazioni per Azure Cosmos DB

Per visualizzare le prestazioni e l'uso degli account di archiviazione in tutte le sottoscrizioni, attenersi alla procedura seguente.

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Cercare **Monitoraggio** e selezionare **Monitora**.

    ![Casella di ricerca con la parola "Monitoraggio" e menu a discesa che indica "Monitoraggio" dei servizi con un'immagine in stile tachimetro](./media/cosmosdb-insights-overview/search-monitor.png)

3. Selezionare **Cosmos DB**.

    ![Screenshot della cartella di lavoro panoramica di Cosmos DB](./media/cosmosdb-insights-overview/cosmos-db.png)

### <a name="overview"></a>Panoramica

In **Panoramica** la tabella visualizza le metriche interattive di Azure Cosmos DB. È possibile filtrare i risultati in base alle opzioni selezionate dagli elenchi a discesa seguenti:

* **Sottoscrizioni**: sono elencate solo le sottoscrizioni con una risorsa Azure Cosmos DB.  

* **Cosmos DB**: è possibile selezionare tutte le risorse di Azure Cosmos DB, un subset o una singola risorsa.

* **Intervallo di tempo**: per impostazione predefinita, visualizza le ultime 4 ore di informazioni in base alle selezioni corrispondenti effettuate.

Il riquadro del contatore sotto gli elenchi a discesa esegue il rollup del numero totale di risorse di Azure Cosmos DB incluse nelle sottoscrizioni selezionate. Per le colonne nella cartella di lavoro che riportano metriche di transazioni sono previste una codifica con colori condizionale o le mappe termiche. Il colore più profondo presenta il valore più elevato, mentre un colore più chiaro deriva da valori più bassi. 

Selezionando la freccia dell'elenco a discesa di una delle risorse di Azure Cosmos DB viene visualizzato il dettaglio delle metriche di prestazioni al livello del singolo contenitore di database:

![Elenco a discesa espanso che mostra i singoli contenitori di database e il dettaglio delle prestazioni associato](./media/cosmosdb-insights-overview/container-view.png)

Selezionando il nome della risorsa di Azure Cosmos DB evidenziato in blu si accede alla pagina **Panoramica** predefinita dell'account Azure Cosmos DB associato. 

### <a name="failures"></a>Errori

Selezionare **Errori** nella parte superiore della pagina; si apre la sezione **Errori** del modello della cartella di lavoro. Mostra le richieste totali con la distribuzione delle risposte che compongono tali richieste:

![Screenshot degli errori con dettaglio per tipo di richiesta HTTP](./media/cosmosdb-insights-overview/failures.png)

| Codice      |  Descrizione       | 
|-----------|:--------------------|
| `200 OK`  | Una delle operazioni REST seguenti ha avuto esito positivo: </br>- GET su una risorsa. </br> - PUT su una risorsa. </br> - POST su una risorsa. </br> - POST su una risorsa di stored procedure per eseguire la stored procedure.|
| `201 Created` | Un'operazione POST per creare una risorsa ha esito positivo. |
| `404 Not Found` | L'operazione tenta di agire su una risorsa che non esiste più. Ad esempio, è possibile che la risorsa sia stata già eliminata. |

Per un elenco completo dei codici di stato, consulta l'articolo [Codice di stato HTTP in Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

### <a name="capacity"></a>Capacità

Selezionare **Capacità** nella parte superiore della pagina; si apre la sezione **Capacità** del modello della cartella di lavoro. Mostra il numero di documenti a disposizione, la relativa crescita nel tempo, l'uso dei dati e la quantità totale di risorse di archiviazione ancora disponibili.  Questo dato può essere usato per identificare potenziali problemi di archiviazione e di uso dei dati.

![Cartella di lavoro Capacità](./media/cosmosdb-insights-overview/capacity.png) 

Come per la cartella di lavoro Panoramica, selezionando l'elenco a discesa accanto a una risorsa di Azure Cosmos DB nella colonna **Sottoscrizione** viene mostrato il dettaglio in base ai singoli contenitori che costituiscono il database.

### <a name="operations"></a>Operazioni 

Selezionare **Operazioni** nella parte superiore della pagina; si apre la sezione **Operazioni** del modello della cartella di lavoro. Consente di visualizzare le richieste suddivise per tipo di richiesta effettuata. 

Nell'esempio seguente è possibile notare che `eastus-billingint` riceve principalmente richieste di lettura, con un numero ridotto di richieste di upsert e creazione. Mentre `westeurope-billingint` è di sola lettura dal punto di vista della richiesta, almeno nelle ultime quattro ore in cui la cartella di lavoro è attualmente definita come ambito tramite il parametro intervallo di tempo.

![Cartella di lavoro Operazioni](./media/cosmosdb-insights-overview/operation.png) 

## <a name="pin-export-and-expand"></a>Aggiungere, esportare ed espandere

È possibile aggiungere una qualsiasi delle sezioni della metrica a un [dashboard di Azure](../../azure-portal/azure-portal-dashboards.md) selezionando l'icona a forma di puntina da disegno in alto a destra nella sezione.

![Esempio di aggiunta della sezione metrica al dashboard](./media/cosmosdb-insights-overview/pin.png)

Per esportare i dati in formato Excel, selezionare l'icona con la freccia in basso a sinistra dell'icona a forma di puntina da disegno.

![Icona Esporta cartella di lavoro](./media/cosmosdb-insights-overview/export.png)

Per espandere o comprimere tutte le viste a discesa nella cartella di lavoro, selezionare l'icona per espandere a sinistra dell'icona di esportazione:

![Icona Espandi cartella di lavoro](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cosmos-db"></a>Personalizzare Monitoraggio di Azure per Azure Cosmos DB

Poiché questa esperienza è basata sui modelli delle cartelle di lavoro di Monitoraggio di Azure, è possibile **Personalizzare** > **modificare** e **salvare** una copia della versione modificata in una cartella di lavoro personalizzata. 

![Barra di personalizzazione](./media/cosmosdb-insights-overview/customize.png)

Le cartelle di lavoro vengono salvate all'interno di un gruppo di risorse, ovvero nella sezione **Report personali** privata o nella sezione **Report condivisi** accessibile a tutti gli utenti che hanno accesso al gruppo di risorse. Dopo aver salvato la cartella di lavoro personalizzata, per avviarla è necessario passare alla raccolta di cartelle di lavoro.

![Avviare la raccolta di cartelle di lavoro dalla barra dei comandi](./media/cosmosdb-insights-overview/gallery.png)

## <a name="troubleshooting"></a>Risoluzione dei problemi

Per informazioni aggiuntive sulla risoluzione dei problemi, vedere l'articolo dedicato alla [risoluzione dei problemi](troubleshoot-workbooks.md)di Insights basato sulla cartella di lavoro.

## <a name="next-steps"></a>Passaggi successivi

* Configurare gli [avvisi sulle metriche](../platform/alerts-metric.md) e le [notifiche sull'integrità del servizio](../../service-health/alerts-activity-log-service-notifications-portal.md) per impostare avvisi automatici che facilitano il rilevamento dei problemi.

* Informazioni sugli scenari che le cartelle di lavoro sono progettate per supportare, su come creare nuovi report e personalizzare report esistenti e altro ancora sono disponibili nell'articolo [Creare report interattivi con le cartelle di lavoro di Monitoraggio di Azure](../platform/workbooks-overview.md).
