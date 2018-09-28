---
title: Introduzione a Log Analytics nel portale di Azure | Microsoft Docs
description: Questo articolo contiene un'esercitazione per l'uso di Log Analytics nel portale di Azure per la scrittura di query.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 3bcc5368a99dc7c7c32381ca0226119d81fc2c0a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978200"
---
# <a name="get-started-with-log-analytics-in-the-azure-portal"></a>Introduzione a Log Analytics nel portale di Azure

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

In questa esercitazione si apprenderà come usare la pagina di Log Analytics nel portale di Azure (attualmente in anteprima) per scrivere query di Log Analytics. Si apprenderà come:

- Scrivere query semplici
- Comprendere lo schema dei dati
- Filtrare, ordinare e raggruppare i risultati
- Applicare un intervallo di tempo
- Creare grafici
- Salvare e caricare query
- Esportare e condividere query


## <a name="meet-the-log-analytics-page"></a>La pagina Log Analytics 
La pagina di Log Analytics è uno strumento Web usato per scrivere ed eseguire query di Azure Log Analytics. Aprirla selezionando **Registri (anteprima)** nel menu Log Analytics. Viene visualizzata una nuova query vuota.

![Home page](media/get-started-analytics-portal/homepage.png)



## <a name="basic-queries"></a>Query di base
È possibile usare le query per cercare termini, identificare le tendenze, analizzare i modelli e ottenere molte altre informazioni dettagliate basate sui dati. Iniziare con una query di base:

```Kusto
Event | search "error"
```

Questa query cerca nella tabella _Event_ i record che contengono il termine "error" in qualsiasi proprietà.

Le query possono iniziare con un nome di tabella o un comando **search**. L'esempio precedente inizia con il nome di tabella _Event_, che definisce l'ambito della query. Il carattere di barra verticale, o pipe, (|) separa i comandi, in modo che l'output del primo comando sia l'input del comando successivo. È possibile aggiungere qualsiasi numero di comandi a una singola query.

Un altro modo per scrivere la stessa query è il seguente:

```Kusto
search in (Event) "error"
```

In questo esempio l'ambito di **search** è la tabella _Event_ e viene eseguita una ricerca del termine "error" in tutti i record della tabella.

## <a name="running-a-query"></a>Esecuzione di una query
Per eseguire una query, fare clic sul pulsante **Esegui** oppure premere **MAIUSC+INVIO**. Prendere in considerazione i dettagli seguenti che determinano il codice che verrà eseguito e i dati restituiti:

- Interruzioni di riga: una singola interruzione rende più chiara la query. Più interruzioni di riga suddividono la query in query separate.
- Cursore: posizionare il cursore in un punto qualsiasi all'interno della query per eseguirla. La query corrente viene considerata come codice fino a quando non viene trovata una riga vuota.
- Intervallo di tempo: per impostazione predefinita, viene usato un intervallo di tempo corrispondente alle _ultime 24 ore_. Per usare un intervallo diverso, usare il controllo di selezione di data e ora o aggiungere alla query un filtro di intervallo di tempo esplicito.


## <a name="understand-the-schema"></a>Informazioni sullo schema
Lo schema è una raccolta di tabelle raggruppate visivamente in una categoria logica. Diverse categorie provengono dalle soluzioni di monitoraggio. La categoria _LogManagement_ contiene i dati comuni, ad esempio eventi di Windows e Syslog, dati sulle prestazioni ed eventi heartbeat client.

![SCHEMA](media/get-started-analytics-portal/schema.png)

In ogni tabella i dati sono organizzati in colonne con tipi di dati diversi, come indicato dalle icone accanto al nome di colonna. La tabella _Event_ nello screenshot contiene ad esempio colonne come _Computer_, costituita da testo, _EventCategory_, costituita da un numero, e _TimeGenerated_, di tipo data/ora.

## <a name="filter-the-results"></a>Filtrare i risultati
Per iniziare, recuperare tutti gli elementi della tabella _Event_.

```Kusto
Event
```

La pagina di Log Analytics definisce automaticamente l'ambito dei risultati in base a:

