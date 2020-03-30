---
title: Monitorare Azure Cosmos DB con Monitor di Azure Monitor per Cosmos DB (anteprima) Documenti Microsoft
description: Questo articolo descrive la funzionalità Monitor di Azure per Cosmos DB che fornisce ai proprietari di Cosmos DB una rapida comprensione dei problemi di prestazioni e utilizzo con i relativi account CosmosDB.
ms.subservice: ''
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/27/2019
ms.openlocfilehash: 9a900a2f2e950fe9b9846ebcc047d7c344284948
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250684"
---
# <a name="explore-azure-monitor-for-azure-cosmos-db-preview"></a>Esplora Monitoraggio di Azure per Azure Cosmos DB (anteprima)Explore Azure Monitor for Azure Cosmos DB (preview)

Monitoraggio di Azure per Azure Cosmos DB (anteprima) offre una visualizzazione delle prestazioni complessive, degli errori, della capacità e dell'integrità operativa di tutte le risorse del database Cosmos di Azure in un'esperienza interattiva unificata. Questo articolo ti aiuterà a comprendere i vantaggi di questa nuova esperienza di monitoraggio e come puoi modificare e adattare l'esperienza in base alle esigenze specifiche della tua organizzazione.   

## <a name="introduction"></a>Introduzione

Prima di immergersi nell'esperienza, dovresti capire come presenta e visualizza le informazioni. 

Offre:

* **In prospettiva di scala** delle risorse di Azure Cosmos DB in tutte le sottoscrizioni in un'unica posizione, con la possibilità di definire selettivamente l'ambito solo delle sottoscrizioni e delle risorse che si è interessati a valutare.

* **Eseguire il drill-down dell'analisi** di una determinata risorsa CosmosDB di Azure per diagnosticare i problemi o eseguire un'analisi dettagliata per categoria: utilizzo, errori, capacità e operazioni. La selezione di una di queste opzioni offre una visualizzazione approfondita delle metriche di Azure Cosmos DB pertinenti.  

* **Personalizzabile:** questa esperienza si basa sui modelli di cartella di lavoro di Monitoraggio di Azure che consentono di modificare le metriche visualizzate, modificare o impostare soglie in linea con i limiti e quindi salvare in una cartella di lavoro personalizzata. I grafici nelle cartelle di lavoro possono quindi essere aggiunti ai dashboard di Azure.Charts in the workbooks can then be pinned to Azure dashboards.  

Questa funzionalità non richiede l'abilitazione o la configurazione di nulla, queste metriche di Azure Cosmos DB vengono raccolte per impostazione predefinita.

