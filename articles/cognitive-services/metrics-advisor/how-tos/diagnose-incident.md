---
title: Diagnosticare gli eventi imprevisti usando metrica Advisor
titleSuffix: Azure Cognitive Services
description: Informazioni su come diagnosticare gli eventi imprevisti usando metrica Advisor e ottenere visualizzazioni dettagliate delle anomalie nei dati.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: aahi
ms.openlocfilehash: fb70745cf7773e8caa91b31048af97e8e626bb91
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91597935"
---
# <a name="how-to-diagnose-an-incident-using-metrics-advisor"></a>Procedura: diagnosticare un evento imprevisto usando metrica Advisor

Metrica Advisor offre diverse funzionalità per la diagnostica e fornisce una visualizzazione approfondita degli eventi imprevisti rilevati e fornisce analisi della causa radice. Quando viene rilevato un gruppo di anomalie in una metrica, le anomalie vengono raggruppate in una gerarchia e analizzate.

> [!NOTE]
> Attualmente metrica Advisor supporta la diagnostica degli eventi imprevisti per le metriche con almeno una dimensione e la misura con il tipo  *numerico* . La metrica deve avere un valore di dimensione aggregato, ad esempio SUM per ogni dimensione, che viene usata per compilare la gerarchia di diagnostica. Metrica Advisor offre [**le impostazioni di rollup automatico**](onboard-your-data.md#automatic-roll-up-settings) per facilitare la generazione di valori aggregati. 

Fare clic su **Hub eventi imprevisti** nella finestra di spostamento a sinistra per visualizzare tutti gli eventi imprevisti in una determinata metrica. Nella parte superiore della pagina è possibile selezionare metriche diverse per visualizzare le relative configurazioni di rilevamento e i risultati del rilevamento e modificare l'intervallo di tempo.

> [!TIP]
> È anche possibile ottenere l' **Hub eventi imprevisti** :
> * Facendo clic su un punto dati nella visualizzazione per la metrica e usando i collegamenti nella parte inferiore della finestra **Aggiungi feedback** visualizzata.
> * Fare clic su una delle anomalie nella scheda **eventi imprevisti** per la metrica. 

La sezione **Panoramica** contiene i risultati del rilevamento, inclusi i conteggi delle anomalie e degli avvisi nell'intervallo di tempo selezionato.

:::image type="content" source="../media/diagnostics/incident-hub-overview.png" alt-text="Hub eventi imprevisti" lightbox="../media/diagnostics/incident-hub-overview.png":::

Gli eventi imprevisti rilevati entro la metrica selezionata e l'intervallo di tempo sono elencati nell' **elenco degli eventi imprevisti**. Sono disponibili opzioni per filtrare e ordinare gli eventi imprevisti. Ad esempio, per gravità. Fare clic su uno degli eventi imprevisti per passare alla pagina **evento imprevisto** per ulteriori operazioni di diagnostica.

:::image type="content" source="../media/diagnostics/incident-list.png" alt-text="Hub eventi imprevisti" lightbox="../media/diagnostics/incident-list.png":::

La sezione **diagnostica** consente di eseguire analisi approfondite su un evento imprevisto e gli strumenti per identificare le cause principali.

:::image type="content" source="../media/diagnostics/diagnose-incident.png" alt-text="Hub eventi imprevisti" lightbox="../media/diagnostics/diagnose-incident.png" :::

## <a name="root-cause-advice"></a>Suggerimenti sulla causa radice

Quando un gruppo di anomalie viene rilevato in una metrica e causa un evento imprevisto, l'Advisor di metrica tenterà di analizzare la causa principale dell'evento imprevisto. Il **Consiglio della causa radice** fornisce suggerimenti automatici per le cause probabili di un evento imprevisto. Questa funzionalità è disponibile solo se è presente un valore aggregato nella dimensione. Se la metrica non ha dimensioni, la causa principale sarà se stessa. Le cause principali sono elencate nel pannello sul lato destro e possono essere elencate diverse ragioni. Se nella tabella non sono presenti dati, significa che la dimensione non soddisfa i requisiti per l'esecuzione dell'analisi.

:::image type="content" source="../media/diagnostics/root-cause-advice.png" lightbox="../media/diagnostics/root-cause-advice.png" alt-text="Hub eventi imprevisti":::


Quando la metrica della causa radice viene fornita con dimensioni specifiche, è possibile fare clic su **Vai a metrica** per visualizzare altri dettagli della metrica.

## <a name="incident-tree"></a>Albero eventi imprevisti

Oltre all'analisi automatica delle potenziali cause principali, Metrics Advisor supporta l'analisi manuale della causa radice, usando l' **albero degli eventi imprevisti**. Esistono due tipi di albero degli eventi imprevisti nella pagina evento imprevisto: l'albero di **diagnostica rapida** e l' **albero interattivo**.

L'albero diagnosi rapida è per la diagnosi di un evento imprevisto corrente e il nodo radice è limitato al nodo radice evento imprevisto corrente. È possibile espandere e comprimere i nodi dell'albero facendo clic su di esso e la relativa serie verrà visualizzata insieme alla serie di eventi imprevisti corrente nel grafico sopra l'albero.

La struttura ad albero interattiva consente di diagnosticare gli eventi imprevisti correnti, nonché gli eventi imprevisti precedenti e quelli correlati. Quando si usa l'albero interattivo, fare clic con il pulsante destro del mouse su un nodo per aprire un menu azione, in cui è possibile scegliere una dimensione per eseguire il drill-down nei nodi radice e una dimensione per eseguire il drill-down per ogni nodo. Facendo clic sul pulsante Annulla dell'elenco dimensioni nella parte superiore, è possibile rimuovere il drill-up o verso il basso da questa dimensione. a sinistra fare clic su un nodo per selezionarlo e visualizzarne la serie insieme alla serie di eventi imprevisti corrente nel grafico.

:::image type="content" source="../media/diagnostics/incident-tree.png" alt-text="Hub eventi imprevisti" lightbox="../media/diagnostics/incident-tree.png" :::

## <a name="anomaly-drill-down"></a>Drill-down anomalie

Quando si visualizzano informazioni sugli eventi imprevisti, potrebbe essere necessario ottenere informazioni più dettagliate, ad esempio per dimensioni diverse e timestamp. Se i dati hanno una o più dimensioni, è possibile usare la funzione drill-down per ottenere una visualizzazione più dettagliata. 

Per usare la funzione drill-down, fare clic sulla scheda drill-through della **metrica** nell' **Hub eventi imprevisti**. 

:::image type="content" source="../media/diagnostics/metric-drilling.png" lightbox="../media/diagnostics/metric-drilling.png" alt-text="Hub eventi imprevisti":::

L'impostazione **dimensioni** è un elenco di dimensioni per un evento imprevisto. è possibile selezionare altri valori di dimensione disponibili per ognuno di essi. Dopo la modifica dei valori della dimensione. L'impostazione **timestamp** consente di visualizzare l'evento imprevisto corrente in momenti diversi nel tempo.

### <a name="select-drilling-options-and-choose-a-dimension"></a>Selezionare le opzioni di drill-through e scegliere una dimensione

Sono disponibili due tipi di opzioni di drill-down: il **drill-down** e il **confronto orizzontale**.

> [!Note]
> 1. Per eseguire il drill-down, è possibile esplorare i dati di valori di dimensione diversi, ad eccezione delle dimensioni attualmente selezionate. 
> 2. Per il confronto orizzontale, è possibile esplorare i dati di valori di dimensione diversi, ad eccezione delle dimensioni all-up.

:::image type="content" source="../media/diagnostics/drill-down-dimension.png"  lightbox="../media/diagnostics/drill-down-dimension.png" alt-text="Hub eventi imprevisti":::

### <a name="value-comparison-for-different-dimension-values"></a>Confronto dei valori per valori di dimensione diversi

La seconda sezione della scheda drill-down è una tabella con confronti per valori di dimensioni diversi. Include il valore, il valore della linea di base, il valore della differenza, il valore Delta e se si tratta di un'anomalia.
 
:::image type="content" source="../media/diagnostics/drill-down-comparison.png" alt-text="Hub eventi imprevisti" lightbox="../media/diagnostics/drill-down-comparison.png":::


### <a name="value-and-expected-value-comparisons-for-different-dimension-value"></a>Confronto tra valore e valore previsto per un valore di dimensione diverso

La terza sezione della scheda drill-down è un istogramma con i valori e i valori previsti per i valori delle dimensioni differenti. L'istogramma è ordinato in base alla differenza tra il valore e il valore previsto. È possibile trovare facilmente il valore imprevisto con l'effetto più grande. Nell'immagine precedente, ad esempio, si può notare che, ad eccezione del valore all up, **US7** contribuisce al massimo per l'anomalia.

:::image type="content" source="../media/diagnostics/drill-down-table.png" alt-text="Hub eventi imprevisti" lightbox="../media/diagnostics/drill-down-table.png":::

### <a name="raw-value-visualization"></a>Visualizzazione del valore non elaborato
L'ultima parte della scheda drill-down è un grafico a linee dei valori non elaborati. Con questo grafico specificato, non è necessario passare alla pagina metrica per visualizzare i dettagli.

:::image type="content" source="../media/diagnostics/drill-down-line-chart.png" alt-text="Hub eventi imprevisti" lightbox="../media/diagnostics/drill-down-line-chart.png":::

## <a name="view-similar-anomalies-using-time-series-clustering"></a>Visualizzazione di anomalie simili tramite il clustering di serie temporali

Quando si visualizza un evento imprevisto, è possibile usare la scheda **Time-Series-clustering simile** per visualizzare le varie serie associate. Le serie in un gruppo vengono riepilogate insieme. Dall'immagine precedente, è possibile tenere presente che esistono almeno due gruppi di serie. Questa funzionalità è disponibile solo se sono soddisfatti i requisiti seguenti:

1. Le metriche devono avere una o più dimensioni o valori di dimensione.
2. La serie in una metrica deve avere una tendenza simile.

Le dimensioni disponibili sono elencate nella parte superiore della scheda ed è possibile effettuare una selezione per specificare la serie.

:::image type="content" source="../media/diagnostics/series-group.png" lightbox="../media/diagnostics/series-group.png"alt-text="Hub eventi imprevisti":::

## <a name="compare-time-series"></a>Confrontare la serie temporale

In alcuni casi, quando viene rilevata un'anomalia in una serie temporale specifica, è utile confrontarla con più serie in un'unica visualizzazione. Fare clic sulla scheda **Confronta strumenti** e quindi fare clic sul pulsante blu **+ Aggiungi** . 

:::image type="content" source="../media/diagnostics/add-series.png" alt-text="Hub eventi imprevisti" lightbox="../media/diagnostics/add-series.png":::

Selezionare una serie dal feed di dati. È possibile scegliere la stessa granularità o una diversa. Selezionare le dimensioni di destinazione e caricare la tendenza della serie, quindi fare clic su **OK** per confrontarla con una serie precedente. La serie verrà assemblata in una sola visualizzazione. È possibile continuare ad aggiungere altre serie per il confronto e ottenere altre informazioni dettagliate. Fare clic sul menu a discesa nella parte superiore della scheda **Confronta strumenti** per confrontare i dati delle serie temporali in un periodo di tempo.  

> [!Warning]
> Per eseguire un confronto, l'analisi dei dati delle serie temporali può richiedere turni nei punti dati in modo che la granularità dei dati debba supportarla. Se, ad esempio, i dati sono settimanali e si utilizza il confronto **giornaliero** , non sarà possibile ottenere risultati. In questo esempio si utilizzerà invece il confronto **mese per** mese.

Dopo aver selezionato un confronto con spostamento temporale, è possibile specificare se si desidera confrontare i valori dei dati, i valori delta o il Delta percentuale.

> [!Note]
> * Il **valore dei dati** è il valore dei dati non elaborati.
> * Il **valore Delta** è la differenza tra il valore non elaborato e il valore confrontato.
> * Il **valore Delta percentuale** è la differenza tra il valore non elaborato e il valore confrontato diviso per valore confrontato.

## <a name="related-incidents-across-metrics"></a>Eventi imprevisti correlati tra le metriche

In alcuni casi potrebbe essere necessario controllare gli eventi imprevisti di metriche diverse nello stesso momento o eventi imprevisti correlati in altre metriche. Per un elenco degli eventi imprevisti correlati, vedere la sezione **analisi delle metriche incrociate** . 

:::image type="content" source="../media/graph/metrics-graph-cross-metrics-analysis.png" alt-text="Hub eventi imprevisti":::

Prima di poter visualizzare gli eventi imprevisti correlati per la metrica corrente, è necessario aggiungere una relazione tra le metriche. Fare clic su **Impostazioni grafico metriche** per aggiungere una relazione. Possono essere correlate solo le metriche con lo stesso nome di dimensione. Usare i parametri seguenti.

- Metrica & feed di dati corrente: il feed di dati e la metrica dell'evento imprevisto corrente
- Direction: direzione della relazione tra due metriche. (non applicare l'elenco eventi imprevisti correlati adesso)
- Un altro feed di dati & metrica: il feed di dati e la metrica per connettersi con la metrica corrente


## <a name="next-steps"></a>Passaggi successivi 

- [Regolare il rilevamento anomalie usando il feedback](anomaly-feedback.md)
- [Configurare le metriche e ottimizzare la configurazione del rilevamento](configure-metrics.md)