- Intervallo di tempo: per impostazione predefinita, le query sono limitate alle ultime 24 ore.
- Numero di risultati: i risultati sono limitati a un massimo di 10.000 record.

Questa query è molto generica e restituisce troppi risultati per essere utile. È possibile filtrare i risultati tramite gli elementi della tabella o aggiungendo in modo esplicito un filtro alla query. Il filtro dei risultati tramite gli elementi della tabella si applica al set di risultati esistente, mentre un filtro per la query restituisce un nuovo set di risultati filtrato e può quindi generare risultati più accurati.

### <a name="add-a-filter-to-the-query"></a>Aggiungere un filtro alla query
A sinistra di ogni record è presente una freccia. Fare clic su tale freccia per visualizzare i dettagli per un record specifico.

Passare il mouse sopra un nome di colonna per visualizzare le icone "+" e "-". Per aggiungere un filtro che restituisce solo i record con lo stesso valore, fare clic sul segno "+". Fare clic su "-" per escludere i record con questo valore e quindi fare clic su **Esegui** per eseguire di nuovo la query.

![Aggiungere un filtro alla query](media/get-started-analytics-portal/add-filter.png)

### <a name="filter-through-the-table-elements"></a>Filtrare gli elementi della tabella
Verranno ora presi n considerazione gli eventi con gravità corrispondete a _Error_. Questo valore è specificato in una colonna denominata _EventLevelName_. Scorrere verso destra per visualizzare questa colonna.

Fare clic sull'icona di filtro accanto al titolo della colonna e nella finestra popup selezionare _Inizia con_ e il testo _error_:

![Filtro](media/get-started-analytics-portal/filter.png)


## <a name="sort-and-group-results"></a>Ordinare e raggruppare i risultati
I risultati sono ora limitati in modo da includere solo gli eventi di errore di SQL Server creati nelle ultime 24 ore. Tuttavia, i risultati non sono ordinati in alcun modo. Per ordinare i risultati in base a una colonna specifica, ad esempio _timestamp_, fare clic sul titolo della colonna. Con il primo clic viene applicato l'ordinamento crescente, con il secondo clic quello decrescente.

![Ordinamento in base alla colonna](media/get-started-analytics-portal/sort-column.png)

Un altro modo per organizzare i risultati è in gruppi. Per raggruppare i risultati in base una colonna specifica, è sufficiente trascinare l'intestazione di colonna sopra le altre colonne. Per creare sottogruppi, trascinare anche altre colonne sulla barra superiore.

![Gruppi](media/get-started-analytics-portal/groups.png)

## <a name="select-columns-to-display"></a>Selezionare le colonne da visualizzare
La tabella dei risultati include spesso molte colonne. Alcune colonne restituite possono non venire visualizzate per impostazione predefinita oppure si potrebbe voler rimuovere alcune colonne visualizzate. Per selezionare le colonne da visualizzare, fare clic sul pulsante Colonne:

![Selezione colonne](media/get-started-analytics-portal/select-columns.png)


## <a name="select-a-time-range"></a>Selezionare un intervallo di tempo
Per impostazione predefinita, la pagina di Log Analytics applica un intervallo di tempo corrispondente alle _ultime 24 ore_. Per usare un intervallo diverso, selezionare un valore diverso tramite il controllo di selezione di data e ora e fare clic su **Esegui**. Oltre ai valori predefiniti, è possibile usare l'opzione _Intervallo di tempo personalizzato_ per selezionare un intervallo assoluto per la query.

![Controllo di selezione di data e ora](media/get-started-analytics-portal/time-picker.png)

Quando si seleziona un intervallo di tempo personalizzato, i valori selezionati sono in formato UTC, che può essere diverso rispetto al fuso orario locale.

Se la query contiene in modo esplicito un filtro per _TimeGenerated_, nel titolo del controllo di selezione di data e ora verrà indicato _Set in query_ (Impostato nella query). La selezione manuale verrà disabilitata per evitare conflitti.


## <a name="charts"></a>Grafici
Oltre a restituire i risultati in una tabella, è possibile presentare i risultati della query in formato visivo. Usare la query seguente come esempio:

