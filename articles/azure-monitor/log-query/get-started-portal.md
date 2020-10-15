---
title: 'Esercitazione: Introduzione alle query di Log Analytics'
description: In questa esercitazione si apprenderà come scrivere e gestire le query di log di Monitoraggio di Azure usando Log Analytics nel portale di Azure.
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 03/17/2020
ms.openlocfilehash: b337065f125d12e309dd1f7fcc56c2af72b1c28c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90088358"
---
# <a name="tutorial-get-started-with-log-analytics-queries"></a>Esercitazione: Introduzione alle query di Log Analytics

Questa esercitazione illustra come usare Log Analytics per scrivere, eseguire e gestire le query di log di Monitoraggio di Azure nel portale di Azure. È possibile usare le query di Log Analytics per cercare termini, identificare le tendenze, analizzare i modelli e fornire molte altre informazioni dettagliate dai dati. 

In questa esercitazione si apprenderà come usare Log Analytics per:

> [!div class="checklist"]
> * Comprendere lo schema dei dati di log
> * Scrivere ed eseguire query semplici e modificare l'intervallo di tempo per le query
> * Filtrare, ordinare e raggruppare i risultati delle query
> * Visualizzare, modificare e condividere gli oggetti visivi dei risultati delle query
> * Salvare, caricare, esportare e copiare query e risultati

Per altre informazioni sulle query di log, vedere [Panoramica delle query di log in Monitoraggio di Azure](log-query-overview.md).<br/>
Per un'esercitazione dettagliata sulla scrittura di query di log, vedere [Introduzione alle query di log in Monitoraggio di Azure](get-started-queries.md).

## <a name="open-log-analytics"></a>Aprire Log Analytics
Per usare Log Analytics, è necessario aver eseguito l'accesso a un account Azure. Se non si ha un account Azure, è possibile [crearne uno gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Per completare la maggior parte dei passaggi di questa esercitazione, è possibile usare [questo ambiente demo](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade), che include numerosi dati di esempio. Con l'ambiente demo, non sarà possibile salvare le query o aggiungere i risultati a un dashboard.

È anche possibile usare il proprio ambiente, se si usa Monitoraggio di Azure per raccogliere i dati di log in almeno una risorsa di Azure. Per aprire un'area di lavoro Log Analytics, selezionare **Log** nel riquadro di spostamento sinistro di Monitoraggio di Azure. 

## <a name="understand-the-schema"></a>Informazioni sullo schema
 
Uno *schema* è una raccolta di tabelle raggruppate in categorie logiche. Lo schema dimostrativo presenta diverse categorie che provengono dalle soluzioni di monitoraggio. Ad esempio, la categoria **LogManagement** contiene eventi di Windows e Syslog, dati sulle prestazioni ed eventi heartbeat degli agenti.

Le tabelle dello schema vengono visualizzate nella scheda **Tabelle** dell'area di lavoro Log Analytics. Le tabelle contengono le colonne, ognuna con un tipo di dati visualizzato dall'icona accanto al nome della colonna. Ad esempio, la tabella **Event** contiene colonne di testo come **Computer** e colonne numeriche come **EventCategory**.

![Screenshot che mostra la pagina Log del portale di Azure con una nuova query, evidenziando il riquadro Tabelle con Computer ed EventCategory evidenziati.](media/get-started-portal/schema.png)

## <a name="write-and-run-basic-queries"></a>Scrivere ed eseguire query di base

Log Analytics viene aperto con una nuova query vuota nell'**Editor di query**.

![Log Analytics](media/get-started-portal/homepage.png)

### <a name="write-a-query"></a>Scrivere una query

Le query di log di Monitoraggio di Azure usano una versione del linguaggio di query Kusto. Le query possono iniziare con un nome di tabella o un comando [search](/azure/kusto/query/searchoperator). 

La query seguente recupera tutti i record dalla tabella **Event**:

```Kusto
Event
```

Il carattere di barra verticale, o pipe, (|) separa i comandi, in modo che l'output del primo comando sia l'input del comando successivo. È possibile aggiungere qualsiasi numero di comandi a una singola query. La query seguente recupera i record dalla tabella **Event** e quindi cerca il termine **error** in qualsiasi proprietà:

```Kusto
Event 
| search "error"
```

Una singola interruzione di riga rende le query più facili da leggere. Più interruzioni di riga suddividono la query in query separate.

Un altro modo per scrivere la stessa query è:

```Kusto
search in (Event) "error"
```

Nel secondo esempio, il comando **search** cerca solo il termine **error** nei record della tabella **Events**.

