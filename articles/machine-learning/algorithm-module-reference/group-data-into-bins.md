---
title: 'Raggruppare i dati in contenitori: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Group Data into Bins (Raggruppa dati in bin) per raggruppare numeri o modificare la distribuzione di dati continui.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/13/2020
ms.openlocfilehash: 392cb9b4c2ded1b98b79ce8dcd780ac59e96b78a
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91998489"
---
# <a name="group-data-into-bins-module"></a>Raggruppare i dati nel modulo bin

Questo articolo descrive come usare i dati di gruppo nel modulo bin in Azure Machine Learning Designer, per raggruppare numeri o modificare la distribuzione di dati continui.

Il modulo Group Data into Bins (Raggruppa dati in bin) supporta più opzioni per il binning dei dati. È possibile personalizzare l'impostazione dei vertici di bin e la ripartizione dei valori nei bin. Ad esempio, è possibile:  

+ Digitare manualmente una serie di valori che fungano da limiti di bin.  
+ Assegnare valori ai bin usando classificazioni basate su *quantili* o percentili.  
+ Forzare una distribuzione uniforme di valori nei bin.  

## <a name="more-about-binning-and-grouping"></a>Altre informazioni su binning e raggruppamento

La suddivisione in *contenitori* o il raggruppamento dei dati (talvolta denominato *quantizzazione*) è uno strumento importante per la preparazione dei dati numerici per Machine Learning. È utile in scenari come i seguenti:

+ Una colonna di numeri continui contiene troppi valori univoci da modellare in modo efficace. Quindi, si assegnano automaticamente o manualmente i valori ai gruppi, per creare un set più piccolo di intervalli discreti.

+ Si desidera sostituire una colonna di numeri con valori categorici che rappresentano intervalli specifici.

    Si supponga, ad esempio, di voler raggruppare i valori di una colonna Età specificando intervalli personalizzati, quali 1-15, 16-22, 23-30 e così via, per i dati demografici degli utenti.

+ Un set di dati ha alcuni valori estremi, tutti ben al di fuori dell'intervallo previsto, e questi valori influiscono in modo fuorviante sul modello con training. Per attenuare la distorsione nel modello, è possibile trasformare i dati in una distribuzione uniforme usando il metodo quantili.

    Con questo metodo, il modulo Group Data into Bins (Raggruppa dati in bin) determina le posizioni e le larghezze dei bin ideali per assicurarsi che in ogni bin rientri approssimativamente lo stesso numero di campioni. Quindi, a seconda del metodo di normalizzazione scelto, i valori nei contenitori sono trasformati in percentile o con mapping a un numero di bin.

### <a name="examples-of-binning"></a>Esempi di binning

Il diagramma seguente illustra la distribuzione di valori numerici prima e dopo il binning con il metodo dei *quantili*. Si noti che, rispetto ai dati non elaborati a sinistra, i dati sono stati sottoposti a binning e trasformati in una scala normale.  

> [!div class="mx-imgBorder"]
> ![Visualizzazione risultati](media/module/group-data-into-bins-result-example.png)

Poiché esistono moltissimi modi per raggruppare i dati, e tutti sono personalizzabili, è consigliabile sperimentare metodi e valori diversi. 

## <a name="how-to-configure-group-data-into-bins"></a>Come configurare il modulo Group Data into Bins (Raggruppa dati in bin)

1. Aggiungere i **dati di gruppo nel modulo bins** alla pipeline nella finestra di progettazione. È possibile trovare questo modulo nella categoria **Trasformazione dati**.

2. Connettere il set di dati con dati numerici al bin. La quantizzazione può essere applicata solo alle colonne che contengono dati numerici. 

    Se il set di dati contiene colonne non numeriche, usare il modulo [Select Columns in Dataset](select-columns-in-dataset.md) (Seleziona colonne nel set di dati) per selezionare un subset di colonne da usare.