```Kusto
Event 
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1d) 
| summarize count() by Source 
```

Per impostazione predefinita, i risultati vengono visualizzati in una tabella. Fare clic su _Grafico_ per visualizzare i risultati in una visualizzazione grafica:

![Grafico a barre](media/get-started-analytics-portal/bar-chart.png)

I risultati vengono visualizzati in un grafico a barre in pila. Fare clic su _Istogramma in pila_ e selezionare _Torta_ per mostrare un'altra visualizzazione dei risultati:

![Grafico a torta](media/get-started-analytics-portal/pie-chart.png)

Diverse proprietà della visualizzazione, ad esempio gli assi x e y, o le preferenze di raggruppamento e divisione, possono essere modificate manualmente dalla barra di controllo.

È anche possibile impostare la visualizzazione preferita nella query stessa, usando l'operatore render.

### <a name="smart-diagnostics"></a>Diagnostica intelligente
In un grafico del tempo, se si verifica un picco o una variazione improvvisa dei dati, sulla linea viene evidenziato un punto. Ciò indica che la funzionalità di _diagnostica intelligente_ ha identificato una combinazione di proprietà che permettono di escludere tramite filtro le variazioni improvvise. Fare clic sul punto per ottenere maggiori dettagli sul filtro e per visualizzare la versione filtrata. Ciò può essere utile per identificare la causa della variazione:

![Diagnostica intelligente](media/get-started-analytics-portal/smart-diagnostics.png)

## <a name="pin-to-dashboard"></a>Aggiungi al dashboard
Per aggiungere un diagramma o una tabella a uno dei dashboard di Azure condivisi, fare clic sull'icona a forma di puntina.

![Aggiungi al dashboard](media/get-started-analytics-portal/pin-dashboard.png)

Quando si aggiunge un grafico a un dashboard, vengono applicate al grafico determinate semplificazioni:

- Colonne e righe di tabella: per aggiungere una tabella al dashboard, la tabella deve avere un massimo di quattro colonne. Verranno visualizzate solo le prime sette righe.
- Limite di tempo: le query vengono automaticamente limitate agli ultimi 14 giorni.
- Limite del numero di contenitori: se si visualizza un grafico con numerosi contenitori discreti, i contenitori meno popolati vengono automaticamente raggruppati in un unico contenitore _altri_.

## <a name="save-queries"></a>Salvataggio delle query
Una volta creata una query utile, è possibile salvarla o condividerla con altri utenti. L'icona **Salva** si trova sulla barra superiore.

È possibile salvare l'intera pagina di query o una singola query come funzione. Le funzioni sono query a cui altre query possono fare riferimento. Per salvare una query come funzione, è necessario specificare un alias di funzione, ovvero il nome usato per chiamare la query quando altre query vi fanno riferimento.

![Salvare la funzione](media/get-started-analytics-portal/save-function.png)

Le query di Log Analytics vengono sempre salvate in un'area di lavoro selezionata e condivise con altri utenti dell'area di lavoro.

## <a name="load-queries"></a>Caricare le query
L'icona Esplora query si trova nell'area in alto a destra. Permette di elencare tutte le query salvate in base alla categoria. Consente inoltre di contrassegnare query specifiche come preferite per trovarle rapidamente in futuro. Fare doppio clic su una query salvata per aggiungerla alla finestra corrente.

![Esplora query](media/get-started-analytics-portal/query-explorer.png)

## <a name="export-and-share-as-link"></a>Esportare e condividere come collegamento
La pagina di Log Analytics supporta diversi metodi di esportazione:

- Excel: salvare i risultati come file CSV.
- Power BI: esportare i risultati in Power BI. Per informazioni dettagliate, vedere [Importare dati di Azure Log Analytics in Power BI](../log-analytics-powerbi.md).
- Condividere un collegamento: la query stessa può essere condivisa come collegamento che altri utenti che hanno accesso alla stessa area di lavoro possono quindi inviare ed eseguire.

## <a name="next-steps"></a>Passaggi successivi

- Leggere altre informazioni sulla [scrittura di query di Log Analytics](get-started-queries.md).