Per impostazione predefinita, Log Analytics limita le query a un intervallo di tempo delle ultime 24 ore. Per impostare un intervallo di tempo diverso, è possibile aggiungere alla query un filtro **TimeGenerated** esplicito oppure usare il controllo **Intervallo di tempo**.

### <a name="use-the-time-range-control"></a>Usare il controllo Intervallo di tempo
Per usare il controllo **Intervallo di tempo**, selezionarlo nella barra superiore e quindi selezionare un valore nell'elenco a discesa oppure selezionare **Personalizza** per creare un intervallo di tempo personalizzato.

![Controllo di selezione di data e ora](media/get-started-portal/time-picker.png)

- I valori dell'intervallo di tempo sono in formato UTC, che può essere diverso rispetto al fuso orario locale.
- Se la query imposta in modo esplicito un filtro per **TimeGenerated**, il controllo di selezione di data e ora mostra **Imposta nella query** ed è disabilitato per evitare conflitti.

### <a name="run-a-query"></a>Eseguire una query
Per eseguire una query, posizionare il cursore in un punto qualsiasi all'interno della query e selezionare **Esegui** nella barra superiore oppure premere **MAIUSC**+**INVIO**. La query viene eseguita fino a quando non viene trovata una riga vuota.

## <a name="filter-results"></a>Filtrare i risultati
Log Analytics limita i risultati a un massimo di 10.000 record. Una query generale come `Event` restituisce troppi risultati per essere utile. È possibile filtrare i risultati della query limitando gli elementi della tabella nella query o aggiungendo in modo esplicito un filtro ai risultati. L'applicazione del filtro tramite gli elementi della tabella restituisce un nuovo set di risultati, mentre un filtro esplicito viene applicato al set di risultati esistente.

### <a name="filter-by-restricting-table-elements"></a>Filtrare limitando gli elementi della tabella
Per filtrare i risultati della query `Event` per gli eventi **Error** limitando gli elementi della tabella nella query:

1. Nei risultati della query, selezionare la freccia a discesa accanto a qualsiasi record con **Error** nella colonna **EventLevelName**. 
   
1. Nei dettagli espansi passare il puntatore del mouse sui dettagli e selezionare **...** accanto a **EventLevelName** e quindi selezionare **Include "Error"** . 
   
   ![Aggiungere un filtro alla query](media/get-started-portal/add-filter.png)
   
1. Si noti che la query nell'**Editor di query** ora è stata modificata con:
   
   ```Kusto
   Event
   | where EventLevelName == "Error"
   ```
   
1. Selezionare **Esegui** per eseguire la nuova query.

### <a name="filter-by-explicitly-filtering-results"></a>Filtrare filtrando in modo esplicito i risultati
Per filtrare i risultati della query `Event` per gli eventi **Error** filtrando i risultati della query:

1. Nei risultati della query selezionare l'icona **Filtro** accanto all'intestazione di colonna **EventLevelName**. 
   
1. Nel primo campo della finestra popup selezionare **È uguale a**, quindi immettere *error* nel campo successivo. 
   
1. Selezionare **Filtra**.
   
   ![Screenshot che mostra una tabella di risultati con un menu contestuale per filtrare i risultati in base a EventLevelName.](media/get-started-portal/filter.png)

## <a name="sort-group-and-select-columns"></a>Ordinare, raggruppare e selezionare le colonne
Per ordinare i risultati della query in base a una colonna specifica, ad esempio **TimeGenerated [UTC]** , selezionare l'intestazione di colonna. Selezionare di nuovo l'intestazione per passare dall'ordine crescente a quello decrescente e viceversa.

![Ordinamento in base alla colonna](media/get-started-portal/sort-column.png)

Un altro modo per organizzare i risultati è in gruppi. Per raggruppare i risultati in base a una colonna specifica, trascinare l'intestazione di colonna nella barra sopra la tabella dei risultati denominata **Trascina qui un'intestazione di colonna per eseguire il raggruppamento in base alla colonna specifica**. Per creare sottogruppi, trascinare le altre colonne nella barra superiore. È possibile ridisporre la gerarchia e l'ordinamento dei gruppi e dei sottogruppi nella barra.

![Screenshot che mostra i risultati delle query con sottogruppi per EventLevelName e Computer.](media/get-started-portal/groups.png)

Per nascondere o visualizzare le colonne nei risultati, selezionare **Colonne** sopra la tabella, quindi selezionare o deselezionare le colonne desiderate dall'elenco a discesa.

![Selezione colonne](media/get-started-portal/select-columns.png)

## <a name="view-and-modify-charts"></a>Visualizzare e modificare i grafici
È anche possibile visualizzare i risultati delle query in formati visivi. Immettere la query seguente come esempio:

```Kusto
Event 
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1d) 
| summarize count() by Source 
```

Per impostazione predefinita i risultati vengono visualizzati in una tabella. Selezionare **Grafico** sopra la tabella per visualizzare i risultati in una visualizzazione grafica.

![Grafico a barre](media/get-started-portal/bar-chart.png)

I risultati vengono visualizzati in un grafico a barre in pila. Selezionare altre opzioni, ad esempio **Colonne in pila** o **Torta** per vedere altre visualizzazioni dei risultati.

![Grafico a torta](media/get-started-portal/pie-chart.png)

È possibile modificare le proprietà della visualizzazione, ad esempio gli assi x e y, o le preferenze di raggruppamento e divisione, manualmente dalla barra di controllo.

È anche possibile impostare la visualizzazione preferita nella query stessa, usando l'operatore [render](/azure/kusto/query/renderoperator).

## <a name="pin-results-to-a-dashboard"></a>Aggiungere i risultati a un dashboard

Per aggiungere una tabella o un grafico dei risultati da Log Analytics a un dashboard di Azure condiviso, selezionare **Aggiungi al dashboard** nella barra superiore. 

![Aggiungi al dashboard](media/get-started-portal/pin-dashboard.png)

Nel riquadro **Aggiungi a un altro dashboard** selezionare o creare un dashboard condiviso a cui aggiungere l'elemento, quindi selezionare **Applica**. La tabella o il grafico viene visualizzato nel dashboard di Azure selezionato.

![Grafico aggiunto al dashboard](media/get-started-portal/pin-dashboard2.png)

Una tabella o un grafico aggiunto a un dashboard condiviso presenta le seguenti semplificazioni: 

- I dati sono limitati agli ultimi 14 giorni.
- Una tabella mostra solo fino a quattro colonne e le prime sette righe.
- I grafici con molte categorie discrete raggruppano automaticamente le categorie meno popolate in un singolo contenitore **others**.

## <a name="save-load-or-export-queries"></a>Salvare, caricare o esportare query

Quando si crea una query, è possibile salvare o condividere la query o i risultati con altri utenti. 

### <a name="save-queries"></a>Salvataggio delle query

Per salvare una query:

1. Selezionare **Salva** nella barra superiore.
   
1. Nella finestra di dialogo **Salva** assegnare alla query un **Nome**, usando i caratteri a-z, A-Z, 0-9, spazio, trattino, carattere di sottolineatura, punto, parentesi o pipe. 
   
1. Selezionare se salvare la query come **Query** o **Funzione**. Le funzioni sono query a cui altre query possono fare riferimento. 
   
   Per salvare una query come funzione, fornire un **Alias della funzione**, ovvero un nome breve che può essere usato da altre query per chiamare questa query.
   
1. Se ci si trova in un'area di lavoro Log Analytics, specificare una **categoria** per **Esplora query** da usare per la query. (Le categorie non sono disponibili per le query di Application Insights)
   
1. Selezionare **Salva**.
   
   ![Salvare la funzione](media/get-started-portal/save-function.png)

### <a name="load-queries"></a>Caricare le query
Per caricare una query salvata, selezionare **Esplora query** in alto a destra. Viene visualizzato il riquadro **Esplora query**, che elenca tutte le query per categoria. Espandere le categorie o immettere un nome di query nella barra di ricerca, quindi selezionare una query per caricarla nell'**Editor di query**. È possibile contrassegnare una query come **Preferita** selezionando la stella accanto al nome della query.

![Esplora query](media/get-started-portal/query-explorer.png)

### <a name="export-and-share-queries"></a>Esportare e condividere query
Per esportare una query, selezionare **Esporta** nella barra superiore, quindi selezionare **Esporta in CSV - tutte le colonne**, **Esporta in CSV - colonne visualizzate** o **Esporta in Power BI (Query M)** dall'elenco a discesa.

Il video seguente mostra come integrare Log Analytics con Excel.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4Asme]

Per condividere un collegamento a una query, selezionare **Copia collegamento** nella barra superiore, quindi selezionare **Copia collegamento alla query**, **Copia testo query** o **Copia risultati della query** per copiare l'elemento negli Appunti. È possibile inviare il collegamento alla query ad altri utenti che hanno accesso alla stessa area di lavoro.

## <a name="next-steps"></a>Passaggi successivi

Passare all'esercitazione successiva per altre informazioni su come scrivere query di log di Monitoraggio di Azure.
> [!div class="nextstepaction"]
> [Scrivere query di log di Monitoraggio di Azure](get-started-queries.md)
