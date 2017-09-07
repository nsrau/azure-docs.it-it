---
title: Uso del portale per la ricerca log in Azure Log Analytics | Microsoft Docs
description: Questo articolo include un'esercitazione che descrive come creare ricerche log e analizzare i dati archiviati nell'area di lavoro di Log Analytics tramite il portale per la ricerca log.  L'esercitazione include l'esecuzione di alcune semplici query per restituire diversi tipi di dati e l'analisi dei risultati.
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: 6fc556ceb34cde26d5f3789a2397cdaa34b0b84d
ms.contentlocale: it-it
ms.lasthandoff: 08/28/2017

---
# <a name="create-log-searches-in-azure-log-analytics-using-the-log-search-portal"></a>Creare ricerche log in Azure Log Analytics tramite il portale per la ricerca log

> [!NOTE]
> Questo articolo descrive il portale per la ricerca log in Azure Log Analytics e l'uso del nuovo linguaggio di query.  In [Aggiornare l'area di lavoro di Azure Log Analytics alla nuova ricerca log](log-analytics-log-search-upgrade.md) sono disponibili altre informazioni sul nuovo linguaggio e istruzioni per l'aggiornamento dell'area di lavoro.  
>
> Se l'area di lavoro non è stata aggiornata al nuovo linguaggio di query, è consigliabile consultare [Trovare dati tramite ricerche nei log in Log Analytics](log-analytics-log-searches.md) per informazioni sulla versione corrente del portale per la ricerca log.

Questo articolo include un'esercitazione che descrive come creare ricerche log e analizzare i dati archiviati nell'area di lavoro di Log Analytics tramite il portale per la ricerca log.  L'esercitazione include l'esecuzione di alcune semplici query per restituire diversi tipi di dati e l'analisi dei risultati.  È incentrata sulle funzionalità del portale per la ricerca log per la modifica della query, anziché sulla modifica diretta.  Per informazioni dettagliate su come modificare direttamente la query, vedere [Informazioni di riferimento sul linguaggio di query](https://go.microsoft.com/fwlink/?linkid=856079).

Per creare ricerche nel portale Advanced Analytics anziché nel portale per la ricerca log, vedere [Getting Started with the Analytics Portal](https://go.microsoft.com/fwlink/?linkid=856587) (Introduzione al portale di Analytics).  Entrambi i portali utilizzano lo stesso linguaggio di query per accedere agli stessi dati nell'area di lavoro di Log Analytics.

## <a name="prerequisites"></a>Prerequisiti
In questa esercitazione si presuppone che sia già disponibile un'area di lavoro di Log Analytics con almeno un'origine connessa che genera dati analizzabili tramite le query.  

- Se l'area di lavoro non è disponibile, è possibile crearne una gratuitamente seguendo la procedura in [Introduzione a un'area di lavoro di Log Analytics](log-analytics-get-started.md).
- Connettere almeno un [agente Windows](log-analytics-windows-agents.md) o un [agente Linux](log-analytics-linux-agents.md) all'area di lavoro.  

## <a name="open-the-log-search-portal"></a>Aprire il portale per la ricerca log
Per iniziare, aprire il portale per la ricerca log.  È possibile accedervi dal portale di Azure o dal portale di OMS.

1. Aprire il Portale di Azure.
2. Passare a Log Analytics e selezionare l'area di lavoro.
3. Selezionare **Ricerca log** per rimanere nel portale di Azure o avviare il portale di OMS selezionando **Portale di OMS** e facendo quindi clic sul pulsante Ricerca log.

![Pulsante di ricerca log](media/log-analytics-log-search-log-search-portal/log-search-button.png)

## <a name="create-a-simple-search"></a>Creare una ricerca semplice
Il modo più rapido per recuperare alcuni dati da utilizzare è una query semplice che restituisce tutti i record in una tabella.  In presenza di client Windows o Linux connessi all'area di lavoro, saranno disponibili dati nella tabella Event (Windows) o nella tabella Syslog (Linux).

