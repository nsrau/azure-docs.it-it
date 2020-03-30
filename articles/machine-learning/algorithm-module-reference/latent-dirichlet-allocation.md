---
title: Allocazione latente del dirichlet
titleSuffix: Azure Machine Learning
description: Informazioni su come utilizzare il modulo Allocazione Dirichlet latente per raggruppare testo altrimenti non classificato in una serie di categorie.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/11/2020
ms.openlocfilehash: 1384491489c175ffc338f80a99aa8d5050f835d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80109225"
---
# <a name="latent-dirichlet-allocation"></a>Allocazione latente del dirichlet

Questo articolo descrive come usare il modulo **Allocazione Dirichlet latent** in Progettazione di Azure Machine Learning (anteprima) per raggruppare testo altrimenti non classificato in un numero di categorie. 

Latent Dirichlet Allocation (LDA) è spesso usato nell'elaborazione del linguaggio naturale (NLP) per trovare testi simili. Un altro termine comune è *la modellazione degli argomenti*.

Questo modulo accetta una colonna di testo e genera questi output:This module takes a column of text, and generates these outputs:

+ Il testo di origine, insieme a un punteggio per ogni categoria

+ Matrice di funzionalità, contenente termini e coefficienti estratti per ogni categoria

+ Una trasformazione che è possibile salvare e riapplicare al nuovo testo

