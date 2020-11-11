---
title: Panoramica delle Log Analytics di monitoraggio di Azure
description: Viene descritto Log Analytics che è uno strumento della portale di Azure usato per modificare ed eseguire query di log per l'analisi dei dati nei log di monitoraggio di Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/04/2020
ms.openlocfilehash: 26d6bcb52099b15aeeb73a36a7144c14bdf481d6
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94496937"
---
# <a name="overview-of-log-analytics-in-azure-monitor"></a>Panoramica delle Log Analytics di monitoraggio di Azure
Log Analytics è uno strumento della portale di Azure usato per modificare ed eseguire query di log con dati nei log di monitoraggio di Azure. È possibile scrivere una query semplice che restituisce un set di record e quindi usare le funzionalità di Log Analytics per ordinarle, filtrarle e analizzarle. In alternativa, è possibile scrivere una query più avanzata per eseguire un'analisi statistica e visualizzare i risultati in un grafico per identificare una determinata tendenza. Se si lavora con i risultati delle query in modo interattivo o si usano le altre funzionalità di monitoraggio di Azure, ad esempio gli avvisi o le cartelle di lavoro di query di log, Log Analytics è lo strumento che verrà usato per la scrittura e il test. 


> [!TIP]
> In questo articolo viene fornita una descrizione di Log Analytics e di ognuna delle relative funzionalità. Se si vuole passare direttamente a un'esercitazione, vedere [log Analytics esercitazione](get-started-portal.md).



## <a name="starting-log-analytics"></a>Avvio Log Analytics
Avviare Log Analytics dai **log** nel menu **monitoraggio di Azure** nel portale di Azure. Questa opzione sarà visualizzata anche nel menu per la maggior parte delle risorse di Azure. Indipendentemente dalla posizione in cui viene avviata, sarà lo stesso strumento Log Analytics. Il menu utilizzato per avviare Log Analytics determina i dati che saranno disponibili. Se lo si avvia dal menu **monitoraggio di Azure** o dal menu **aree di lavoro log Analytics** , sarà possibile accedere a tutti i record in un'area di lavoro. Se si selezionano i **log** da un altro tipo di risorsa, i dati saranno limitati ai dati di log per tale risorsa. Per i dettagli, vedere [Ambito e intervallo di tempo delle query su log in Log Analytics di Monitoraggio di Azure](scope.md).