Digitare una delle query seguenti nella casella di ricerca e fare clic sul pulsante di ricerca.  

```
Event
```
```
Syslog
```

I dati vengono restituiti nella visualizzazione elenco predefinita ed è possibile vedere il numero totale di record restituiti.

![Query semplice](media/log-analytics-log-search-log-search-portal/log-search-portal-01.png)

Vengono visualizzate alcune delle prime proprietà di ogni record.  Fare clic su **Mostra più** per visualizzare tutte le proprietà per un record specifico.

![Dettagli sui record](media/log-analytics-log-search-log-search-portal/log-search-portal-02.png)

## <a name="set-the-time-scope"></a>Impostare l'intervallo temporale
Per ogni record raccolto da Log Analytics esiste una proprietà **TimeGenerated** che contiene la data e ora di creazione del record.  Una query nel portale per la ricerca log restituisce solo i record con **TimeGenerated** incluso nell'intervallo temporale visualizzato sul lato sinistro della schermata.  

È possibile modificare il filtro temporale selezionando il valore desiderato nell'elenco a discesa oppure modificando il dispositivo di scorrimento.  Il dispositivo di scorrimento visualizza un grafico a barre che mostra il numero relativo di record per ogni segmento di tempo all'interno dell'intervallo.  Questo segmento può variare a seconda dell'intervallo.

L'intervallo temporale predefinito è **1 giorno**.  Se si modifica il valore impostando **7 giorni** il numero totale di record dovrebbe aumentare.

![Intervallo temporale](media/log-analytics-log-search-log-search-portal/log-search-portal-03.png)

## <a name="filter-results-of-the-query"></a>Filtrare i risultati della query
Sul lato sinistro della schermata è disponibile il riquadro di filtro che consente di aggiungere filtri alla query senza modificarla direttamente.  Varie proprietà dei record restituiti vengono visualizzate con i relativi primi dieci valori e il numero di record.

Se si sta usando la tabella **Event**, selezionare la casella di controllo accanto a **Error** in **EVENTLEVELNAME**.   Se si sta usando la tabella **Syslog**, selezionare la casella di controllo accanto a **err** in **SEVERITYLEVEL**.  La query viene modificata come segue per limitare i risultati agli eventi di errore.

```
Event | where (EventLevelName == "Error")
```
```
Syslog | where (SeverityLevel == "err")
```

![Filtro](media/log-analytics-log-search-log-search-portal/log-search-portal-04.png)

Aggiungere proprietà al riquadro di filtro scegliendo **Aggiungi ai filtri** dal menu della proprietà in uno dei record.

![Menu Aggiungi ai filtri](media/log-analytics-log-search-log-search-portal/log-search-portal-02a.png)

È possibile impostare lo stesso filtro selezionando **Filtra** dal menu della proprietà per un record con il valore in base al quale si vogliono filtrare i risultati.  

L'opzione **Filtra** è disponibile solo per le proprietà con il nome in blu.  Si tratta di campi *disponibili per la ricerca* che vengono indicizzati per le condizioni di ricerca.  I campi in grigio sono campi *disponibili per la ricerca a testo libero* per i quali è disponibile solo l'opzione **Mostra riferimenti**.  Questa opzione restituisce i record con il valore specificato in qualsiasi proprietà.

![Menu di filtro](media/log-analytics-log-search-log-search-portal/log-search-portal-01a.png)

È possibile raggruppare i risultati in base a una singola proprietà selezionando l'opzione **Raggruppa per** nel menu del record.  Verrà aggiunto un operatore [summarize](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) alla query, che visualizza i risultati in un grafico.  È possibile effettuare il raggruppamento in base a una o più proprietà, ma in questo caso occorre modificare direttamente la query.  Selezionare il menu del record accanto alla proprietà **Computer** e scegliere **Raggruppa per 'Computer'**.  

![Raggruppare in base alla proprietà Computer](media/log-analytics-log-search-log-search-portal/log-search-portal-10.png)

