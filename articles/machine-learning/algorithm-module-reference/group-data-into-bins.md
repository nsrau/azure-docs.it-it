---
title: Raggruppare i dati in contenitori
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Group Data into Bins (Raggruppa dati in bin) per raggruppare numeri o modificare la distribuzione di dati continui.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/19/2020
ms.openlocfilehash: b33aa3d2ab22fc986100d6141dd03d5547ef1862
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83853733"
---
# <a name="group-data-into-bins"></a>Raggruppare i dati in contenitori

Questo articolo descrive come usare il modulo[Group Data into Bins](group-data-into-bins.md) (Raggruppa dati in bin) nella finestra di progettazione di Azure Machine Learning (anteprima) per raggruppare numeri o modificare la distribuzione di dati continui.

Il modulo [Group Data into Bins](group-data-into-bins.md) (Raggruppa dati in bin) supporta più opzioni per il binning dei dati. È possibile personalizzare l'impostazione dei vertici di bin e la ripartizione dei valori nei bin. Ad esempio, è possibile:  

+ Digitare manualmente una serie di valori che fungano da limiti di bin.  
+ Assegnare valori ai bin usando classificazioni basate su *quantili* o percentili.  
+ Forzare una distribuzione uniforme di valori nei bin.  

### <a name="more-about-binning-and-grouping"></a>Altre informazioni su binning e raggruppamento

Il *binning* o raggruppamento dei dati (detto talvolta *quantizzazione*) è uno strumento importante per la preparazione dei dati numerici per la funzionalità di Machine Learning ed è utile nell'ambito di scenari come i seguenti:

+ Una colonna di numeri continui ha un numero troppo elevato di valori univoci per una modellazione efficace. Si assegnano quindi, automaticamente o manualmente, i valori a gruppi, per creare un set più piccolo di intervalli discreti.

+ Sostituire una colonna di numeri con valori categorici che rappresentano intervalli specifici.

    Si supponga, ad esempio, di voler raggruppare i valori di una colonna Età specificando intervalli personalizzati, quali 1-15, 16-22, 23-30 e così via, per i dati demografici degli utenti.

+ Un set di dati ha alcuni valori estremi, tutti ben al di fuori dell'intervallo previsto, e questi valori influiscono in modo fuorviante sul modello con training. Per attenuare la distorsione nel modello, è possibile trasformare i dati in una distribuzione uniforme, usando il metodo dei quantili.

    Con questo metodo, il modulo [Group Data into Bins](group-data-into-bins.md) (Raggruppa dati in bin) determina le posizioni e le larghezze dei bin ideali per assicurarsi che in ogni bin rientri approssimativamente lo stesso numero di campioni. Quindi, a seconda del metodo di normalizzazione scelto, i valori nei bin vengono trasformati in percentili oppure ne viene eseguito il mapping a un numero di bin.

### <a name="examples-of-binning"></a>Esempi di binning

Il diagramma seguente illustra la distribuzione di valori numerici prima e dopo il binning con il metodo dei **quantili**. Si noti che, rispetto ai dati non elaborati a sinistra, i dati sono stati sottoposti a binning e trasformati in una scala normale.  

'È possibile trovare un esempio nel risultato dell'esecuzione di questa pipeline: https://ml.azure.com/visualinterface/authoring/Normal/87270db9-4651-448e-bd28-8ef7428084dc?wsid=%2Fsubscriptions%2Fe9b2ec51-5c94-4fa8-809a-dc1e695e4896%2Fresourcegroups%2Fmodule-ws-rg%2Fworkspaces%2Fmodule-prerelease-119&flight=cm&tid=72f988bf-86f1-41af-91ab-2d7cd011db47&smtendpoint=https%3A%2F%2Fsmt-test1.azureml-test.net '

Poiché esistono moltissimi modi per raggruppare i dati, e tutti sono personalizzabili, è consigliabile sperimentare metodi e valori diversi. 

## <a name="how-to-configure-group-data-into-bins"></a>Come configurare il modulo Group Data into Bins (Raggruppa dati in bin)

1. Aggiungere il modulo **Group Data Into Bins** (Raggruppa dati in bin) alla pipeline nella finestra di progettazione (anteprima). È possibile trovare questo modulo nella categoria **Trasformazione dati**.

2. Connettere il set di dati con dati numerici al bin.  La quantizzazione può essere applicata solo a colonne contenenti dati numerici. 

    Se il set di dati contiene colonne non numeriche, usare il modulo [Select Columns in Dataset](select-columns-in-dataset.md) (Seleziona colonne nel set di dati) per selezionare un subset di colonne da usare.

