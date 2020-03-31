---
title: Informazioni di riferimento sui moduli di hashing delle funzionalitàFeature Hashing module reference
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Feature Hashing in Azure Machine Learning per featurizzare i dati di testo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 7178417a5c20afe5b1ed02bc526ec174704962df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456251"
---
# <a name="feature-hashing-module-reference"></a>Informazioni di riferimento sui moduli di hashing delle funzionalitàFeature Hashing module reference

Questo articolo descrive un modulo incluso nella finestra di progettazione di Azure Machine Learning (anteprima).

Utilizzare il modulo Feature Hashing per trasformare un flusso di testo inglese in un set di funzionalità integer. È quindi possibile passare questo set di funzionalità con hash a un algoritmo di apprendimento automatico per eseguire il training di un modello di analisi del testo.

La funzionalità di hashing delle funzionalità fornita in questo modulo si basa sul framework nimbusml. Per ulteriori informazioni, vedere [Classe NgramHash](https://docs.microsoft.com/python/api/nimbusml/nimbusml.feature_extraction.text.extractor.ngramhash?view=nimbusml-py-latest).

## <a name="what-is-feature-hashing"></a>Che cos'è l'hashing delle funzionalità?

L'hashing delle funzionalità funziona convertendo token univoci in numeri interi. Funziona sulle stringhe esatte fornite come input e non esegue alcuna analisi linguistica o pre-elaborazione. 

Ad esempio, prendi una serie di frasi semplici come queste, seguite da un punteggio di valutazione. Si supponga di voler utilizzare questo testo per compilare un modello.

|Testo dell'utente|Valutazione|
|--------------|---------------|
|Mi è piaciuto molto questo libro|3|
|Odiavo questo libro|1|
|Questo libro è stato fantastico|3|
|Adoro i libri|2|

Internamente, il modulo Feature Hashing crea un dizionario di n-grammi. Ad esempio, l'elenco di bigrammi per questo set di dati sarebbe simile al seguente:For example, the list of bigrams for this dataset would something like this:

|Termine (bigrammi)|Frequenza|
|------------|---------------|
|Questo libro|3|
|Mi è piaciuto molto|1|
|Odiavo|1|
|Amo|1|

È possibile controllare la dimensione degli n grammi utilizzando la proprietà **N-grams.** Se si scelgono i bigrammi, vengono calcolati anche gli unigrammi. Il dizionario includerebbe anche termini singoli come questi:

|Termine (unigrammi)|Frequenza|
|------------|---------------|
|libro|3|
|I|3|
|books|1|
|Era|1|

Dopo la compilazione del dizionario, il modulo Hash della funzionalità converte i termini del dizionario in valori hash. Calcola quindi se una funzionalità è stata utilizzata in ogni caso. Per ogni riga di dati di testo, il modulo restituisce un set di colonne, una colonna per ogni funzionalità con hash.

Ad esempio, dopo l'hashing, le colonne di entità geografiche potrebbero essere simili alle seguente:For example, after hashing, the feature columns might look something like this:

|Rating|Funzione hash 1|Funzione di hashing 2|Funzionalità di hashing 3|
|-----|-----|-----|-----|
|4|1|1|0|
|5|0|0|0|

* Se il valore nella colonna è 0, la riga non contiene la funzionalità di hash.
* Se il valore è 1, la riga contiene la funzionalità.

L'hashing delle funzionalità consente di rappresentare documenti di testo di lunghezza variabile come vettori di entità geografiche numeriche di uguale lunghezza per ridurre la dimensionalità. Se si tenta di utilizzare la colonna di testo per il training così com'è, verrà considerata come una colonna di entità geografiche con molti valori distinti.

Gli output numerici consentono inoltre di utilizzare metodi di apprendimento automatico comuni, tra cui classificazione, clustering e recupero delle informazioni. Poiché le operazioni di ricerca possono utilizzare gli intervalli di numeri interi anziché i confronti tra stringhe, anche il recupero dei pesi delle funzionalità è molto più veloce.

## <a name="configure-the-feature-hashing-module"></a>Configurare il modulo Feature Hashing

1.  Aggiungere il modulo Feature Hashing alla pipeline nella finestra di progettazione.

1. Connettere il set di dati che contiene il testo da analizzare.

    > [!TIP]
    > Poiché l'hashing delle funzionalità non esegue operazioni lessicali come lo stemming o il troncamento, è talvolta possibile ottenere risultati migliori pre-elaborazione del testo prima di applicare l'hash delle funzionalità. 

1. Impostare **Colonne di destinazione** sulle colonne di testo che si desidera convertire in funzionalità con hash. Tenete a mente che:

    * Le colonne devono essere il tipo di dati string.
    
    * La scelta di più colonne di testo può avere un impatto significativo sulla dimensionalità delle feature. Ad esempio, il numero di colonne per un hash a 10 bit va da 1.024 per una singola colonna a 2.048 per due colonne.

1. Usare **hashing bitsize** per specificare il numero di bit da utilizzare durante la creazione della tabella hash.
    
    La dimensione in bit predefinita è 10.The default bit size is 10. Per molti problemi, questo valore è adeguato. Potrebbe essere necessario più spazio per evitare collisioni, a seconda delle dimensioni del vocabolario n-grams nel testo di allenamento.
    
1. Per **N-grammi**, immettere un numero che definisce la lunghezza massima degli n grammi da aggiungere al dizionario di training. Un n-grammo è una sequenza di *n* parole, trattate come un'unità unica.

    Ad esempio, se si immettono 3, verranno creati unigrammi, bigrammi e trigrammi.

1. Inviare la pipeline.

## <a name="results"></a>Risultati

Al termine dell'elaborazione, il modulo restituisce un set di dati trasformato in cui la colonna di testo originale è stata convertita in più colonne. Ogni colonna rappresenta una feature nel testo. A seconda di quanto sia significativo il dizionario, il set di dati risultante può essere di grandi dimensioni:Depending on how significant the dictionary is, the resulting dataset can be large:

|Nome colonna 1|Tipo di colonna 2|
|-------------------|-------------------|
|TESTO UTENTE|Colonna di dati originale|
|Sentimento|Colonna di dati originale|
|USERTEXT - Funzione hash 1|Colonna della funzionalità con hash|
|USERTEXT - Funzione hash 2|Colonna della funzionalità con hash|
|USERTEXT - Funzione hash n|Colonna della funzionalità con hash|
|USERTEXT - Funzionalità hash 1024|Colonna della funzionalità con hash|

Dopo aver creato il set di dati trasformato, è possibile utilizzarlo come input per il modulo Train Model.
 
## <a name="best-practices"></a>Procedure consigliate

Le seguenti procedure consigliate consentono di ottenere il massimo dal modulo Feature Hashing:

* Aggiungere un modulo di testo di pre-elaborazione prima di utilizzare l'hashing delle funzionalità per pre-elaborare il testo di input. 

* Aggiungere un modulo Seleziona colonne dopo il modulo Feature Hashing per rimuovere le colonne di testo dal set di dati di output. Non sono necessarie le colonne di testo dopo la generazione delle funzionalità di hashing.
    
* Prendi in considerazione l'uso di queste opzioni di pre-elaborazione del testo, per semplificare i risultati e migliorare l'accuratezza:

    * Interruzione di parola
    * Arresto della rimozione delle parole
    * Normalizzazione dei casi
    * Rimozione della punteggiatura e dei caratteri speciali
    * Stemming  

Il set ottimale di metodi di pre-elaborazione da applicare in qualsiasi soluzione dipende dal dominio, dal vocabolario e dalle esigenze aziendali. pipeline con i dati per vedere quali metodi di elaborazione del testo sono più efficaci.

## <a name="next-steps"></a>Passaggi successivi
            
Vedere il set di moduli disponibili per Azure Machine LearningSee the [set of modules available](module-reference.md) to Azure Machine Learning 
