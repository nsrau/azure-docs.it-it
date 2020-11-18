---
title: Esercitazione su Log Analytics
description: Questa esercitazione illustra come usare le funzionalità di Log Analytics in Monitoraggio di Azure per compilare ed eseguire una query su log e analizzarne i risultati nel portale di Azure.
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 10/07/2020
ms.openlocfilehash: bd6ba091b3715741af929613600a59511e7cdb0b
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94495364"
---
# <a name="log-analytics-tutorial"></a>Esercitazione su Log Analytics
Log Analytics è uno strumento disponibile nel portale di Azure che consente di modificare ed eseguire query su log dai dati raccolti dai log di Monitoraggio di Azure e analizzarne i risultati in modo interattivo. È possibile usare le query di Log Analytics per recuperare i record che corrispondono a determinati criteri, identificare le tendenze, analizzare i modelli e fornire un'ampia varietà di informazioni dettagliate sui dati. 

Questa esercitazione descrive l'interfaccia di Log Analytics, illustra come iniziare a usare alcune query di base e mostra quali operazioni è possibile eseguire sui risultati. Verranno illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Comprendere lo schema dei dati di log
> * Scrivere ed eseguire query semplici e modificare l'intervallo di tempo per le query
> * Filtrare, ordinare e raggruppare i risultati delle query
> * Visualizzare, modificare e condividere gli oggetti visivi dei risultati delle query
> * Salvare, caricare, esportare e copiare query e risultati

> [!IMPORTANT]
> Questa esercitazione usa le funzionalità di Log Analytics per compilare ed eseguire una query anziché usare la query stessa. Verranno usate le funzionalità di Log Analytics per compilare una query e usare un'altra query di esempio. Quando si è pronti per apprendere la sintassi delle query e iniziare a modificare direttamente la query stessa, passare all'[esercitazione sul linguaggio di query Kusto](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor), che mostra varie query di esempio che è possibile modificare ed eseguire in Log Analytics, sfruttando molte delle funzionalità che verranno illustrate in questa esercitazione.


## <a name="prerequisites"></a>Prerequisiti
Questa esercitazione usa l'[ambiente demo di Log Analytics](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade), che include numerosi dati di esempio che supportano le query di esempio. È anche possibile usare la propria sottoscrizione di Azure, ma in questo caso i dati potrebbero non trovarsi nelle stesse tabelle.

## <a name="open-log-analytics"></a>Aprire Log Analytics
Aprire l'[ambiente demo di Log Analytics](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade) oppure selezionare **Log** dal menu di Monitoraggio di Azure nella sottoscrizione. L'ambito iniziale verrà impostato su un'area di lavoro Log Analytics e quindi la query effettuerà la selezione da tutti i dati nell'area di lavoro. Se si seleziona **Log** dal menu di una risorsa di Azure, l'ambito viene impostato solo sui record di tale risorsa. Per informazioni dettagliate sull'ambito, vedere [Ambito della query su log](scope.md).

È possibile visualizzare l'ambito nell'angolo in alto a sinistra della schermata. Se si usa un ambiente personalizzato, viene visualizzata un'opzione per selezionare un ambito diverso, ma questa opzione non è disponibile nell'ambiente demo.