3. Specificare la modalità di binning. La modalità di binning determina altri parametri. Assicurarsi quindi di selezionare prima l'opzione **Binning mode** (Modalità di binning). Sono supportati i tipi di binning seguenti:

    **Quantiles** (Quantili): il metodo dei quantili assegna valori ai bin in base a classificazioni di percentili. Il metodo dei quantili è noto anche come binning con altezza uguale.

    **Equal Width** (Stessa larghezza): con questa opzione è necessario specificare il numero totale di bin. I valori della colonna di dati vengono inseriti nei bin in modo tale che l'intervallo tra il valore di inizio e quello di fine sia lo stesso per ogni bin. Di conseguenza, è possibile che alcuni bin abbiano più valori se i dati sono raggruppati intorno a un punto specifico.

    **Custom Edges** (Vertici personalizzati): è possibile specificare i valori iniziali di ogni bin. Il valore dei vertici è sempre il limite inferiore del bin.  Si supponga, ad esempio, di voler raggruppare valori in due bin, uno con valori maggiori di 0 e uno con valori minori o uguali a 0. In questo caso, per i vertici di bin è necessario digitare 0 in **Comma-separated list of bin edges** (Elenco di vertici di bin delimitati da virgole). L'output del modulo, 1 e 2, indica l'indice bin per ogni valore di riga. Si noti che l'elenco di valori delimitati da virgole deve essere in ordine crescente, ad esempio 1, 3, 5, 7.

4. **Number of bins** (Numero di bin): se si usano le modalità di binning **Quantiles** (Quantili) ed **Equal Width** (Stessa larghezza), usare questa opzione per specificare il numero di bin o *quantili* che si vogliono creare.

5. Per **Columns to bin** (Colonne per il binning) usare il selettore di colonne per scegliere le colonne contenenti i valori di cui si vuole eseguire il binning. Il tipo di dati delle colonne deve essere numerico.

    La stessa regola di binning viene applicata a tutte le colonne scelte applicabili. Se pertanto è necessario eseguire il binning di alcune colonne con un metodo diverso, usare un'istanza separata di [Group Data into Bins](group-data-into-bins.md) (Raggruppa dati in bin) per ogni set di colonne.

    > [!WARNING]
    > Se si sceglie una colonna di tipo non consentito, viene generato un errore di run-time. Il modulo restituisce un errore non appena trova una colonna di tipo non consentito. Se si riceve un errore, esaminare tutte le colonne selezionate. L'errore non elenca tutte le colonne non valide.

6. Per **Output mode** (Modalità di output) indicare come si vogliono restituire i valori quantizzati.

      + **Append** (Accoda): crea una nuova colonna con valori sottoposti a binning e la accoda alla tabella di input.

      + **Inplace** (Sul posto): sostituisce i valori originali con i nuovi valori nel set di dati.

      + **ResultOnly** (Solo risultati): restituisce solo le colonne di risultati.

7. Se si seleziona la modalità di binning **Quantiles** (Quantili), usare l'opzione **Quantile normalization** (Normalizzazione quantili) per determinare la normalizzazione dei valori prima dell'ordinamento in quantili. Si noti che la normalizzazione dei valori trasforma i valori, ma non influisce sul numero finale di bin.

    Sono supportati i tipi di normalizzazione seguenti:

    + **Percent**: i valori vengono normalizzati nell'intervallo [0,100]

    + **PQuantile**: i valori vengono normalizzati nell'intervallo [0,1]

    + **QuantileIndex**:  i valori vengono normalizzati nell'intervallo [1,numero di bin]

8. Se si sceglie l'opzione **Custom Edges** (Vertici personalizzati), digitare un elenco delimitato da virgole di numeri da usare come *vertici di bin* nella casella di testo + **Comma-separated list of bin edges** (Elenco di vertici di bin delimitati da virgole).  I valori indicano il punto che divide i bin. Se pertanto si digita un valore di vertice di bin, vengono generati due bin. Se si digitano due valori di vertice di bin, verranno generati tre bin e così via.

    I valori devono essere elencati secondo l'ordine di creazione dei bin, dal più basso al più alto.

10. **Tag columns as categorical** (Contrassegna colonne come categoriche): selezionare questa opzione per indicare che le colonne quantizzate devono essere gestite come variabili categoriche.

11. Inviare la pipeline.

### <a name="results"></a>Risultati

Il modulo [Group Data into Bins](group-data-into-bins.md) (Raggruppa dati in bin) restituisce un set di dati in cui è stato eseguito il binning di ogni elemento secondo la modalità specificata. 

Restituisce anche la funzione **Binning transformation** (Trasformazione binning), che può essere passata al modulo [Apply Transformation](apply-transformation.md) (Applica trasformazione) per eseguire il binning di nuovi campioni di dati con la stessa modalità e gli stessi parametri di binning.  

> [!TIP]
> Si tenga presente che se si usa il binning sui dati di training, è necessario usare lo stesso metodo di binning sui dati usati per i test e le stime. Questo vale per il metodo di binning e per le posizioni e le larghezze dei bin. 
> 
> Per assicurarsi che i dati vengano sempre trasformati usando lo stesso metodo di binning, è consigliabile salvare le trasformazioni dati utili e quindi applicarle ad altri set di dati usando il modulo [Apply Transformation](apply-transformation.md) (Applica trasformazione).

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 