## <a name="work-with-results"></a>Usare i risultati
Il portale per la ricerca log offre un'ampia gamma di funzionalità per l'utilizzo dei risultati di una query.  È possibile ordinare, filtrare e raggruppare i risultati per analizzare i dati senza modificare la query effettiva.  I risultati di una query non sono ordinati per impostazione predefinita.

Per visualizzare i dati in formato di tabella e avere così a disposizione ulteriori opzioni di filtro e ordinamento, fare clic su **Tabella**.  

![Visualizzazione tabella](media/log-analytics-log-search-log-search-portal/log-search-portal-05.png)

Fare clic sulla freccia accanto a un record per visualizzare i dettagli di tale record.

![Ordinare i risultati](media/log-analytics-log-search-log-search-portal/log-search-portal-06.png)

È possibile ordinare i risultati in base a qualsiasi campo facendo clic sulla relativa intestazione di colonna.

![Ordinare i risultati](media/log-analytics-log-search-log-search-portal/log-search-portal-07.png)

Per filtrare i risultati in base a un valore specifico nella colonna, fare clic sul pulsante di filtro e specificare una condizione di filtro.

![Filtrare i risultati](media/log-analytics-log-search-log-search-portal/log-search-portal-08.png)

Per raggruppare i risultati in base a una colonna, trascinare l'intestazione della colonna nella parte superiore dei risultati.  È possibile effettuare il raggruppamento in base a più campi trascinando più colonne nella parte superiore.

![Raggruppare i risultati](media/log-analytics-log-search-log-search-portal/log-search-portal-09.png)



## <a name="work-with-performance-data"></a>Utilizzare i dati sulle prestazioni
I dati sulle delle prestazioni per gli agenti Windows e Linux sono archiviati nell'area di lavoro di Log Analytics nella tabella **Perf**.  I record sulle prestazioni hanno lo stesso aspetto di qualsiasi altro record ed è possibile scrivere una query semplice che restituisce tutti i record sulle prestazioni esattamente come per gli eventi.

```
Perf
```

![Dati sulle prestazioni](media/log-analytics-log-search-log-search-portal/log-search-portal-11.png)

La restituzione di milioni di record per tutti i contatori e gli oggetti prestazioni non è molto utile.  È possibile usare gli stessi metodi descritti in precedenza per filtrare i dati oppure digitare la query seguente direttamente nella casella di ricerca log.  Vengono restituiti solo i record relativi all'utilizzo del processore per i computer Windows e Linux.

```
Perf | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time")
```

![Utilizzo del processore](media/log-analytics-log-search-log-search-portal/log-search-portal-12.png)

I dati vengono così limitati a un particolare contatore, ma sono ancora presentati in una forma non particolarmente utile.  È possibile visualizzare i dati in un grafico a linee, ma è prima necessario raggrupparli in base a Computer e TimeGenerated.  Per effettuare il raggruppamento in base a più campi, è necessario modificare direttamente la query, quindi modificarla come indicato di seguito.  Questa query usa la funzione [avg](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions/avg()) sulla proprietà **CounterValue** per calcolare il valore medio ogni ora.

```
Perf  | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time") | summarize avg(CounterValue) by Computer, TimeGenerated
```

![Grafico dei dati sulle prestazioni](media/log-analytics-log-search-log-search-portal/log-search-portal-13.png)

Ora che i dati sono raggruppati in modo adeguato, è possibile visualizzarli in un grafico aggiungendo l'operatore [render](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/render-operator).  

```
Perf  | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time") | summarize avg(CounterValue) by Computer, TimeGenerated | render timechart
```

![Grafico a linee](media/log-analytics-log-search-log-search-portal/log-search-portal-14.png)

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sul linguaggio di query di Log Analytics sono disponibili in [Getting Started with the Analytics Portal](https://go.microsoft.com/fwlink/?linkid=856079) (Introduzione al portale di Analytics).
- Eseguire un'esercitazione con il [portale Advanced Analytics](https://go.microsoft.com/fwlink/?linkid=856587) che consente di eseguire le stesse query e accedere agli stessi dati del portale per la ricerca log.

