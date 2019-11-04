---
title: Riferimento al modulo feature hashing
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo feature hashing nel Azure Machine Learning per trasforma i dati di testo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 09/01/2019
ms.openlocfilehash: 48960eae4941bb744a937639e1308e1b5f6aaf9f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497839"
---
# <a name="feature-hashing-module-reference"></a>Riferimento al modulo feature hashing

Questo articolo descrive un modulo incluso in Azure Machine Learning Designer (anteprima).

Usare il modulo feature hashing per trasformare un flusso di testo in inglese in un set di funzionalità Integer. È quindi possibile passare questo set di funzionalità con hash a un algoritmo di machine learning per eseguire il training di un modello di analisi del testo.

La funzionalità di hashing delle funzionalità fornita in questo modulo si basa su nimbusml Framework. Per ulteriori informazioni, vedere la [classe NgramHash](https://docs.microsoft.com/python/api/nimbusml/nimbusml.feature_extraction.text.extractor.ngramhash?view=nimbusml-py-latest).

## <a name="what-is-feature-hashing"></a>Che cos'è l'hashing delle funzionalità?

L'hashing delle funzionalità funziona convertendo i token univoci in numeri interi. Funziona sulle stringhe esatte fornite come input e non esegue alcuna analisi linguistica o pre-elaborazione. 

Si prenda, ad esempio, un set di frasi semplici come queste, seguite da un punteggio di valutazione. Si supponga di voler utilizzare questo testo per compilare un modello.

|Testo utente|Sentiment|
|--------------|---------------|
|Ho amato questo libro|3|
|Ho odiato questo libro|1|
|Questo libro è stato fantastico|3|
|Amo I libri|2|

Internamente, il modulo feature hashing crea un dizionario di n-grammi. Ad esempio, l'elenco di bigrammi per questo set di dati sarà simile al seguente:

|Termini (bigrammi)|Frequenza|
|------------|---------------|
|Questo libro|3|
|Ho amato|1|
|Ho odiato|1|
|Amo|1|

È possibile controllare le dimensioni degli n-grammi usando la proprietà **n-grammi** . Se si sceglie bigrammi, vengono calcolati anche unigrammi. Il dizionario includerà anche termini singoli come i seguenti:

|Termini (unigrammi)|Frequenza|
|------------|---------------|
|libro|3|
|I|3|
|Documentazione|1|
|è stato|1|

Dopo la compilazione del dizionario, il modulo feature hashing converte i termini del dizionario in valori hash. Viene quindi calcolato se una funzionalità è stata utilizzata in ogni caso. Per ogni riga di dati di testo, il modulo restituisce un set di colonne, una colonna per ogni funzionalità con hash.

Ad esempio, dopo l'hashing, le colonne delle funzionalità potrebbero avere un aspetto simile al seguente:

|Rating|Funzionalità di hashing 1|Funzionalità di hashing 2|Funzionalità di hashing 3|
|-----|-----|-----|-----|
|4|1|1|0|
|5|0|0|0|

* Se il valore nella colonna è 0, la riga non conteneva la funzionalità con hash.
* Se il valore è 1, la riga conteneva la funzionalità.

L'hashing delle funzionalità consente di rappresentare documenti di testo di lunghezza variabile come vettori di funzioni numeriche di uguale lunghezza per ridurre la dimensionalità. Se si è tentato di utilizzare la colonna di testo per il training così come è, verrebbe considerata come una colonna di funzionalità categorica con molti valori distinti.

Gli output numerici consentono anche di usare metodi di apprendimento automatico comuni, tra cui classificazione, clustering e recupero di informazioni. Poiché le operazioni di ricerca possono usare hash di tipo integer anziché confronti di stringhe, anche il recupero dei pesi delle funzioni è molto più rapido.

## <a name="configure-the-feature-hashing-module"></a>Configurare il modulo feature hashing

1.  Aggiungere il modulo feature hashing alla pipeline nella finestra di progettazione.

1. Connettere il set di dati che contiene il testo che si desidera analizzare.

    > [!TIP]
    > Poiché l'hashing delle funzionalità non esegue operazioni lessicali come lo stemming o il troncamento, è talvolta possibile ottenere risultati migliori tramite la pre-elaborazione del testo prima di applicare l'hashing delle funzionalità. 

1. Impostare le **colonne di destinazione** sulle colonne di testo che si desidera convertire in funzionalità con hash. Tenere presente quanto segue:

    * Il tipo di dati delle colonne deve essere String.
    
    * La scelta di più colonne di testo può avere un impatto significativo sulla dimensionalità delle funzionalità. Ad esempio, il numero di colonne per un hash a 10 bit va da 1.024 per una singola colonna a 2.048 per due colonne.

1. Usare **hash bitsize** per specificare il numero di bit da usare durante la creazione della tabella hash.
    
    La dimensione di bit predefinita è 10. Per molti problemi, questo valore è adeguato. Potrebbe essere necessario più spazio per evitare conflitti, a seconda delle dimensioni del vocabolario di n-grammi nel testo di training.
    
1. Per **n-grammi**, immettere un numero che definisce la lunghezza massima dei n-grammi da aggiungere al dizionario di training. Un n-Gram è una sequenza di *n* parole, trattate come un'unità univoca.

    Ad esempio, se si immette 3, unigrammi, bigrammi e trigrammi verranno creati.

1. Eseguire la pipeline.

## <a name="results"></a>Risultati

Al termine dell'elaborazione, il modulo restituisce un set di dati trasformato in cui la colonna di testo originale è stata convertita in più colonne. Ogni colonna rappresenta una funzionalità nel testo. A seconda dell'importanza del dizionario, il set di dati risultante può essere di grandi dimensioni:

|Nome colonna 1|Tipo di colonna 2|
|-------------------|-------------------|
|USERTEXT|Colonna di dati originale|
|SENTIMENTO|Colonna di dati originale|
|USERTEXT-hash della funzionalità 1|Colonna delle funzionalità con hash|
|USERTEXT-hash della funzionalità 2|Colonna delle funzionalità con hash|
|USERTEXT-funzionalità di hashing n|Colonna delle funzionalità con hash|
|USERTEXT-hash della funzionalità 1024|Colonna delle funzionalità con hash|

Dopo aver creato il set di dati trasformato, è possibile usarlo come input per il modulo Train Model.
 
### <a name="best-practices"></a>Procedure consigliate

Le seguenti procedure consigliate consentono di sfruttare al meglio il modulo feature hashing:

* Aggiungere un modulo di testo pre-elaborazione prima di usare feature hashing per pre-elaborare il testo di input. 

* Aggiungere un modulo Select Columns dopo il modulo feature hashing per rimuovere le colonne di testo dal set di dati di output. Non sono necessarie le colonne di testo dopo la generazione delle funzionalità di hashing.
    
* Provare a usare queste opzioni di pre-elaborazione del testo per semplificare i risultati e migliorare la precisione:

    * Separazione delle parole
    * Arresto della rimozione di Word
    * Normalizzazione maiuscole/minuscole
    * Rimozione di segni di punteggiatura e caratteri speciali
    * Stemming  

Il set ottimale di metodi di pre-elaborazione da applicare a qualsiasi soluzione dipende dal dominio, dal vocabolario e dalle esigenze aziendali. pipeline con i dati per vedere quali metodi di elaborazione del testo sono più efficaci.

## <a name="next-steps"></a>Passaggi successivi
            
Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning 
