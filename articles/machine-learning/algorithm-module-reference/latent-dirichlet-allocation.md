---
title: 'Allocazione Dirichlet latente: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo di allocazione Dirichlet latente per raggruppare testo altrimenti non classificato in categorie.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 06/05/2020
ms.openlocfilehash: f9f239ea69aaf71e591a447feb300c13a45ba1a4
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90907846"
---
# <a name="latent-dirichlet-allocation-module"></a>Modulo di allocazione Dirichlet latente

Questo articolo descrive come usare il modulo di allocazione Dirichlet latente in Azure Machine Learning Designer, per raggruppare testo altrimenti non classificato in categorie. 

L'allocazione Dirichlet latente (LDA) viene spesso usata nell'elaborazione del linguaggio naturale per trovare testi simili. Un altro termine comune è la *modellazione dell'argomento*.

Questo modulo accetta una colonna di testo e genera gli output seguenti:

+ Testo di origine, insieme a un punteggio per ogni categoria

+ Matrice di funzionalità che contiene termini e coefficienti estratti per ogni categoria

+ Trasformazione, che è possibile salvare e riapplicare al nuovo testo usato come input

Questo modulo usa la libreria Scikit-learn. Per altre informazioni su Scikit-learn, vedere il [repository GitHub](https://github.com/scikit-learn/scikit-learn), che include le esercitazioni e una spiegazione dell'algoritmo.

## <a name="more-about-latent-dirichlet-allocation"></a>Altre informazioni sull'allocazione Dirichlet latente

LDA non è in genere un metodo per la classificazione. Ma usa un approccio generativo, pertanto non è necessario fornire etichette di classe note e quindi dedurre i modelli.  L'algoritmo genera invece un modello probabilistico che viene usato per identificare i gruppi di argomenti. È possibile utilizzare il modello probabilistico per classificare i case di training esistenti o i nuovi case forniti al modello come input.

È possibile che si preferisca un modello generativo perché evita di creare presupposti sicuri sulla relazione tra il testo e le categorie. USA solo la distribuzione delle parole per gli argomenti del modello matematico.

La teoria è illustrata in questo documento, disponibile come download PDF: [allocazione Dirichlet latente: Blei, ng e Giordania](https://ai.stanford.edu/~ang/papers/nips01-lda.pdf).

L'implementazione in questo modulo si basa sulla [libreria Scikit-learn](https://github.com/scikit-learn/scikit-learn/blob/master/sklearn/decomposition/_lda.py) per Lda.

Per ulteriori informazioni, vedere la sezione [Note tecniche](#technical-notes) .

## <a name="how-to-configure-latent-dirichlet-allocation"></a>Come configurare l'allocazione Dirichlet latente

Questo modulo richiede un set di dati contenente una colonna di testo, non elaborata o pre-elaborata.

1. Aggiungere il modulo di **allocazione Dirichlet latente** alla pipeline.

2. Come input per il modulo, fornire un set di dati che contenga una o più colonne di testo.

3. Per le **colonne di destinazione**, scegliere una o più colonne contenenti testo da analizzare.

    È possibile scegliere più colonne, ma devono essere del tipo di dati **stringa** .

    Poiché LDA crea una matrice di funzionalità di grandi dimensioni dal testo, in genere si analizzerà una singola colonna di testo.

4. Per  **numero di argomenti da modellare**, immettere un numero intero compreso tra 1 e 1000 indicante il numero di categorie o argomenti che si desidera derivare dal testo di input.

    Per impostazione predefinita, vengono creati 5 argomenti.

5. Per **n-grammi**, specificare la lunghezza massima di n-grammi generati durante l'hashing.

    Il valore predefinito è 2, vale a dire che vengono generati sia bigrammi che unigrammi.

6. Selezionare l'opzione **normalizza** per convertire i valori di output in probabilità. 

    Anziché rappresentare i valori trasformati come numeri interi, i valori nel set di dati di output e funzionalità verranno trasformati come segue:

    + I valori nel set di dati verranno rappresentati come probabilità in cui `P(topic|document)` .

    + I valori nella matrice dell'argomento della funzionalità verranno rappresentati come probabilità in cui `P(word|topic)` .

    > [!NOTE] 
    > In Azure Machine Learning Designer, la libreria Scikit-learn non supporta più l'output *doc_topic_distr* non normalizzato della versione 0,19. In questo modulo il parametro **Normalize** può essere applicato solo all'output della *matrice dell'argomento Feature* . L'output del *set di dati trasformato* è sempre normalizzato.

7. Selezionare l'opzione **Mostra tutte le opzioni**e impostarla su **true** se si desidera impostare i parametri avanzati seguenti.

    Questi parametri sono specifici dell'implementazione di Scikit-learn di LDA. Sono disponibili alcune esercitazioni valide su LDA in Scikit-learn, oltre al documento ufficiale di [Scikit-learn](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.LatentDirichletAllocation.html).

    + **Parametro Rho**. Fornire una probabilità precedente per il densità di distribuzioni di argomenti. Questo parametro corrisponde al parametro di sklearn `topic_word_prior` . Usare il valore **1** se si prevede che la distribuzione delle parole sia piatta; ovvero si presuppone che tutte le parole siano equiprobable. Se si ritiene che la maggior parte delle parole risultino sparse, è possibile impostarla su un valore inferiore.

    + **Parametro Alpha**. Specificare una probabilità precedente per il densità dei pesi degli argomenti per documento. Questo parametro corrisponde al parametro di sklearn `doc_topic_prior` .

    + **Numero stimato di documenti**. Immettere un numero che rappresenti la stima ottimale del numero di documenti (righe) che verranno elaborati. Questo parametro consente al modulo di allocare una tabella hash di dimensioni sufficienti. Corrisponde al `total_samples` parametro in Scikit-learn.

    + **Dimensioni del batch**. Immettere un numero che indichi il numero di righe da includere in ogni batch di testo inviato al modello LDA. Questo parametro corrisponde al `batch_size` parametro in Scikit-learn.

    + **Valore iniziale di iterazione utilizzato nella pianificazione dell'aggiornamento dell'apprendimento**. Specificare il valore iniziale che downweights la velocità di apprendimento per le prime iterazioni nell'apprendimento online. Questo parametro corrisponde al `learning_offset` parametro in Scikit-learn.

    + **Potenza applicata all'iterazione durante gli aggiornamenti**. Indica il livello di potenza applicato al numero di iterazioni per controllare la velocità di apprendimento durante gli aggiornamenti online. Questo parametro corrisponde al `learning_decay` parametro in Scikit-learn.

    + **Numero di passaggi sui dati**. Consente di specificare il numero massimo di volte in cui l'algoritmo scorrerà i dati. Questo parametro corrisponde al `max_iter` parametro in Scikit-learn.

8. Selezionare l'opzione **dizionario di compilazione di Ngrams** o **dizionario di compilazione di NGRAMS prima di LDA**, se si desidera creare l'elenco di n-grammi in un passaggio iniziale prima di classificare il testo.

    Se si crea prima il dizionario iniziale, sarà possibile usare il dizionario in un secondo momento per esaminare il modello. La possibilità di eseguire il mapping dei risultati al testo anziché agli indici numerici è in genere più facile da interpretare. Tuttavia, il salvataggio del dizionario richiederà più tempo e utilizzerà memoria aggiuntiva.

9. Per le **dimensioni massime del dizionario Ngram**, immettere il numero totale di righe che è possibile creare nel dizionario n-Gram.

    Questa opzione è utile per controllare le dimensioni del dizionario. Tuttavia, se il numero di Ngrams nell'input supera questa dimensione, possono verificarsi collisioni.

10. Inviare la pipeline. Il modulo LDA usa il teorema Bayes per determinare quali argomenti possono essere associati a singole parole. Le parole non sono associate esclusivamente ad alcun argomento o gruppo. Al contrario, ogni n-Gram ha una probabilità acquisita di essere associata a una qualsiasi delle classi individuate.

## <a name="results"></a>Risultati

Il modulo ha due output:

+ **DataSet trasformato**: questo output contiene il testo di input, un numero specificato di categorie individuate e i punteggi per ogni esempio di testo per ogni categoria.

+ **Matrice di argomenti della funzionalità**: la colonna più a sinistra contiene la funzionalità di testo estratto. Una colonna per ogni categoria contiene il punteggio per la funzionalità specificata in tale categoria.


### <a name="lda-transformation"></a>Trasformazione LDA

Questo modulo restituisce inoltre la *trasformazione Lda* che applica Lda al set di dati.

È possibile salvare questa trasformazione e riutilizzarla per altri set di impostazioni. Questa tecnica può essere utile se è stato eseguito il training su un corpus di grandi dimensioni e si vogliono riutilizzare i coefficienti o le categorie.

Per riutilizzare questa trasformazione, selezionare l'icona **registra set di dati** nel riquadro di destra del modulo di allocazione Dirichlet latente per lasciare il modulo sotto la categoria **set** di dati nell'elenco dei moduli. È quindi possibile connettere questo modulo al modulo [Apply Transformation](apply-transformation.md) per riutilizzare questa trasformazione.

### <a name="refining-an-lda-model-or-results"></a>Perfezionamento di un modello o di risultati LDA

In genere, non è possibile creare un singolo modello LDA che soddisfi tutte le esigenze. Anche un modello progettato per un'attività potrebbe richiedere molte iterazioni per migliorare la precisione. Si consiglia di provare tutti questi metodi per migliorare il modello:

+ Modifica dei parametri del modello
+ Uso della visualizzazione per comprendere i risultati
+ Ottenere i commenti degli esperti in materia per determinare se gli argomenti generati sono utili

Le misure qualitative possono essere utili anche per valutare i risultati. Per valutare i risultati della modellazione dell'argomento, prendere in considerazione:

+ Accuratezza. Gli elementi simili sono molto simili?
+ Diversità. Il modello può distinguere tra elementi simili quando necessario per il problema aziendale?
+ Scalabilità. Funziona su un'ampia gamma di categorie di testo o solo su un dominio di destinazione limitato?

Spesso è possibile migliorare l'accuratezza dei modelli basati su LDA usando l'elaborazione del linguaggio naturale per pulire, riepilogare e semplificare o categorizzare il testo. Ad esempio, le tecniche seguenti, tutte supportate in Azure Machine Learning, possono migliorare l'accuratezza della classificazione:

+ Eliminazione parole vuote

+ Normalizzazione maiuscole/minuscole

+ Lemmatizzazione o stemming

+ Riconoscimento di entità denominate

Per ulteriori informazioni, vedere [pre-elaborazione del testo](preprocess-text.md).

Nella finestra di progettazione è anche possibile usare le librerie R o Python per l'elaborazione del testo: [Execute r script](execute-r-script.md),  [Execute Python script](execute-python-script.md).



## <a name="technical-notes"></a>Note tecniche

Questa sezione contiene informazioni dettagliate sull'implementazione, suggerimenti e risposte alle domande più frequenti.

### <a name="implementation-details"></a>Dettagli dell'implementazione

Per impostazione predefinita, le distribuzioni di output per un set di dati trasformato e una matrice di argomenti della funzionalità vengono normalizzate come probabilità:

+ Il set di dati trasformato viene normalizzato come probabilità condizionale degli argomenti forniti da un documento. In questo caso, la somma di ogni riga è uguale a 1.

+ La matrice di argomenti della funzionalità viene normalizzata come probabilità condizionale di parole in base a un argomento. In questo caso, la somma di ogni colonna è uguale a 1.

> [!TIP]
> In alcuni casi il modulo potrebbe restituire un argomento vuoto. Nella maggior parte dei casi, la causa è l'inizializzazione pseudo-casuale dell'algoritmo. In tal caso, è possibile provare a modificare i parametri correlati. Modificare ad esempio le dimensioni massime del dizionario N-Gram o il numero di bit da usare per l'hashing delle funzionalità.

### <a name="lda-and-topic-modeling"></a>Modellazione di LDA e argomenti

L'allocazione di Dirichlet latente viene spesso usata per la *modellazione di argomenti basata sul contenuto*, che essenzialmente indica le categorie di apprendimento da testo non classificato. Nella modellazione di argomenti basati sul contenuto, un argomento è una distribuzione su parole.

Si supponga, ad esempio, di avere fornito un corpus di recensioni dei clienti che includono molti prodotti. Il testo delle revisioni inviate dai clienti nel corso del tempo contiene molte condizioni, alcune delle quali sono usate in più argomenti.

Un *argomento* identificato dal processo Lda potrebbe rappresentare le revisioni per un singolo prodotto o potrebbe rappresentare un gruppo di verifiche del prodotto. A LDA, l'argomento stesso è semplicemente una distribuzione di probabilità nel tempo per un set di parole.

I termini sono raramente esclusivi di un prodotto. Possono fare riferimento ad altri prodotti o essere termini generali applicabili a tutti gli elementi ("eccezionali", "terribili"). Altri termini potrebbero essere parole non significative. Tuttavia, il metodo LDA non tenta di acquisire tutte le parole nell'universo o di comprendere come sono correlate le parole, a parte le probabilità di co-occorrenza. Consente di raggruppare solo le parole utilizzate nel dominio di destinazione.

Una volta calcolati gli indici dei termini, una misura di somiglianza basata sulla distanza Confronta le singole righe di testo per determinare se due parti di testo sono simili. Ad esempio, si potrebbe notare che il prodotto ha più nomi strettamente correlati. In alternativa, si potrebbe notare che i termini fortemente negativi sono in genere associati a un prodotto specifico. È possibile utilizzare la misura di somiglianza per identificare i termini correlati e per creare indicazioni.

###  <a name="module-parameters"></a>Parametri del modulo

|Nome|Tipo|Range|Facoltativo|Predefinito|Descrizione|  
|----------|----------|-----------|--------------|-------------|-----------------|  
|Target column(s)|Selezione colonne||Necessario|StringFeature|Nome o indice della colonna di destinazione.|  
|Numero di argomenti da modellare|Integer|[1; 1000]|Necessario|5|Modellare la distribuzione dei documenti rispetto a N argomenti.|  
|N-grams|Integer|[1; 10]|Obbligatoria|2|Ordine di N-grammi generati durante l'hashing.|  
|Normalizzare|Boolean|true o false|Necessario|true|Normalizzare l'output in probabilità.  Il set di dati trasformato sarà P (argomento&#124;documento) e la matrice dell'argomento della funzionalità sarà P (argomento di Word&#124;).|  
|Mostra tutte le opzioni|Boolean|true o false|Necessario|False|Presenta parametri aggiuntivi specifici per Scikit-learn online LDA.|  
|Parametro Rho|Float|[0.00001; 1.0]|Si applica quando è selezionata la casella di controllo **Mostra tutte le opzioni**|0,01|Argomento distribuzione precedente di Word.|  
|Parametro Alpha|Float|[0.00001; 1.0]|Si applica quando è selezionata la casella di controllo **Mostra tutte le opzioni**|0,01|Argomento del documento distribuzione precedente.|  
|Numero stimato di documenti|Integer|[1;int.MaxValue]|Si applica quando è selezionata la casella di controllo **Mostra tutte le opzioni**|1000|Numero stimato di documenti. Corrisponde al `total_samples` parametro.|  
|Dimensioni del batch|Integer|[1; 1024]|Si applica quando è selezionata la casella di controllo **Mostra tutte le opzioni**|32|Dimensioni del batch.|  
|Valore iniziale di iterazione usato nella pianificazione degli aggiornamenti della velocità di apprendimento|Integer|[0; int. MaxValue|Si applica quando è selezionata la casella di controllo **Mostra tutte le opzioni**|0|Valore iniziale che downweights la velocità di apprendimento per le iterazioni iniziali. Corrisponde al `learning_offset` parametro.|  
|Potenza applicata all'iterazione durante gli aggiornamenti|Float|[0,0;1,0]|Si applica quando è selezionata la casella di controllo **Mostra tutte le opzioni**|0.5|Potenza applicata al numero di iterazioni per controllare la velocità di apprendimento. Corrisponde al `learning_decay` parametro. |  
|Number of training iterations|Integer|[1; 1024]|Si applica quando è selezionata la casella di controllo **Mostra tutte le opzioni**|25|Numero di iterazioni di training.|  
|Dizionario di compilazione di Ngrams|Boolean|true o false|Si applica quando la casella di controllo **Mostra tutte le opzioni** *non* è selezionata|True|Compila un dizionario di Ngrams prima di calcolare LDA. Utile per l'ispezione e l'interpretazione del modello.|  
|Dimensioni massime del dizionario Ngram|Integer|[1;int.MaxValue]|Si applica quando il **dizionario di compilazione delle opzioni di Ngrams** è **true**|20000|Dimensioni massime del dizionario Ngrams. Se il numero di token nell'input supera questa dimensione, potrebbero verificarsi collisioni.|  
|Numero di bit da usare per l'hashing delle funzionalità.|Integer|[1; 31]|Si applica quando la casella di controllo **Mostra tutte le opzioni** *non* è selezionata e il **dizionario di compilazione di Ngrams** è **false**|12|Numero di bit da usare per l'hashing delle funzionalità.| 
|Dizionario di compilazione di Ngrams prima di LDA|Boolean|true o false|Si applica quando è selezionata la casella di controllo **Mostra tutte le opzioni**|True|Compila un dizionario di Ngrams prima di LDA. Utile per l'ispezione e l'interpretazione del modello.|  
|Numero massimo di Ngrams nel dizionario|Integer|[1;int.MaxValue]|Si applica quando la casella di controllo **Mostra tutte le opzioni** è selezionata e l'opzione **dizionario di compilazione di Ngrams** è **true**|20000|Dimensioni massime del dizionario. Se il numero di token nell'input supera questa dimensione, potrebbero verificarsi collisioni.|  
|Numero di bit hash|Integer|[1; 31]|Si applica quando la casella di controllo **Mostra tutte le opzioni** è selezionata e l'opzione **dizionario di compilazione di Ngrams** è **false**|12|Numero di bit da utilizzare durante l'hashing delle funzionalità.|   


## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 

Per un elenco di errori specifici dei moduli, vedere [eccezioni e codici di errore per la finestra di progettazione](designer-error-codes.md).
