---
title: Informazioni di riferimento sul riquadro Progettazione viste di Log Analytics | Microsoft Docs
description: Progettazione viste di Log Analytics consente di creare viste personalizzate nella console di OMS contenenti visualizzazioni diverse dei dati nel repository di OMS. In questo articolo vengono fornite informazioni di riferimento sulle impostazioni relative a ciascun riquadro disponibile per l&quot;uso nelle viste personalizzate.
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: 
ms.assetid: 41787c8f-6c13-4520-b0d3-5d3d84fcf142
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/26/2016
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: c00a472e2dda12874dd7bee4ccb6c4d53bf104a7
ms.openlocfilehash: 1f4a42ea42000c3a8dd25baea610332ea6cd6a71


---
# <a name="log-analytics-view-designer-tile-reference"></a>Informazioni di riferimento sul riquadro Progettazione viste di Log Analytics
Progettazione viste di Log Analytics consente di creare viste personalizzate nella console di OMS contenenti visualizzazioni diverse dei dati nel repository di OMS. In questo articolo vengono fornite informazioni di riferimento sulle impostazioni relative a ciascun riquadro disponibile per l'uso nelle viste personalizzate.

Altri articoli disponibili su Progettazione viste sono:

* [Progettazione viste](log-analytics-view-designer.md): una panoramica su Progettazione viste e sulle procedure per creare e modificare viste personalizzate.
* [Informazioni di riferimento sulle parti della visualizzazione](log-analytics-view-designer-parts.md): informazioni di riferimento sulle impostazioni relative a ciascun riquadro disponibile per l'uso nelle viste personalizzate. 

Nella tabella seguente sono elencati i diversi tipi di riquadri disponibili in Progettazione viste.  Nelle sezioni seguenti vengono descritti i dettagli di ogni tipo di riquadro e le relative proprietà.

