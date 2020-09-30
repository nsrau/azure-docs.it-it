---
title: 'Converti Word in vector: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare tre modelli Word2Vec forniti per estrarre un vocabolario e i corrispondenti incorporamenti di parole da un corpus di testo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/19/2020
ms.openlocfilehash: 5fad3e4862b0c40c9edd00a5b9d47b245e529396
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91536733"
---
# <a name="convert-word-to-vector-module"></a>Converti Word in modulo Vector

Questo articolo descrive come usare il modulo Convert Word to Vector in Azure Machine Learning Designer per eseguire queste attività:

- Applicare diversi modelli Word2Vec (Word2Vec, FastText, guanto con training) sul corpus di testo specificato come input.
- Genera un vocabolario con incorporamenti di Word.

Questo modulo usa la libreria Gensim. Per ulteriori informazioni su Gensim, vedere il [sito web ufficiale](https://radimrehurek.com/gensim/apiref.html), che include le esercitazioni e una spiegazione degli algoritmi.

### <a name="more-about-converting-words-to-vectors"></a>Altre informazioni sulla conversione di parole in vettori

La conversione di parole in vettori, o di vettori di parole, è un processo di elaborazione del linguaggio naturale (PNL). Il processo usa i modelli di linguaggio per eseguire il mapping di parole nello spazio vettoriale. Uno spazio vettoriale rappresenta ogni parola per un vettore di numeri reali. Consente inoltre a parole con significati simili avere rappresentazioni simili.

Usare gli incorporamenti di Word come input iniziale per le attività di PNL downstream, ad esempio la classificazione del testo e l'analisi dei sentimenti.

Tra le varie tecnologie di incorporamento di Word, in questo modulo sono stati implementati tre metodi ampiamente usati. Due, Word2Vec e FastText, sono modelli di training online. L'altro è un modello pretrainato, Glove-wiki-gigaword-100. 

I modelli di training online vengono sottoposti a training sui dati di input. I modelli con training preliminare vengono sottoposti a training offline in un corpus di testo di dimensioni maggiori, ad esempio Wikipedia, Google News, che in genere contiene circa 100 miliardi parole. L'incorporamento delle parole rimane costante durante la vettorizzazione di parole. I modelli di Word con training avanzato offrono vantaggi, ad esempio tempi di training ridotti, codificati per vettori di parole migliori e miglioramento delle prestazioni complessive.

Di seguito sono riportate alcune informazioni sui metodi:

+ Word2Vec è una delle tecniche più diffuse per apprendere gli incorporamenti di Word usando una rete neurale superficiale. La teoria è illustrata in questo documento, disponibile come download PDF: [stima efficiente delle rappresentazioni di Word nello spazio vettoriale](https://arxiv.org/pdf/1301.3781.pdf). L'implementazione in questo modulo si basa sulla [libreria Gensim per Word2Vec](https://radimrehurek.com/gensim/models/word2vec.html).

+ La teoria FastText è illustrata in questo documento, disponibile come download PDF: [arricchimento di vettori di Word con informazioni sulle sottoparole](https://arxiv.org/pdf/1607.04606.pdf). L'implementazione in questo modulo si basa sulla [libreria Gensim per FastText](https://radimrehurek.com/gensim/models/fasttext.html).

+ Il modello con training con il guanto è Glove-wiki-gigaword-100. Si tratta di una raccolta di vettori sottoposti a training in base a un corpus di testo di Wikipedia, che contiene i token 5,6 miliardi e 400.000 parole di vocabolario non incluse nel caso. È disponibile un download PDF: [GloVe: vettori globali per la rappresentazione di Word](https://nlp.stanford.edu/pubs/glove.pdf).

## <a name="how-to-configure-convert-word-to-vector"></a>Come configurare Convert Word to Vector

Questo modulo richiede un set di dati contenente una colonna di testo. Il testo pre-elaborato è migliore.

1. Aggiungere il modulo **Convert Word to Vector** alla pipeline.

2. Come input per il modulo, fornire un set di dati che contenga una o più colonne di testo.

3. Per **colonna di destinazione**scegliere solo una colonna contenente il testo da elaborare.

    Poiché questo modulo crea un vocabolario da testo, il contenuto delle colonne è diverso, il che comporta un contenuto del vocabolario diverso. Per questo motivo il modulo accetta solo una colonna di destinazione.

4. Per la  **strategia Word2Vec**, scegliere da **GloVe pretrained English Model**, **Gensim Word2Vec**e **Gensim FastText**.

5. Se la **strategia Word2Vec** è **Gensim Word2Vec** o **Gensim FastText**:

    + Per l' **algoritmo di training Word2Vec**, scegliere tra **Skip_gram** e **CBOW**. La differenza viene introdotta nel [documento originale (PDF)](https://arxiv.org/pdf/1301.3781.pdf).

        Il metodo predefinito è **Skip_gram**.

    + Per la **lunghezza dell'incorporamento di Word**, specificare la dimensionalità dei vettori di parole. Questa impostazione corrisponde al `size` parametro in Gensim.

        Le dimensioni predefinite di incorporamento sono pari a 100.

    + In **dimensioni finestra di contesto**specificare la distanza massima tra la parola da stimare e la parola corrente. Questa impostazione corrisponde al `window` parametro in Gensim.

        Le dimensioni predefinite della finestra sono pari a 5.

    + Per **Number of epochs**, specificare il numero di epoche (iterazioni) sul corpus. Corrisponde al `iter` parametro in Gensim.

        Il numero di Epoch predefinito è 5.

6. Per le **dimensioni massime del vocabolario**, specificare il numero massimo di parole nel vocabolario generato.

    Se sono presenti più parole univoche rispetto alla dimensione massima, eliminare quelle non frequenti.

    La dimensione predefinita del vocabolario è 10.000.

7. Per il **numero minimo di parole**, fornire un numero minimo di parole. Il modulo ignorerà tutte le parole con una frequenza inferiore a questo valore.

    Il valore predefinito è 5.

8. Inviare la pipeline.

## <a name="examples"></a>Esempi

Il modulo ha un output:

+ **Vocabolario con incorporamenti**: contiene il vocabolario generato, insieme all'incorporamento di ogni parola. Una dimensione occupa una colonna.

L'esempio seguente illustra come funziona il modulo Convert Word to Vector. USA Converti Word in Vector con le impostazioni predefinite nel set di dati di Wikipedia SP 500 pre-elaborato.

### <a name="source-dataset"></a>Set di dati di origine

Il set di dati contiene una colonna di categoria, insieme al testo completo recuperato da wikipedia. La tabella seguente illustra alcuni esempi rappresentativi.

|Testo|
|----------|
|nasdaq 100 component s p 500 component foundation founder location city apple campus 1 infinite loop street infinite loop cupertino california cupertino california location country united states...|
|br nasdaq 100 nasdaq 100 component br s p 500 s p 500 component industry computer software foundation br founder charles geschke br john warnock location adobe systems...|
|s p 500 s p 500 component industry automotive industry automotive predecessor general motors corporation 1908 2009 successor...|
|s p 500 s p 500 component industry conglomerate company conglomerate foundation founder location city fairfield connecticut fairfield connecticut location country usa area...|
|br s p 500 s p 500 component foundation 1903 founder william s harley br arthur davidson harley davidson founder arthur davidson br walter davidson br william a davidson location...|

### <a name="output-vocabulary-with-embeddings"></a>Vocabolario di output con incorporamenti

La tabella seguente contiene l'output di questo modulo, prendendo come input il set di dati di Wikipedia SP 500. La colonna più a sinistra indica il vocabolario. Il vettore di incorporamento è rappresentato dai valori delle colonne rimanenti nella stessa riga.

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

In questo esempio è stato usato il valore predefinito **Gensim Word2Vec** per la **strategia Word2Vec**e l' **algoritmo di training** è **Skip-Gram**. La **lunghezza dell'incorporamento di Word** è 100, quindi sono disponibili 100 colonne di incorporamento.

## <a name="technical-notes"></a>Note tecniche

Questa sezione contiene suggerimenti e risposte alle domande più frequenti.

+ Differenza tra il modello di training online e quello pretrainato:

    In questo modulo Converti Word in Vector sono state fornite tre diverse strategie: due modelli di training online e un modello con training. I modelli di training online usano il set di dati di input come dati di training e generano il vocabolario e i vettori di parole durante il training. Il modello con training preliminare è già stato sottoposto a training da un corpus di testo molto più ampio, ad esempio il testo di Wikipedia o Twitter. Il modello sottoposto a training è in realtà una raccolta di coppie di parole/incorporamento.  

    Il modello con training preliminare del guanto riepiloga un vocabolario dal set di dati di input e genera un vettore di incorporamento per ogni parola dal modello con training preliminare. Senza formazione online, l'uso di un modello con training automatico può ridurre i tempi di training. Offre prestazioni migliori, soprattutto se le dimensioni del set di dati di input sono relativamente ridotte.

+ Dimensioni di incorporamento:

    In generale, la lunghezza dell'incorporamento di Word è impostata su un centinaio. Ad esempio, 100, 200, 300. Una dimensione di incorporamento ridotta indica uno spazio vettoriale ridotto che può causare conflitti di incorporamento di parole.  

    La lunghezza degli incorporamenti di Word è fissa per i modelli con training. In questo esempio, la dimensione di incorporamento di Glove-wiki-gigaword-100 è 100.


## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 

Per un elenco degli errori specifici dei moduli della finestra di progettazione, vedere [Machine Learning codici di errore](designer-error-codes.md).