[![Avvia Log Analytics](media/log-analytics-overview/start-log-analytics.png)](media/log-analytics-overview/start-log-analytics.png#lightbox)

Quando si avvia Log Analytics, viene visualizzata una finestra di dialogo con [query di esempio](example-queries.md). Questi sono suddivisi in categorie per soluzione ed è possibile cercare le query che soddisfano i requisiti specifici. Potrebbe essere possibile trovare un che esegue esattamente le operazioni necessarie o caricarne uno nell'editor e modificarlo come richiesto. L'esplorazione delle query di esempio è in realtà un ottimo modo per apprendere come scrivere query personalizzate. Naturalmente, se si vuole iniziare con uno script vuoto e scriverlo manualmente, è possibile chiudere le query di esempio. È sufficiente fare clic sulle **query** nella parte superiore della schermata se si desidera ottenerle nuovamente.

## <a name="log-analytics-interface"></a>Interfaccia Log Analytics
Nell'immagine seguente vengono identificati i diversi componenti di Log Analytics.

[![Log Analytics](media/log-analytics-overview/log-analytics.png)](media/log-analytics-overview/log-analytics.png#lightbox)

### <a name="1-top-action-bar"></a>1. barra delle azioni superiore
Controlli per l'utilizzo della query nella finestra query.

| Opzione | Descrizione |
|:---|:---|
| Ambito | Specifica l'ambito dei dati utilizzati per la query. Potrebbe trattarsi di tutti i dati in un'area di lavoro Log Analytics o dati per una determinata risorsa in più aree di lavoro. Vedere [ambito della query](scope.md). |
| Pulsante Esegui | Fare clic su questo pulsante per eseguire la query selezionata nella finestra query. Per eseguire una query, è anche possibile premere MAIUSC + INVIO. |
| Controllo di selezione di data e ora | Consente di selezionare l'intervallo di tempo per i dati disponibili per la query. Questa operazione viene sottoposta a override se si include un filtro temporale nella query. Vedere l' [ambito della query di log e l'intervallo di tempo in monitoraggio di Azure log Analytics](scope.md). |
| Pulsante per il salvataggio | Salvare la query in Esplora query per l'area di lavoro. |
 Pulsante Copia | Copiare un collegamento alla query, il testo della query o i risultati della query negli Appunti. |
| Pulsante nuova regola di avviso | Crea una nuova scheda con una query vuota. |
| Pulsante Esporta | Esportare i risultati della query in un file CSV o nella query per Power Query formato di linguaggio delle formule per l'uso con Power bi. |
| Pulsante Aggiungi al dashboard | Aggiungere i risultati della query a un dashboard di Azure. |
| Pulsante Formatta query | Dispone il testo selezionato per migliorare la leggibilità. |
| Pulsante query di esempio | Aprire la finestra di dialogo query di esempio visualizzata quando si apre per la prima volta Log Analytics. |
| Pulsante Esplora query | Aprire **Esplora query** che consente di accedere alle query salvate nell'area di lavoro. |


### <a name="2-sidebar"></a>2. sidebar
Elenchi di tabelle nell'area di lavoro, query di esempio e opzioni di filtro per la query corrente.

| Scheda | Descrizione |
|:---|:---|
| Tabelle | Elenca le tabelle che fanno parte dell'ambito selezionato. Selezionare **raggruppa** per per modificare il raggruppamento delle tabelle. Passare il puntatore del mouse su un nome di tabella per visualizzare una finestra di dialogo con una descrizione della tabella e delle opzioni per visualizzare la relativa documentazione e visualizzare un'anteprima dei dati. Espandere una tabella per visualizzare le relative colonne. Fare doppio clic sul nome di una tabella o di una colonna per aggiungerlo alla query. |
| Query | Elenco di query di esempio che è possibile aprire nella finestra query. Si tratta dello stesso elenco visualizzato quando si apre Log Analytics. Selezionare **raggruppa** per per modificare il raggruppamento delle query. Fare doppio clic su una query per aggiungerla alla finestra query o passare il puntatore del mouse su di essa per altre opzioni. |
| Filtra | Crea opzioni di filtro in base ai risultati di una query. Dopo l'esecuzione di una query, le colonne verranno visualizzate con valori diversi dai risultati. Selezionare uno o più valori, quindi fare clic su **applica & Esegui** per aggiungere un comando **where** alla query ed eseguirlo di nuovo. |

### <a name="3-query-window"></a>3. finestra query
La finestra di query è la posizione in cui si modifica la query. Sono inclusi IntelliSense per i comandi KQL e la codifica a colori per migliorare la leggibilità. Fare clic su nella **+** parte superiore della finestra per aprire un'altra scheda.

Poiché una singola finestra può includere più query. Una query non può includere righe vuote, quindi è possibile separare più query in una finestra con una o più righe vuote. La query corrente è quella con il cursore posizionato in un punto qualsiasi all'interno.

Per eseguire la query corrente, fare clic sul pulsante **Esegui** o premere MAIUSC + INVIO.

### <a name="4-results-window"></a>4. finestra risultati
I risultati della query vengono visualizzati nella finestra risultati. Per impostazione predefinita, i risultati vengono visualizzati come una tabella. Per visualizzare un grafico, selezionare **grafico** nella finestra risultati o aggiungere un comando **Render** alla query.

#### <a name="results-view"></a>visualizzazione Risultati
Visualizza i risultati della query in una tabella organizzata in base alle colonne e alle righe. Fare clic a sinistra di una riga per espanderne i valori. Fare clic sull'elenco a discesa **Columns** per modificare l'elenco di colonne. Per ordinare i risultati, fare clic sul nome di una colonna. Filtrare i risultati facendo clic sull'imbuto accanto al nome di una colonna. Cancellare i filtri e reimpostare l'ordinamento eseguendo di nuovo la query.

Selezionare le **colonne del gruppo** per visualizzare la barra di raggruppamento sopra i risultati della query. Raggruppare i risultati in base a qualsiasi colonna trascinandolo sulla barra. Creare gruppi annidati nei risultati aggiungendo colonne aggiuntive. 

#### <a name="chart-view"></a>Visualizzazione grafico
Consente di visualizzare i risultati in uno dei più tipi di grafico disponibili. È possibile specificare il tipo di grafico in un comando **Render** nella query o selezionarlo dall'elenco a discesa **tipo di visualizzazione** .

| Opzione | Descrizione |
|:---|:---|
| **Tipo di visualizzazione** | Tipo di grafico da visualizzare. |
| **Asse X** | Colonna nei risultati da utilizzare per l'asse X 
| **Asse Y** | Colonna nei risultati da utilizzare per l'asse Y. Si tratta in genere di una colonna numerica. |
| **Dividi per** | Colonna nei risultati che definisce la serie nel grafico. Viene creata una serie per ogni valore nella colonna. |
| **Aggregazione** | Tipo di aggregazione da eseguire sui valori numerici nell'asse Y. |

## <a name="relationship-to-azure-data-explorer"></a>Relazione con Esplora dati di Azure
Se si ha già familiarità con l'interfaccia utente Web di Azure Esplora dati, Log Analytics dovrebbe avere un aspetto familiare. Questo perché si basa su Azure Esplora dati e usa lo stesso linguaggio di query kusto (KQL). Log Analytics aggiunge funzionalità specifiche di monitoraggio di Azure, ad esempio l'applicazione di filtri in base all'intervallo di tempo e la possibilità di creare una regola di avviso da una query. Entrambi gli strumenti includono un visualizzatore che consente di analizzare la struttura delle tabelle disponibili, ma l'interfaccia utente Web di Azure Esplora dati funziona principalmente con le tabelle nei database Esplora dati di Azure, mentre Log Analytics funziona con le tabelle in un'area di lavoro Log Analytics. 

## <a name="next-steps"></a>Passaggi successivi
- Seguire un'[esercitazione sull'uso di Log Analytics nel portale di Azure](get-started-portal.md).
- Seguire un'[esercitazione sulla scrittura di query](get-started-queries.md).