3. Specificare la modalità di binning. La modalità di suddivisione in contenitori determina altri parametri, quindi assicurarsi di selezionare prima l'opzione modalità di suddivisione in **contenitori** . Sono supportati i tipi di binning seguenti:

    - **Quantiles** (Quantili): il metodo dei quantili assegna valori ai bin in base a classificazioni di percentili. Questo metodo è noto anche come suddivisione in contenitori con altezza uguale.

    - **Uguale larghezza**: con questa opzione è necessario specificare il numero totale di contenitori. I valori della colonna di dati vengono inseriti nei contenitori in modo che ogni bin abbia lo stesso intervallo tra i valori di inizio e di fine. Di conseguenza, è possibile che alcuni bin abbiano più valori se i dati sono raggruppati intorno a un punto specifico.

    - **Custom Edges** (Vertici personalizzati): è possibile specificare i valori iniziali di ogni bin. Il valore dei vertici è sempre il limite inferiore del bin. 
    
      Si supponga, ad esempio, di voler raggruppare i valori in due contenitori. Uno avrà valori maggiori di 0 e uno avrà valori minori o uguali a 0. In questo caso, per i bordi di bin immettere **0** in un elenco delimitato **da virgole di bordi del cestino**. L'output del modulo sarà 1 e 2, che indica l'indice bin per ogni valore di riga. Si noti che l'elenco di valori separati da virgole deve essere in ordine crescente, ad esempio 1, 3, 5, 7.
    
    > [!Note]
    > La modalità *MDL entropia* è definita in studio (classico) e non esiste alcun pacchetto open source corrispondente che può essere utilizzato per supportare ancora nella finestra di progettazione.        

4. Se si usano le modalità di suddivisione in contenitori **quantili** e **Equal Width** , usare l'opzione **Number of bins** per specificare il numero di bin o *quantili*che si vuole creare.

5. Per le **colonne da**inserire, utilizzare il selettore di colonna per scegliere le colonne che contengono i valori che si desidera inserire. Il tipo di dati delle colonne deve essere numerico.

    La stessa regola di binning viene applicata a tutte le colonne scelte applicabili. Se è necessario suddividere alcune colonne usando un metodo diverso, usare un'istanza separata dei dati di gruppo nel modulo bin per ogni set di colonne.

    > [!WARNING]
    > Se si sceglie una colonna che non è un tipo consentito, viene generato un errore di Runtime. Il modulo restituisce un errore non appena trova una colonna di tipo non consentito. Se si riceve un errore, esaminare tutte le colonne selezionate. L'errore non elenca tutte le colonne non valide.

6. Per la **modalità di output**, indicare come si desidera restituire i valori quantizzati:

    + **Append**: crea una nuova colonna con i valori di contenitori e la aggiunge alla tabella di input.

    + **Inplace** (Sul posto): sostituisce i valori originali con i nuovi valori nel set di dati.

    + **ResultOnly** (Solo risultati): restituisce solo le colonne di risultati.

7. Se si seleziona la modalità di suddivisione in contenitori **quantili** , usare l'opzione di **normalizzazione quantile** per determinare il modo in cui i valori vengono normalizzati prima di eseguire l'ordinamento in quantili. Si noti che la normalizzazione dei valori trasforma i valori ma non influisce sul numero finale di contenitori.

    Sono supportati i tipi di normalizzazione seguenti:

    + **Percentuale**: i valori vengono normalizzati nell'intervallo [0100].

    + **PQuantile**: i valori vengono normalizzati nell'intervallo [0, 1].

    + **QuantileIndex**: i valori vengono normalizzati nell'intervallo [1, numero di contenitori].

8. Se si sceglie l'opzione **Custom Edges** , immettere un elenco delimitato da virgole di numeri da usare come *bordi del cestino* nella casella di testo elenco delimitato **da virgole di bordi del cestino** . 

    I valori indicano il punto che divide i contenitori. Ad esempio, se si immette un valore del bordo del cestino, verranno generati due contenitori. Se si immettono due valori del bordo del cestino, verranno generati tre contenitori.

    I valori devono essere ordinati in base all'ordine in cui vengono creati i contenitori, dal più basso al più alto.

10. Selezionare l'opzione **Contrassegna colonne come categoriche** per indicare che le colonne quantizzate devono essere gestite come variabili categoriche.

11. Inviare la pipeline.

## <a name="results"></a>Risultati

Il modulo Group Data into Bins (Raggruppa dati in bin) restituisce un set di dati in cui è stato eseguito il binning di ogni elemento secondo la modalità specificata. 

Restituisce inoltre una *trasformazione di contenitori*. Questa funzione può essere passata al modulo [Apply Transformation](apply-transformation.md) per contenere nuovi esempi di dati usando la stessa modalità di suddivisione in contenitori e i parametri.  

> [!TIP]
> Se si utilizza la suddivisione in contenitori sui dati di training, è necessario utilizzare lo stesso metodo di suddivisione in contenitori sui dati utilizzati per il test e la stima. È inoltre necessario utilizzare le stesse posizioni bin e le stesse larghezze del cestino. 
> 
> Per garantire che i dati vengano sempre trasformati usando lo stesso metodo di suddivisione in contenitori, è consigliabile salvare le trasformazioni di dati utili. Quindi applicarli ad altri set di impostazioni usando il modulo [Apply Transformation](apply-transformation.md) .

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 