| Riquadro | Descrizione |
|:--- |:--- |
| [Number](#number-tile) |Un numero singolo che indica la quantità di record prodotti da una query. |
| [Due numeri](#two-numbers-tile) |Due numeri singoli che indicano la quantità di record prodotti da due query diverse. |
| [Anello](#donut-tile) |Un grafico ad anello basato su una query con un valore di riepilogo al centro. |
| [Grafico a linee e callout](#line-chart-amp-callout-tile) |Un grafico a linee basato su una query e un callout con un valore di riepilogo. |
| [Grafico a linee](#line-chart-tile) |Un grafico a linee basato su una query. |
| [Due sequenze temporali](#two-timelines-tile) |Un istogramma con due serie, ognuna basata su una query separata. |

## <a name="number-tile"></a>Riquadro Numero
Il riquadro **Numero** visualizza un numero singolo che indica la quantità di record prodotti da una query di log e un'etichetta.

![Riquadro Numero](media/log-analytics-view-designer/tile-number.png)

| Impostazione | Description |
|:--- |:--- |
| Nome |Testo da visualizzare nella parte superiore del riquadro. |
| Descrizione |Testo da visualizzare sotto il nome del riquadro. |
| **Riquadro** | |
| Legenda |Testo da visualizzare sotto il valore. |
| Query |Query da eseguire.  Verrà visualizzato il conteggio del numero di record restituiti dalla query. |
| **Funzionalità avanzate** |**> Verifica del flusso di dati** |
| Enabled |Selezionare se la verifica del flusso di dati deve essere abilitata per il riquadro.  Viene fornito un messaggio alternativo se non sono disponibili dati per il riquadro.  In genere viene usato per fornire un messaggio durante il periodo temporaneo in cui la vista viene installata e i dati diventano disponibili. |
| Query |La query da eseguire per verificare se sono disponibili i dati per la vista.  Se la query non restituisce risultati, viene visualizzato un messaggio anziché il valore dalla query principale. |
| Message |Il messaggio da visualizzare se la query di verifica del flusso di dati non restituisce dati.  Se non si specifica alcun messaggio, viene visualizzato *Esecuzione della valutazione*. |


## <a name="two-numbers-tile"></a>Riquadro Due numeri
Nel riquadro **Due numeri** vengono visualizzati due numeri che mostrano il conteggio dei record provenienti da due query di log diverse e un'etichetta per ciascuno.

![Riquadro Due numeri](media/log-analytics-view-designer/tile-two-numbers.png)

| Impostazione | Description |
|:--- |:--- |
| Nome |Testo da visualizzare nella parte superiore del riquadro. |
| Descrizione |Testo da visualizzare sotto il nome del riquadro. |
| **Primo riquadro** | |
| Legenda |Testo da visualizzare sotto il valore. |
| Query |Query da eseguire.  Verrà visualizzato il conteggio del numero di record restituiti dalla query. |
| **Secondo riquadro** | |
| Legenda |Testo da visualizzare sotto il valore. |
| Query |Query da eseguire.  Verrà visualizzato il conteggio del numero di record restituiti dalla query. |
| **Funzionalità avanzate** |**> Verifica del flusso di dati** |
| Enabled |Selezionare se la verifica del flusso di dati deve essere abilitata per il riquadro.  Viene fornito un messaggio alternativo se non sono disponibili dati per il riquadro.  In genere viene usato per fornire un messaggio durante il periodo temporaneo in cui la vista viene installata e i dati diventano disponibili. |
| Query |La query da eseguire per verificare se sono disponibili i dati per la vista.  Se la query non restituisce risultati, viene visualizzato un messaggio anziché il valore dalla query principale. |
| Message |Il messaggio da visualizzare se la query di verifica del flusso di dati non restituisce dati.  Se non si specifica alcun messaggio, viene visualizzato *Esecuzione della valutazione*. |


## <a name="donut-tile"></a>Riquadro Anello
Nel riquadro **Anello** viene visualizzato un singolo numero riepilogato da una colonna di valore in una query di log.  L'anello visualizza graficamente i risultati dei tre record principali.

![Riquadro Anello](media/log-analytics-view-designer/tile-donut.png)

| Impostazione | Description |
|:--- |:--- |
| Nome |Testo da visualizzare nella parte superiore del riquadro. |
| Descrizione |Testo da visualizzare sotto il nome del riquadro. |
| **Anello** | |
| Query |La query da eseguire per l'anello.  La prima proprietà deve essere un valore di testo, mentre la seconda proprietà un valore numerico.  In genere si tratta di una query che usa la parola chiave **measure** per riepilogare i risultati. |
| **Anello** |**> Centro** |
| Text |Il testo da visualizzare sotto il valore all'interno dell'anello. |
| Operazione |L'operazione da eseguire sulla proprietà del valore per riepilogare un singolo valore.<br><br>- Somma: per aggiungere i valori di tutti i record con il valore di proprietà.<br>- Percentuale: la percentuale dei valori sommati dai record con il valore di proprietà rispetto ai valori sommati di tutti i record. |
| I valori dei risultati usati nell'operazione centrale |Se si desidera, fare clic sul segno più per aggiungere uno o più valori.  I risultati della query saranno limitati ai record con i valori di proprietà specificati.  Se non vengono aggiunti valori, tutti i record sono inclusi nella query. |
| **Anello** |**> Opzioni aggiuntive** |
| Colori |Il colore da visualizzare per ognuna delle tre proprietà principali.  Se si desidera specificare colori alternativi per valori di proprietà specifici, usare Mappa colori avanzata. |
| Mappa colori avanzata |Visualizza un colore per valori di proprietà specifici.  Se il valore specificato è fra i primi tre, viene visualizzato il colore alternativo anziché il colore standard.  Se la proprietà non è fra i primi tre, il colore non viene visualizzato. |
| **Funzionalità avanzate** |**> Verifica del flusso di dati** |
| Enabled |Selezionare se la verifica del flusso di dati deve essere abilitata per il riquadro.  Viene fornito un messaggio alternativo se non sono disponibili dati per il riquadro.  In genere viene usato per fornire un messaggio durante il periodo temporaneo in cui la vista viene installata e i dati diventano disponibili. |
| Query |La query da eseguire per verificare se sono disponibili i dati per la vista.  Se la query non restituisce risultati, viene visualizzato un messaggio anziché il valore dalla query principale. |
| Message |Il messaggio da visualizzare se la query di verifica del flusso di dati non restituisce dati.  Se non si specifica alcun messaggio, viene visualizzato *Esecuzione della valutazione*. |


## <a name="line-chart-tile"></a>Riquadro Grafico a linee
Nel riquadro **Grafico a linee** viene visualizzato un grafico a linee con più serie prodotte da una query di log nel tempo.  

![Riquadro Grafico a linee e callout](media/log-analytics-view-designer/tile-line-chart.png)

| Impostazione | Description |
|:--- |:--- |
| Nome |Testo da visualizzare nella parte superiore del riquadro. |
| Descrizione |Testo da visualizzare sotto il nome del riquadro. |
| **Grafico a linee** | |
| Query |La query da eseguire per il grafico a linee.  La prima proprietà deve essere un valore di testo, mentre la seconda proprietà un valore numerico.  In genere si tratta di una query che usa la parola chiave **measure** per riepilogare i risultati.  Se la query usa la parola chiave **interval**, l'asse X del grafico userà questo intervallo di tempo.  Se la query non include la parola chiave **interval**, vengono usati intervalli orari per l'asse X. |
| **Grafico a linee** |**> Asse Y** |
| Usa scala logaritmica |Selezionare questa opzione per usare una scala logaritmica per l'asse Y. |
| Unità |Specifica le unità per i valori restituiti dalla query.  Queste informazioni vengono usate per visualizzare le etichette del grafico che indicano i tipi di valore e, facoltativamente, per convertire i valori.  **Tipo di unità** specifica la categoria dell'unità e definisce i valori **Tipo di unità corrente** che sono disponibili.  Se si seleziona un valore in **Converti in**, i valori numerici vengono convertiti dal tipo **Unità corrente** al tipo **Converti in**. |
| Etichetta personalizzata |Il testo da visualizzare per l'asse Y accanto all'etichetta per il tipo di unità.  Se non viene specificata alcuna etichetta, viene visualizzato solo il tipo di unità. |
| **Funzionalità avanzate** |**> Verifica del flusso di dati** |
| Enabled |Selezionare se la verifica del flusso di dati deve essere abilitata per il riquadro.  Viene fornito un messaggio alternativo se non sono disponibili dati per il riquadro.  In genere viene usato per fornire un messaggio durante il periodo temporaneo in cui la vista viene installata e i dati diventano disponibili. |
| Query |La query da eseguire per verificare se sono disponibili i dati per la vista.  Se la query non restituisce risultati, viene visualizzato un messaggio anziché il valore dalla query principale. |
| Message |Il messaggio da visualizzare se la query di verifica del flusso di dati non restituisce dati.  Se non si specifica alcun messaggio, viene visualizzato *Esecuzione della valutazione*. |


## <a name="line-chart--callout-tile"></a>Riquadro Grafico a linee e callout
Nel riquadro **Grafico a linee e callout** viene visualizzato un grafico a linee con più serie prodotto da una query di log nel tempo e un callout con un valore riepilogato.  

![Riquadro Grafico a linee e callout](media/log-analytics-view-designer/tile-line-chart-callout.png)

| Impostazione | Description |
|:--- |:--- |
| Nome |Testo da visualizzare nella parte superiore del riquadro. |
| Descrizione |Testo da visualizzare sotto il nome del riquadro. |
| **Grafico a linee** | |
| Query |La query da eseguire per il grafico a linee.  La prima proprietà deve essere un valore di testo, mentre la seconda proprietà un valore numerico.  In genere si tratta di una query che usa la parola chiave **measure** per riepilogare i risultati.  Se la query usa la parola chiave **interval**, l'asse X del grafico userà questo intervallo di tempo.  Se la query non include la parola chiave **interval**, vengono usati intervalli orari per l'asse X. |
| **Grafico a linee** |**> Callout** |
| Callout |Il testo del titolo da visualizzare sopra il valore di callout. |
| Nome della serie |Il valore della proprietà per la serie da usare per il valore di callout.  Se non viene fornita alcuna serie, vengono usati tutti i record prodotti dalla query. |
| Operazione |L'operazione da eseguire sulla proprietà di valore per riepilogare in un singolo valore per il callout.<br>- Media: la media del valore di tutti i record.<br><br>- Numero: la quantità di tutti i record restituiti dalla query.<br>- Ultimo campione: il valore dell'ultimo intervallo incluso nel grafico.<br>- Max: il valore massimo degli intervalli inclusi nel grafico.<br>- Min: il valore minimo risultante dagli intervalli inclusi nel grafico.<br>- Somma: la somma del valore di tutti i record. |
| **Grafico a linee** |**> Asse Y** |
| Usa scala logaritmica |Selezionare questa opzione per usare una scala logaritmica per l'asse Y. |
| Unità |Specifica le unità per i valori restituiti dalla query.  Queste informazioni vengono usate per visualizzare le etichette del grafico che indicano i tipi di valore e, facoltativamente, per convertire i valori.  **Tipo di unità** specifica la categoria dell'unità e definisce i valori **Tipo di unità corrente** che sono disponibili.  Se si seleziona un valore in **Converti in**, i valori numerici vengono convertiti dal tipo **Unità corrente** al tipo **Converti in**. |
| Etichetta personalizzata |Il testo da visualizzare per l'asse Y accanto all'etichetta per il tipo di unità.  Se non viene specificata alcuna etichetta, viene visualizzato solo il tipo di unità. |
| **Funzionalità avanzate** |**> Verifica del flusso di dati** |
| Enabled |Selezionare se la verifica del flusso di dati deve essere abilitata per il riquadro.  Viene fornito un messaggio alternativo se non sono disponibili dati per il riquadro.  In genere viene usato per fornire un messaggio durante il periodo temporaneo in cui la vista viene installata e i dati diventano disponibili. |
| Query |La query da eseguire per verificare se sono disponibili i dati per la vista.  Se la query non restituisce risultati, viene visualizzato un messaggio anziché il valore dalla query principale. |
| Message |Il messaggio da visualizzare se la query di verifica del flusso di dati non restituisce dati.  Se non si specifica alcun messaggio, viene visualizzato *Esecuzione della valutazione*. |


## <a name="two-timelines-tile"></a>Riquadro Due sequenze temporali
Il riquadro **Due sequenze temporali** visualizza come istogramma i risultati di due query di log nel tempo.  Viene visualizzato un callout per ogni serie.  

![Riquadro Due sequenze temporali](media/log-analytics-view-designer/tile-two-timelines.png)

| Impostazione | Description |
|:--- |:--- |
| Nome |Testo da visualizzare nella parte superiore del riquadro. |
| Descrizione |Testo da visualizzare sotto il nome del riquadro. |
| Primo grafico | |
| Legenda |Il testo da visualizzare nel callout per la prima serie. |
| Colore |Il colore da usare per le colonne della prima serie. |
| Query grafico |La query da eseguire per la prima serie.  Il conteggio del numero di record in ogni intervallo di tempo sarà rappresentato dalle colonne del grafico. |
| Operazione |L'operazione da eseguire sulla proprietà di valore per riepilogare in un singolo valore per il callout.<br><br>- Media: la media del valore di tutti i record.<br>- Numero: la quantità di tutti i record restituiti dalla query.<br>- Ultimo campione: il valore dell'ultimo intervallo incluso nel grafico.<br>- Max: il valore massimo degli intervalli inclusi nel grafico. |
| **Secondo grafico** | |
| Legenda |Il testo da visualizzare nel callout per la seconda serie. |
| Colore |Il colore da usare per le colonne della seconda serie. |
| Query grafico |La query da eseguire per la seconda serie.  Il conteggio del numero di record in ogni intervallo di tempo sarà rappresentato dalle colonne del grafico. |
| Operazione |L'operazione da eseguire sulla proprietà di valore per riepilogare in un singolo valore per il callout.<br><br>- Media: la media del valore di tutti i record.<br>- Numero: la quantità di tutti i record restituiti dalla query.<br>- Ultimo campione: il valore dell'ultimo intervallo incluso nel grafico.<br>- Max: il valore massimo degli intervalli inclusi nel grafico. |
| **Funzionalità avanzate** |**> Verifica del flusso di dati** |
| Enabled |Selezionare se la verifica del flusso di dati deve essere abilitata per il riquadro.  Viene fornito un messaggio alternativo se non sono disponibili dati per il riquadro.  In genere viene usato per fornire un messaggio durante il periodo temporaneo in cui la vista viene installata e i dati diventano disponibili. |
| Query |La query da eseguire per verificare se sono disponibili i dati per la vista.  Se la query non restituisce risultati, viene visualizzato un messaggio anziché il valore dalla query principale. |
| Message |Il messaggio da visualizzare se la query di verifica del flusso di dati non restituisce dati.  Se non si specifica alcun messaggio, viene visualizzato *Esecuzione della valutazione*. |


## <a name="next-steps"></a>Passaggi successivi
* Informazioni sulle [ricerche nei log](log-analytics-log-searches.md) per supportare le query nei riquadri.
* Aggiungere [parti della visualizzazione](log-analytics-view-designer-parts.md) per la vista personalizzata.




<!--HONumber=Jan17_HO1-->


