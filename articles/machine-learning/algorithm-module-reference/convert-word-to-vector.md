---
title: Convert Word to Vector
titleSuffix: Azure Machine Learning
description: Informazioni su come usare tre modelli Word2Vec forniti per estrarre un vocabolario e i corrispondenti incorporamenti di parole da un corpus di testo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/19/2020
ms.openlocfilehash: e0e796b75690bcacc6be8ef29b8b490c7faa40af
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83853653"
---
# <a name="convert-word-to-vector"></a>Convert Word to Vector

Questo articolo descrive come usare il modulo **Convert Word to Vector** nella finestra di progettazione di Azure Machine Learning (anteprima) per applicare diversi modelli Word2Vec (Word2Vec, FastText, modello Glove con training preliminare) sul corpus di testo specificato come input e generare un vocabolario con incorporamenti di parole.

Questo modulo usa la libreria Gensim. Per ulteriori informazioni su Gensim, vedere il [sito Web ufficiale](https://radimrehurek.com/gensim/apiref.html) che include le esercitazioni e la spiegazione degli algoritmi.

### <a name="more-about-convert-word-to-vector"></a>Altre informazioni su Convert Word to Vector

In generale, la conversione di parole in vettori, o la vettorializzazione delle parole, è un processo di elaborazione del linguaggio naturale, che usa modelli o tecniche di linguaggio per eseguire il mapping di parole nello spazio vettoriale, ovvero rappresentare ogni parola in base a un vettore di numeri reali. Inoltre, consente alle parole con significati simili di avere rappresentazioni simili.

Gli incorporamenti di Word possono essere usati come input iniziale per le attività NLP a valle, ad esempio la classificazione del testo, l'analisi del sentiment e così via.

Tra le varie tecnologie di incorporamento delle parole, in questo modulo sono stati implementati tre metodi ampiamente usati, tra cui due modelli con training online, Word2Vec e FastText, e un modello con training preliminare, glove-wiki-gigaword-100. I modelli online vengono sottoposti a training sui dati di input, mentre il training preliminare avviene offline su un corpus di testo di dimensioni maggiori, come Wikipedia o Google News. In genere contiene circa 100 miliardi parole, per cui il loro incorporamento rimane costante durante la vettorizzazione. I modelli di parole con training preliminare offrono vantaggi come tempi di training ridotti, codifica di vettori di parole migliori e prestazioni complessive migliorate.

+ Word2Vec è una delle tecniche più diffuse per apprendere gli incorporamenti di parole con la rete neurale superficiale. La teoria viene discussa nel seguente paper (in inglese), disponibile per il download in formato PDF: [Efficient Estimation of Word Representations in Vector Space, Mikolov, Tomas, et al](https://arxiv.org/pdf/1301.3781.pdf). L'implementazione in questo modulo si basa sulla [libreria Gensim per Word2Vec](https://radimrehurek.com/gensim/models/word2vec.html).

+ La teoria FastText è illustrata nel paper seguente (in inglese), disponibile per il download in formato PDF: [Enriching Word Vectors with Subword Information, Bojanowski, Piotr, et al](https://arxiv.org/pdf/1607.04606.pdf). L'implementazione in questo modulo si basa sulla [libreria Gensim per FastText](https://radimrehurek.com/gensim/models/fasttext.html).

+ La raccolta glove-wiki-gigaword-100 per modelli Glove con training preliminare è basata sul corpus di testo di Wikipedia, con 5,6 miliardi di token e 400.000 termini in minuscolo e disponibile per il download in formato PDF (in inglese): [GloVe: Global Vectors for Word Representation](https://nlp.stanford.edu/pubs/glove.pdf).

## <a name="how-to-configure-convert-word-to-vector"></a>Come configurare Convert Word to Vector

Questo modulo richiede un set di dati contenente una colonna di testo (meglio se pre-elaborato).

1. Aggiungere il modulo **Convert Word to Vector** alla pipeline.

2. Come input per il modulo, fornire un set di dati contenente una o più colonne di testo.

3. Per **Target column** (Colonna di destinazione), scegliere solo una colonna contenente il testo da elaborare.

    In generale, poiché questo modulo crea un vocabolario a partire dal testo, il contenuto varia a seconda delle colonne. Questo comporta un contenuto del vocabolario diverso, pertanto il modulo accetta solo una colonna di destinazione.

4. Per **Word2Vec strategy** (Strategia Word2Vec), scegliere tra `GloVe pretrained English Model`, `Gensim Word2Vec` e `Gensim FastText`.

5. Se **Word2Vec strategy** è `Gensim Word2Vec` oppure `Gensim FastText`:

    + **Word2Vec Training Algorithm** (Algoritmo di training Word2Vec). Scegliere tra `Skip_gram` e `CBOW`. La differenza viene illustrata nel [paper](https://arxiv.org/pdf/1301.3781.pdf) originale.

        Il metodo predefinito è `Skip_gram`.

    + **Length of word embedding** (Lunghezza dell'incorporamento di parole). Specificare la dimensionalità dei vettori di parole. Corrisponde al parametro `size` in Gensim.

        Il valore embedding_size predefinito è 100.

    + **Context window size** (Dimensioni finestra di contesto). Specificare la distanza massima tra la parola stimata e la parola corrente. Corrisponde al parametro `window` in Gensim.

        La dimensione predefinita della finestra è 5.

    + **Numero di periodi**. Specificare il numero di periodi (iterazioni) nel corpus. Corrisponde al parametro `iter` in Gensim.

        Il numero di periodi predefinito è 5.

6. Per **Maximum vocabulary size** (Dimensioni massime del vocabolario), specificare il numero massimo di parole nel vocabolario generato.

    Se sono presenti più parole univoche, eliminare quelle meno frequenti.

    Il valore predefinito per le dimensioni del vocabolario è 10000.

7. Per **Minimum word count** (Numero minimo di parole), specificare un numero minimo di parole per far sì che il modulo ignori tutte le parole che hanno una frequenza inferiore a tale valore.

    Il valore predefinito è 5.

8. Inviare la pipeline.

## <a name="examples"></a>Esempi

Il modulo ha un output:

+ **Vocabulary with embeddings** (Vocabolario con incorporamenti): contiene il vocabolario generato, insieme all'incorporamento di ogni parola. Una dimensione occupa una colonna.

### <a name="result-examples"></a>Esempi di risultati

Per illustrare il funzionamento del modulo **Convert Word to Vector**, l'esempio seguente applica il modulo con le impostazioni predefinite al set di dati Wikipedia SP 500 pre-elaborato fornito in Azure Machine Learning (anteprima).

#### <a name="source-dataset"></a>Set di dati di origine

Il set di dati contiene una colonna per la categoria e il testo completo recuperato da Wikipedia. Questa tabella contiene solo alcuni esempi rappresentativi.

|text|
|----------|
|nasdaq 100 component s p 500 component foundation founder location city apple campus 1 infinite loop street infinite loop cupertino california cupertino california location country united states...|
|br nasdaq 100 nasdaq 100 component br s p 500 s p 500 component industry computer software foundation br founder charles geschke br john warnock location adobe systems...|
|s p 500 s p 500 component industry automotive industry automotive predecessor general motors corporation 1908 2009 successor...|
|s p 500 s p 500 component industry conglomerate company conglomerate foundation founder location city fairfield connecticut fairfield connecticut location country usa area...|
|br s p 500 s p 500 component foundation 1903 founder william s harley br arthur davidson harley davidson founder arthur davidson br walter davidson br william a davidson location...|

#### <a name="output-vocabulary-with-embeddings"></a>Vocabolario di output con incorporamenti

La tabella seguente contiene l'output di questo modulo accettando il set di dati Wikipedia SP 500 come input. La colonna più a sinistra indica il vocabolario, mentre il vettore di incorporamento è rappresentato dai valori delle colonne rimanenti nella stessa riga.

|Vocabolario|Dim. incorporamento 0|Dim. incorporamento 1|Dim. incorporamento 2|Dim. incorporamento 3|Dim. incorporamento 4|Dim. incorporamento 5|...|Dim. incorporamento 99|
|-------------|-------------|-------------|-------------|-------------|-------------|-------------|-------------|-------------|
|nasdaq|-0.375865|0.609234|0.812797|-0.002236|0.319071|-0.591986|...|0.364276
|component|0.081302|0.40001|0.121803|0.108181|0.043651|-0.091452|...|0.636587
|s|-0.34355|-0.037092|-0.012167|0.151542|0.601019|0.084501|...|0.149419
|p|-0.133407|0.073244|0.170396|0.326706|0.213463|-0.700355|...|0.530901
foundation|-0.166819|0.10883|-0.07933|-0.073753|0.262137|0.045725|...|0.27487
founder|-0.297408|0.493067|0.316709|-0.031651|0.455416|-0.284208|...|0.22798
posizione|-0.375213|0.461229|0.310698|0.213465|0.200092|0.314288|...|0.14228
city|-0.460828|0.505516|-0.074294|-0.00639|0.116545|0.494368|...|-0.2403
apple|0.05779|0.672657|0.597267|-0.898889|0.099901|0.11833|...|0.4636
campus|-0.281835|0.29312|0.106966|-0.031385|0.100777|-0.061452|...|0.05978
infinite|-0.263074|0.245753|0.07058|-0.164666|0.162857|-0.027345|...|-0.0525
loop|-0.391421|0.52366|0.141503|-0.105423|0.084503|-0.018424|...|-0.0521

In questo esempio è stato usato il parametro `Gensim Word2Vec` predefinito come **strategia di Word2Vec**, l'**algoritmo di training** è `Skip-gram` e la **lunghezza dell'incorporamento di parole** è 100, quindi sono disponibili 100 colonne di incorporamento.

## <a name="technical-notes"></a>Note tecniche

Questa sezione contiene suggerimenti e risposte alle domande più frequenti.

+ Differenza tra il modello con training online e quello con training preliminare

    In questo **modello Convert Word to Vector** sono state fornite tre diverse strategie: due modelli con training online e un modello con training preliminare. Il modello con training online usa il set di dati di input come dati di training e genera il vocabolario e i vettori di parole durante il training. Il modello con training preliminare, invece, è già stato sottoposto a training con un corpus di testi molto più ampio, tratto ad esempio da Wikipedia o Twitter, per cui il modello con training preliminare è in realtà una raccolta di coppie parola-incorporamento.  

    Se il modello Glove con training preliminare viene scelto come strategia di vettorizzazione delle parole, questo riepiloga un vocabolario dal set di dati di input e genera il vettore di incorporamento per ogni parola a partire dal modello con training preliminare, senza formazione online. L'uso di un modello con training preliminare può ridurre il tempo di training e ottenere prestazioni migliori, soprattutto quando le dimensioni del set di dati di input sono relativamente piccole.

+ Dimensioni dell'incorporamento

    In generale, la lunghezza dell'incorporamento di parole è impostata sull'ordine delle centinaia (ad esempio 100, 200 o 300) per ottenere prestazioni ottimali. Questo perché gli incorporamenti di piccole dimensioni comportano poco spazio per i vettori, il che può causare collisioni tra incorporamenti di parole.  

    Per i modelli con training preliminare, la lunghezza degli incorporamenti di parole è fissa. In questa implementazione, la dimensione di incorporamento di glove-wiki-gigaword-100 è 100.


## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 

Per un elenco degli errori specifici dei moduli di della finestra di progettazione (anteprima), vedere [Codici di errore di Machine Learning](designer-error-codes.md).