>[!NOTE]
>Non è previsto alcun costo per accedere a questa funzionalità e verranno addebitati solo le funzionalità essenziali di Monitoraggio di Azure configurate o abilitate, come descritto nella pagina dei dettagli sui prezzi di Monitoraggio di Azure.There is no charge to access this feature and you will be only charged for the Azure Monitor essential features you configure or enable, as described on the [Azure Monitor pricing details](https://azure.microsoft.com/pricing/details/monitor/) page.

## <a name="view-utilization-and-performance-metrics-for-azure-cosmos-db"></a>Visualizzare le metriche di utilizzo e prestazioni per Azure Cosmos DBView utilization and performance metrics for Azure Cosmos DB

Per visualizzare l'utilizzo e le prestazioni degli account di archiviazione in tutte le sottoscrizioni, eseguire la procedura seguente.

1. Accedere al [portale](https://portal.azure.com)di Azure .

2. Cercare **Monitor** e selezionare **Monitor**.

    ![Casella di ricerca con la parola "Monitor" e un menu a discesa che dice Servizi "Monitor" con un'immagine in stile tachimetro](./media/cosmosdb-insights-overview/search-monitor.png)

3. Selezionare **Cosmos DB (anteprima)**.

    ![Screenshot della cartella di lavoro di panoramica di Cosmos DB](./media/cosmosdb-insights-overview/cosmos-db.png)

### <a name="overview"></a>Panoramica

In **Panoramica**, la tabella visualizza le metriche interattive di Azure Cosmos DB. È possibile filtrare i risultati in base alle opzioni selezionate dai seguenti elenchi a discesa:

* **Sottoscrizioni:** vengono elencate solo le sottoscrizioni con una risorsa di database Cosmos di Azure.Subscriptions - only subscriptions that have an Azure Cosmos DB resource are listed.  

* **Cosmos DB:** è possibile selezionare tutto, un sottoinsieme o una singola risorsa di Azure Cosmos DB.

* **Intervallo** di tempo - per impostazione predefinita, visualizza le ultime 4 ore di informazioni in base alle selezioni corrispondenti effettuate.

Il riquadro del contatore sotto gli elenchi a discesa esegue il rollup del numero totale di risorse di Azure Cosmos DB nelle sottoscrizioni selezionate. Esistono mappe colore o heatmap condizionali per le colonne nella cartella di lavoro che segnalano le metriche delle transazioni. Il colore più profondo ha il valore più alto e un colore più chiaro si basa sui valori più bassi. 

Se si seleziona una freccia a discesa accanto a una delle risorse di Azure Cosmos DB, verrà visualizzata una suddivisione delle metriche delle prestazioni a livello di singolo contenitore di database:Selecting a drop-down arrow next to one of the Azure Cosmos DB resources will reveal a breakdown of the performance metrics at the individual database container level:

![Elenco a discesa espanso che mostra singoli contenitori di database e la suddivisione delle prestazioni associata](./media/cosmosdb-insights-overview/container-view.png)

Se si seleziona il nome della risorsa Azure Cosmos DB evidenziato in blu, si passa alla **panoramica** predefinita per l'account database Cosmos di Azure associato. 

### <a name="failures"></a>Errori

Selezionare **Errori** nella parte superiore della pagina per aprire la parte **Errori** del modello di cartella di lavoro. Mostra le richieste totali con la distribuzione delle risposte che costituiscono tali richieste:

![Schermata degli errori con suddivisione per tipo di richiesta HTTP](./media/cosmosdb-insights-overview/failures.png)

| Codice      |  Descrizione       | 
|-----------|:--------------------|
| `200 OK`  | Una delle operazioni REST seguenti è riuscita: </br>- GET su una risorsa. </br> - PUT su una risorsa. </br> - POST su una risorsa. </br> - POST su una risorsa stored procedure per eseguire la stored procedure.|
| `201 Created` | Un'operazione POST per la creazione di una risorsa è riuscita. |
| `404 Not Found` | L'operazione tenta di agire su una risorsa che non esiste più. È ad esempio possibile che la risorsa sia già stata eliminata. |

Per un elenco completo dei codici di stato, consultare l'articolo codice di [stato HTTP di Azure Cosmos DB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

### <a name="capacity"></a>Capacity

Selezionare **Capacità** nella parte superiore della pagina per aprire la parte **Capacità** del modello di cartella di lavoro. Mostra quanti documenti sono presenti, la crescita del documento nel tempo, l'utilizzo dei dati e la quantità totale di spazio di archiviazione disponibile.  Può essere utilizzato per identificare potenziali problemi di archiviazione e utilizzo dei dati.

![Cartella di lavoro Capacità](./media/cosmosdb-insights-overview/capacity.png) 

Come per la cartella di lavoro di panoramica, la selezione dell'elenco a discesa accanto a una risorsa di Azure Cosmos DB nella colonna **Sottoscrizione** rivelerà una suddivisione in base ai singoli contenitori che costituiscono il database.

### <a name="operations"></a>Operazioni 

Selezionare **Operazioni** nella parte superiore della pagina per aprire la parte **Operazioni** del modello di cartella di lavoro. Ti dà la possibilità di vedere le vostre richieste suddivise per il tipo di richieste effettuate. 

Quindi, nell'esempio qui `eastus-billingint` sotto si vede che sta principalmente ricevendo richieste di lettura, ma con un piccolo numero di upsert e creare richieste. Mentre `westeurope-billingint` è di sola lettura dal punto di vista della richiesta, almeno nelle ultime quattro ore in cui la cartella di lavoro è attualmente con ambito tramite il relativo parametro di intervallo di tempo.

![Cartella di lavoro Operazioni](./media/cosmosdb-insights-overview/operation.png) 

## <a name="pin-export-and-expand"></a>Aggiungere, esportare ed espandere

È possibile aggiungere una qualsiasi delle sezioni metriche a un dashboard di [Azure](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) selezionando l'icona a forma di puntina da aggiungere nella parte superiore destra della sezione.

![Esempio di pin della sezione metrica all'esempio di dashboard](./media/cosmosdb-insights-overview/pin.png)

Per esportare i dati nel formato Excel, selezionare l'icona della freccia rivolta verso il basso a sinistra dell'icona a forma di puntina da dominio.

![Icona Esporta cartella di lavoro](./media/cosmosdb-insights-overview/export.png)

Per espandere o comprimere tutte le visualizzazioni a discesa nella cartella di lavoro, selezionare l'icona di espansione a sinistra dell'icona di esportazione:

![Icona Espandi cartella di lavoro](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cosmos-db-preview"></a>Personalizzare Azure Monitor per Azure Cosmos DB (anteprima)Customize Azure Monitor for Azure Cosmos DB (preview)

Poiché questa esperienza si basa sui modelli di cartella di lavoro di Monitoraggio di Azure, è possibile **personalizzare** > **Modifica** e **salvare** una copia della versione modificata in una cartella di lavoro personalizzata. 

![Personalizzare la barra](./media/cosmosdb-insights-overview/customize.png)

Le cartelle di lavoro vengono salvate all'interno di un gruppo di risorse, nella sezione **Report** personali privata o nella sezione **Report condivisi** accessibile a tutti gli utenti con accesso al gruppo di risorse. Dopo aver salvato la cartella di lavoro personalizzata, è necessario passare alla raccolta di cartelle di lavoro per avviarla.

![Avviare la raccolta di cartelle di lavoro dalla barra dei comandi](./media/cosmosdb-insights-overview/gallery.png)

## <a name="next-steps"></a>Passaggi successivi

* Configurare [gli avvisi di metrica](../platform/alerts-metric.md) e [le notifiche](../../service-health/alerts-activity-log-service-notifications.md) di integrità dei servizi per configurare gli avvisi automatici per facilitare il rilevamento dei problemi.

* Informazioni sugli scenari che le cartelle di lavoro sono progettate per supportare, su come creare report nuovi e personalizzarle esistenti e altro ancora esaminando la revisione di [Creare report interattivi con cartelle di lavoro](../app/usage-workbooks.md)di Monitoraggio di Azure.