Questo modulo utilizza la libreria scikit-learn. Per altre informazioni su scikit-learn, vedere il [repository GitHub, che include esercitazioni e una spiegazione dell'algoritmo.

### <a name="more-about-latent-dirichlet-allocation-lda"></a>Più informazioni su Latent Dirichlet Allocation (LDA)

In generale, LDA non è un metodo per la classificazione di per sé, ma utilizza un approccio generativo. Ciò significa che non è necessario fornire etichette di classe note e quindi dedurre i modelli.  L'algoritmo genera invece un modello probabilistico utilizzato per identificare gruppi di argomenti. È possibile utilizzare il modello probabilistico per classificare i casi di training esistenti o i nuovi casi forniti al modello come input.

Un modello generativo può essere preferibile perché evita di formulare ipotesi forti sulla relazione tra testo e categorie e utilizza solo la distribuzione di parole per modellare matematicamente gli argomenti.

+ La teoria è discussa in questo articolo, disponibile in download PDF: [Latent Dirichlet Allocation: Blei, Ng, and Jordan](https://ai.stanford.edu/~ang/papers/nips01-lda.pdf)

+ L'implementazione in questo modulo si basa sulla [libreria scikit-learn](https://github.com/scikit-learn/scikit-learn/blob/master/sklearn/decomposition/_lda.py) per LDA.

Per ulteriori informazioni, vedere la sezione [Note tecniche.](#technical-notes)

## <a name="how-to-configure-latent-dirichlet-allocation"></a>Come configurare l'allocazione di Dirichlet latente

Questo modulo richiede un set di dati che contiene una colonna di testo, non elaborato o pre-elaborato.

1. Aggiungere il modulo **Allocazione Dirichlet latente** alla pipeline.

2. Come input per il modulo, fornire un set di dati contenente una o più colonne di testo.

3. Per **Colonne di destinazione**, scegliere una o più colonne contenenti testo da analizzare.

    È possibile scegliere più colonne, ma devono essere del tipo di dati string.

    In generale, poiché LDA crea una matrice di funzionalità di grandi dimensioni dal testo, in genere si analizza una singola colonna di testo.

4. Per **Numero di argomenti da modellare**, digitare un numero intero compreso tra 1 e 1000 che indica il numero di categorie o argomenti che si desidera derivare dal testo di input.

    Per impostazione predefinita, vengono creati 5 argomenti.

5. Per **N-grammi**, specificare la lunghezza massima di N grammi generati durante l'hashing.

    Il valore predefinito è 2, ovvero vengono generati sia bigrammi che unigrammi.

6. Selezionare l'opzione **Normalizza** per convertire i valori di output in probabilità. Pertanto, anziché rappresentare i valori trasformati come numeri interi, i valori nel set di dati di output e di funzionalità verrebbero trasformati come segue:

    + I valori nel set di dati `P(topic|document)`verranno rappresentati come probabilità in cui .

    + I valori nella matrice dell'argomento della `P(word|topic)`caratteristica verranno rappresentati come probabilità in cui .

    > [!NOTE] 
    > In Azure Machine Learning Designer (anteprima), poiché la libreria basata su scikit-learn non supporta più l'output *di doc_topic_distr* non normalizzata della versione 0.19, pertanto, in questo modulo, il parametro **Normalize** può essere applicato solo all'output della **matrice Feature Topic,** **l'output del set** di dati trasformato viene sempre normalizzato.

7. Selezionare l'opzione **Mostra tutte le opzioni**, quindi impostarla su TRUE se si desidera visualizzare e quindi impostare parametri avanzati aggiuntivi.

    Questi parametri sono specifici per l'implementazione scikit-learn di LDA. Ci sono alcuni buoni tutorial su LDA in scikit-imparare, così come il documento ufficiale [scikit-imparare](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.LatentDirichletAllocation.html).

    + **Parametro Rho**. Fornire una probabilità preliminare per la scambio delle distribuzioni di argomenti. Corrisponde al parametro `topic_word_prior` di sklearn. Si userebbe il valore 1 se si prevede che la distribuzione delle parole è piatta; cioè, tutte le parole sono considerate equiprobabili. Se si ritiene che la maggior parte delle parole venga visualizzata raramente, è possibile impostarla su un valore molto inferiore.

    + **Parametro alfa**. Specificare una probabilità precedente per la sparsità dei pesi dell'argomento per documento.  Corrisponde al parametro `doc_topic_prior` di sklearn.

    + **Numero stimato di documenti**. Digitare un numero che rappresenta la stima migliore del numero di documenti (righe) che verranno elaborati. Ciò consente al modulo di allocare una tabella hash di dimensioni sufficienti.  Corrisponde al `total_samples` parametro in scikit-learn.

    + **Dimensione del lotto**. Digitare un numero che indica il numero di righe da includere in ogni batch di testo inviato al modello LDA. Corrisponde al `batch_size` parametro in scikit-learn.

    + **Valore iniziale dell'iterazione utilizzato nella pianificazione dell'aggiornamento dell'apprendimento**. Specificare il valore iniziale che riduce la velocità di apprendimento per le iterazioni iniziali nell'apprendimento online. Corrisponde al `learning_offset` parametro in scikit-learn.

    + **Potenza applicata all'iterazione durante gli aggiornamenti.** Indicare il livello di potenza applicato al conteggio delle iterazioni per controllare la velocità di apprendimento durante gli aggiornamenti online. Corrisponde al `learning_decay` parametro in scikit-learn.

    + **Numero di passaggi sui dati**. Specificare il numero massimo di volte in cui l'algoritmo verrà eseguito sui dati. Corrisponde al `max_iter` parametro in scikit-learn.

8. Selezionare l'opzione **Crea dizionario di ngrammi** o **Crea dizionario di ngrammi prima di LDA**, se si desidera creare l'elenco di n grammi in un passaggio iniziale, prima di classificare il testo.

    Se si crea in anticipo il dizionario iniziale, è possibile utilizzarlo in un secondo momento durante la revisione del modello. La possibilità di mappare i risultati in indici di testo anziché numerici è generalmente più facile da interpretazione. Tuttavia, il salvataggio del dizionario richiederà più tempo e utilizzerà spazio di archiviazione aggiuntivo.

9. Per **Dimensione massima del dizionario ngram**digitare il numero totale di righe che è possibile creare nel dizionario n grammi.

    Questa opzione è utile per controllare le dimensioni del dizionario. Tuttavia, se il numero di ngrammi nell'ingresso supera questa dimensione, possono verificarsi collisioni.

10. Inviare la pipeline. Il modulo LDA utilizza il teorema di Bayes per determinare quali argomenti potrebbero essere associati alle singole parole. Le parole non sono associate esclusivamente ad alcun argomento o gruppo; invece, ogni n-grammo ha una probabilità appresa di essere associato a una qualsiasi delle classi scoperte.

## <a name="results"></a>Risultati

Il modulo ha due output:

+ **Set di dati trasformato**: Contiene il testo di input e un numero specificato di categorie individuate, insieme ai punteggi per ogni esempio di testo per ogni categoria.

+ **Matrice dell'argomento della funzionalità:** la colonna più a sinistra contiene la funzionalità di testo estratto ed è presente una colonna per ogni categoria contenente il punteggio per tale funzionalità in tale categoria.


### <a name="lda-transformation"></a>Trasformazione LDA

Questo modulo restituisce anche la *trasformazione LDA* che applica LDA al set di dati.

È possibile salvare questa trasformazione registrando il set di dati nella scheda **Outputs e registri** nel riquadro destro del modulo e riutilizzarlo per altri set di dati. Ciò potrebbe essere utile se è stato eseguito il training su un corpo di grandi dimensioni e si desidera riutilizzare i coefficienti o le categorie.

### <a name="refining-an-lda-model-or-results"></a>Perfezionamento di un modello o dei risultati LDA

In genere non è possibile creare un singolo modello LDA che soddisfi tutte le esigenze e anche un modello progettato per un'attività potrebbe richiedere molte iterazioni per migliorare l'accuratezza. Si consiglia di provare tutti questi metodi per migliorare il modello:We recommend that you try all these methods to improve your model:

+ Modifica dei parametri del modello
+ Utilizzo della visualizzazione per comprendere i risultati
+ Ottenere il feedback degli esperti in materia per accertare se gli argomenti generati sono utili.

Misure qualitative possono essere utili anche per valutare i risultati. Per valutare i risultati della modellazione degli argomenti, considerare quanto segue:To evaluate topic modeling results, consider:

+ Precisione - Gli elementi simili sono davvero simili?
+ Diversità - Il modello può discriminare tra elementi simili quando necessario per il problema aziendale?
+ Scalabilità: funziona su un'ampia gamma di categorie di testo o solo su un dominio di destinazione limitato?

L'accuratezza dei modelli basati su LDA può spesso essere migliorata utilizzando l'elaborazione del linguaggio naturale per pulire, riepilogare e semplificare o classificare il testo. Ad esempio, le tecniche seguenti, tutte supportate in Azure Machine Learning, possono migliorare l'accuratezza della classificazione:For example, the following techniques, all supported in Azure Machine Learning, can improve classification accuracy:

+ Eliminazione parole vuote

+ Normalizzazione dei casi

+ Lemmatizzazione o stemming

+ Riconoscimento di entità denominate

Per ulteriori informazioni, consultate [Pre-elaborazione del testo.](preprocess-text.md)

Nella finestra di progettazione, è anche possibile usare le librerie R o Python per l'elaborazione del testo: [Execute R Script](execute-r-script.md), Execute Python [Script](execute-python-script.md)



## <a name="technical-notes"></a>Note tecniche

Questa sezione contiene dettagli sull'implementazione, suggerimenti e risposte alle domande frequenti.

### <a name="implementation-details"></a>Dettagli dell'implementazione

Per impostazione predefinita, le distribuzioni degli output per il set di dati trasformato e la matrice di argomenti di funzionalità vengono normalizzate come probabilità.

+ Il set di dati trasformato viene normalizzato come probabilità condizionale degli argomenti forniti in un documento. In questo caso, la somma di ogni riga è uguale a 1.

+ La matrice caratteristica-argomento viene normalizzata come probabilità condizionale di parole dato un argomento. In questo caso, la somma di ogni colonna è uguale a 1.

> [!TIP]
> Occasionalmente il modulo potrebbe restituire un argomento vuoto, che è più spesso causato dall'inizializzazione pseudo-casuale dell'algoritmo.  In questo caso, è possibile provare a modificare i parametri correlati, ad esempio la dimensione massima del dizionario N-gram o il numero di bit da utilizzare per l'hashing delle funzionalità.

### <a name="lda-and-topic-modeling"></a>LDA e modellazione degli argomenti

Latent Dirichlet Allocation (LDA) viene spesso utilizzato per la modellazione di *argomenti basata sul contenuto*, che fondamentalmente significa imparare categorie da testo non classificato. Nella modellazione degli argomenti basata sul contenuto, un argomento è una distribuzione sulle parole.

Ad esempio, si supponga di aver fornito un corpus di recensioni dei clienti che include molti, molti prodotti. Il testo delle recensioni che sono state inviate da molti clienti nel corso del tempo conterrebbe molti termini, alcuni dei quali vengono utilizzati in più argomenti.

Un **argomento** identificato dal processo LDA potrebbe rappresentare revisioni per un singolo prodotto A oppure un gruppo di revisioni di prodotti. Per LDA, l'argomento stesso è solo una distribuzione di probabilità nel tempo per un insieme di parole.

I termini sono raramente esclusivi di un prodotto, ma possono riferirsi ad altri prodotti, o essere termini generali che si applicano a tutto ("grande", "orribile"). Altri termini potrebbero essere parole rumore.  Tuttavia, è importante capire che il metodo LDA non pretende di catturare tutte le parole nell'universo, o di capire come le parole sono correlate, a parte le probabilità di co-occorrenza. Può raggruppare solo le parole utilizzate nel dominio di destinazione.

Dopo aver calcolato i termini indici, le singole righe di testo vengono confrontate utilizzando una misura di somiglianza basata sulla distanza, per determinare se due parti di testo sono simili tra loro.  Ad esempio, è possibile che il prodotto abbia più nomi fortemente correlati. Oppure, potresti scoprire che termini fortemente negativi sono di solito associati a un particolare prodotto. È possibile utilizzare la misura di somiglianza sia per identificare i termini correlati che per creare raccomandazioni.

###  <a name="module-parameters"></a>Parametri del modulo

|Nome|Type|Range|Facoltativo|Predefinito|Descrizione|  
|----------|----------|-----------|--------------|-------------|-----------------|  
|Target column(s)|Selezione colonne||Obbligatoria|StringFeature|Nome o indice della colonna di destinazione|  
|Numero di argomenti da modellare|Integer|[1;1000]|Obbligatoria|5|Modellare la distribuzione dei documenti in base a N argomenti|  
|N-grams|Integer|[1;10]|Obbligatoria|2|Ordine degli N-grammi generati durante l'hashing|  
|Normalizzare|Boolean|true o false|Obbligatoria|true|Normalizzare l'output in base alle probabilità.  Il set di dati trasformato sarà P(argomento&#124;documento) e la matrice dell'argomento della funzionalità sarà P(word&#124;argomento)|  
|Mostra tutte le opzioni|Boolean|true o false|Obbligatoria|False|Presenta parametri aggiuntivi specifici per Scikit-learn online LDA|  
|Parametro Rho|Float|[0.00001;1.0]|Si applica quando la casella di controllo **Mostra tutte le opzioni** è selezionata|0.01|Distribuzione precedente delle parole|  
|Parametro alfa|Float|[0.00001;1.0]|Si applica quando la casella di controllo **Mostra tutte le opzioni** è selezionata|0.01|Documentazione argomento distribuzione precedente|  
|Numero stimato di documenti|Integer|[1;int.MaxValue]|Si applica quando la casella di controllo **Mostra tutte le opzioni** è selezionata|1000|Numero stimato di documenti (corrisponde al parametro total_samples)|  
|Dimensione del lotto|Integer|[1;1024]|Si applica quando la casella di controllo **Mostra tutte le opzioni** è selezionata|32|Dimensione del lotto|  
|Valore iniziale dell'iterazione utilizzato nella pianificazione degli aggiornamenti della frequenza di apprendimento|Integer|[0;int. Valore massimo]|Si applica quando la casella di controllo **Mostra tutte le opzioni** è selezionata|0|Valore iniziale che riduce la velocità di apprendimento per le iterazioni iniziali. Corrisponde al parametro learning_offset|  
|Alimentazione applicata all'iterazione durante gli aggiornamenti|Float|[0,0;1,0]|Si applica quando la casella di controllo **Mostra tutte le opzioni** è selezionata|0.5|Potenza applicata al conteggio delle iterazioni per controllare la velocità di apprendimento. Corrisponde al parametro learning_decay |  
|Number of training iterations|Integer|[1;1024]|Si applica quando la casella di controllo **Mostra tutte le opzioni** è selezionata|25|Number of training iterations|  
|Costruire dizionario di ngrams|Boolean|true o false|Si applica quando la casella di controllo **Mostra tutte le opzioni** *non* è selezionata|True|Compila un dizionario di ngrammi prima di calcolare LDA. Utile per l'ispezione e l'interpretazione del modello|  
|Dimensione massima del dizionario ngram|Integer|[1;int.MaxValue]|Si applica quando l'opzione **Crea dizionario di ngrams** è True|20000|Dimensione massima del dizionario ngrams. Se il numero di gettoni nell'ingresso supera questa dimensione, possono verificarsi collisioni|  
|Numero di bit da utilizzare per l'hashing delle funzionalità|Integer|[1;31]|Si applica quando la casella di controllo **Mostra tutte le opzioni** *non* è selezionata e **Crea dizionario di ngrammi** è False|12|Numero di bit da utilizzare per l'hashing delle funzionalità| 
|Costruire dizionario di ngrams prima di LDA|Boolean|true o false|Si applica quando la casella di controllo **Mostra tutte le opzioni** è selezionata|True|Compila un dizionario di ngrammi prima di LDA. Utile per l'ispezione e l'interpretazione del modello|  
|Numero massimo di ngrammi nel dizionario|Integer|[1;int.MaxValue]|Si applica quando la casella di controllo **Mostra tutte le opzioni** è selezionata e l'opzione Crea **dizionario di ngrammi** è True|20000|Dimensione massima del dizionario. Se il numero di gettoni nell'ingresso supera questa dimensione, possono verificarsi collisioni|  
|Numero di bit hash|Integer|[1;31]|Si applica quando la casella di controllo **Mostra tutte le opzioni** è selezionata e l'opzione Crea **dizionario di ngrammi** è False|12|Numero di bit da utilizzare durante l'hashing della funzionalità|   


## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning.   
Per un elenco degli errori specifici dei moduli, vedere Eccezioni e codici di [errore per la finestra di progettazione](designer-error-codes.md).