[![Ambito delle query](media/log-analytics-tutorial/scope.png)](media/log-analytics-tutorial/scope.png#lightbox)

## <a name="table-schema"></a>Schema della tabella
Il lato sinistro della schermata include la scheda **Tabelle** che consente di esaminare le tabelle disponibili nell'ambito corrente. Per impostazione predefinita, queste sono raggruppate per **Soluzione**, ma è possibile modificarne il raggruppamento o filtrarle. 

Espandere la soluzione **Gestione log** e individuare la tabella **AzureActivity** . È possibile espandere la tabella per visualizzarne lo schema oppure passare il puntatore del mouse sul nome per visualizzare informazioni aggiuntive. 

[![Visualizzazione tabelle](media/log-analytics-tutorial/table-details.png)](media/log-analytics-tutorial/table-details.png#lightbox)

Fare clic su **Altre informazioni** per passare al riferimento a tabella che documenta ogni tabella e le relative colonne. Fare clic su **Anteprima dati** per visualizzare rapidamente alcuni record recenti nella tabella. Questa operazione può essere utile per assicurarsi che si tratti dei dati previsti prima di eseguire effettivamente una query con tali dati.

[![Dati di esempio](media/log-analytics-tutorial/sample-data.png)](media/log-analytics-tutorial/sample-data.png#lightbox)

## <a name="write-a-query"></a>Scrivere una query
A questo punto, verrà eseguita la scrittura di una query usando la tabella **AzureActivity**. Fare doppio clic sul nome per aggiungerlo alla finestra della query. È anche possibile digitare direttamente nella finestra e sfruttare la funzionalità IntelliSense per completare i nomi delle tabelle nell'ambito corrente e i comandi KQL.

Questa è la query più semplice che è possibile scrivere. Restituisce solo tutti i record in una tabella. Eseguirla facendo clic sul pulsante **Esegui** o premendo MAIUSC + INVIO con il cursore posizionato in un punto qualsiasi del testo della query.

[![Risultati query](media/log-analytics-tutorial/query-results.png)](media/log-analytics-tutorial/query-results.png#lightbox)

Come si può osservare, sono stati restituiti risultati. Il numero di record restituiti dalla query viene visualizzato nell'angolo in basso a destra. 

## <a name="filter"></a>Filtro

A questo punto, si aggiungerà un filtro alla query per ridurre il numero di record restituiti. Selezionare la scheda **Filtro** nel riquadro a sinistra. Verranno visualizzate colonne diverse nei risultati della query che è possibile usare per filtrare i risultati. I primi valori di tali colonne vengono visualizzati con il numero di record con tale valore. Fare clic su **Amministrativo** in **CategoryValue** e quindi su **Applica ed esegui**. 

[![Riquadro query](media/log-analytics-tutorial/query-pane.png)](media/log-analytics-tutorial/query-pane.png#lightbox)

Viene aggiunga alla query un'istruzione **where** con il valore selezionato. I risultati includono ora solo i record con tale valore, per osservare che il numero di record è stato ridotto.

[![Risultati della query filtrati](media/log-analytics-tutorial/query-results-filter-01.png)](media/log-analytics-tutorial/query-results-filter-01.png#lightbox)


## <a name="time-range"></a>Intervallo di ore
Per tutte le tabelle in un'area di lavoro Log Analytics è presente una colonna denominata **TimeGenerated** che indica l'ora di creazione del record. Tutte le query hanno un intervallo di tempo che limita i risultati ai record con un valore **TimeGenerated** compreso in tale intervallo. L'intervallo di tempo può essere impostato nella query o con il selettore nella parte superiore della schermata.

Per impostazione predefinita, la query restituirà i record delle ultime 24 ore. Selezionare il menu a discesa **Intervallo di tempo** e modificarlo impostandolo su **7 giorni**. Fare di nuovo clic su **Esegui** per restituire i risultati. Si noterà che i risultati vengono restituiti, ma è presente un messaggio che indica che non sono visualizzati tutti i risultati, in quanto Log Analytics può restituire un massimo di 10.000 record e la query ha restituito più record rispetto a questo valore. 

[![Intervallo di tempo](media/log-analytics-tutorial/query-results-max.png)](media/log-analytics-tutorial/query-results-max.png#lightbox)


## <a name="multiple-query-conditions"></a>Più condizioni di query
A questo punto, i risultati verranno ulteriormente ridotti aggiungendo un'altra condizione di filtro. Una query può includere qualsiasi numero di filtri al fine di ottenere esattamente il set di record desiderato. Selezionare **Riuscita** in **ActivityStatusValue** e fare clic su **Applica ed esegui**. 

[![Risultati della query con più filtri](media/log-analytics-tutorial/query-results-filter-02.png)](media/log-analytics-tutorial/query-results-filter-02.png#lightbox)


## <a name="analyze-results"></a>Analizzare i risultati
Oltre a facilitare la scrittura e l'esecuzione di query, Log Analytics fornisce anche le funzionalità necessarie per l'utilizzo dei risultati. Per iniziare, espandere un record per visualizzare i valori per tutte le relative colonne.

[![Espandere il record](media/log-analytics-tutorial/expand-record.png)](media/log-analytics-tutorial/expand-record.png#lightbox)

Fare clic sul nome di una colonna per ordinare i risultati in base a tale colonna. Fare clic sull'icona del filtro accanto per specificare una condizione di filtro. Questa operazione è simile all'aggiunta di una condizione di filtro alla query stessa, con la differenza che questo filtro viene cancellato se la query viene eseguita di nuovo. Usare questo metodo per analizzare rapidamente un set di record come parte dell'analisi interattiva.

Ad esempio, impostare un filtro nella colonna **CallerIpAddress** per limitare i record a un singolo chiamante. 

[![Filtro dei risultati della query](media/log-analytics-tutorial/query-results-filter.png)](media/log-analytics-tutorial/query-results-filter.png#lightbox)

Anziché filtrare i risultati, è possibile raggruppare i record in base a una colonna specifica. Deselezionare il filtro appena creato e quindi attivare il dispositivo di scorrimento **Raggruppa colonne**. 

[![Raggruppa colonne](media/log-analytics-tutorial/query-results-group-columns.png)](media/log-analytics-tutorial/query-results-group-columns.png#lightbox)

Trascinare quindi la colonna **CallerIpAddress** nella riga di raggruppamento. I risultati sono ora organizzati in base a tale colonna ed è possibile comprimere ogni gruppo per semplificare l'analisi.

[![Risultati della query raggruppati](media/log-analytics-tutorial/query-results-grouped.png)](media/log-analytics-tutorial/query-results-grouped.png#lightbox)

## <a name="work-with-charts"></a>Usare i grafici
Verrà ora esaminata una query che usa dati numerici che è possibile visualizzare in un grafico. Anziché creare una query, verrà selezionata una query di esempio.

Nel riquadro a sinistro fare clic su **Query**. In questo riquadro sono incluse le query di esempio che è possibile aggiungere alla finestra Query. Se si usa un'area di lavoro personalizzata, sarà presente una serie di query in più categorie, ma se si usa l'ambiente demo, verrà visualizzata una sola categoria **Aree di lavoro Log Analytics**. Espanderla per visualizzare le query nella categoria.

Fare clic sulla query denominata **Request Count by ResponseCode** (Numero di richieste per codice di risposta). La query verrà aggiunta alla finestra Query. Si noti che la nuova query è separata dall'altra con una riga vuota. Una query in KQL termina quando viene rilevata una riga vuota, quindi vengono visualizzate come query separate. 

[![Nuova query](media/log-analytics-tutorial/example-query.png)](media/log-analytics-tutorial/example-query.png#lightbox)

La query corrente corrisponde a quella su cui è posizionato il cursore. Come si può notare, la prima query è evidenziata per indicare che si tratta della query corrente. Fare clic in un punto qualsiasi della nuova query per selezionarla e quindi fare clic sul pulsante **Esegui** per eseguirla.

[![Grafico dei risultati della query](media/log-analytics-tutorial/example-query-output-chart.png)](media/log-analytics-tutorial/example-query-output-chart.png#lightbox)

Si noti che questo output è un grafico anziché una tabella come l'ultima query. Questo perché alla fine della query di esempio è presente un comando [render](/azure/data-explorer/kusto/query/renderoperator?pivots=azuremonitor). Si noti che sono disponibili diverse opzioni per l'utilizzo del grafico, ad esempio per modificarlo in un altro tipo.

Provare a selezionare **Risultati** per visualizzare l'output della query sotto forma di tabella. 

[![Tabella dei risultati della query](media/log-analytics-tutorial/example-query-output-table.png)](media/log-analytics-tutorial/example-query-output-table.png#lightbox)



## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come usare Log Analytics, completare l'esercitazione sull'uso delle query su log.
> [!div class="nextstepaction"]
> [Scrivere query di log di Monitoraggio di Azure](get-started-queries.md)
