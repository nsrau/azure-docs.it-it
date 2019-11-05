---
title: 'Esempio di progettazione #7: classificare le revisioni'
titleSuffix: Azure Machine Learning
description: Informazioni su come creare un modello di machine learning per classificare le revisioni del libro in categorie diverse.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 11/04/2019
ms.openlocfilehash: 6f9a2327953177f29ba7a1f5948d24c7df428c09
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515447"
---
# <a name="sample-7---text-classification-predict-company-category"></a>Esempio 7-classificazione del testo: stimare la categoria aziendale 

Questo esempio illustra come usare i moduli di analisi del testo per compilare una pipeline di classificazione del testo in Azure Machine Learning Designer (anteprima).

L'obiettivo della classificazione del testo è quello di assegnare parte del testo a una o più classi o categorie predefinite. Il testo può essere un documento, un articolo di notizie, una query di ricerca, un messaggio di posta elettronica, un tweet, ticket di supporto, commenti dei clienti, revisione del prodotto utente e così via. Le applicazioni di classificazione del testo includono la categorizzazione di articoli di giornali e contenuti Wire di notizie in argomenti, l'organizzazione di pagine Web in categorie gerarchiche, il filtraggio della posta indesiderata, l'analisi dei sentimenti, la stima degli obiettivi utente dalle query di ricerca ticket di supporto e analisi dei commenti dei clienti. 

Questa pipeline addestra un **classificatore di regressione logistica multiclasse** per stimare la categoria aziendale con il set di dati di wikipedia SP 500 derivato da wikipedia.  

I passaggi fondamentali di un modello di apprendimento automatico del training con dati di testo sono:

1. Ottenere i dati

1. Pre-elaborare i dati di testo

1. Progettazione delle caratteristiche

   Convertire la funzionalità di testo nella funzionalità numerica con il modulo di estrazione delle funzionalità, ad esempio feature hashing, estrarre la funzionalità n-Gram dai dati di testo.

1. Eseguire il training del modello

1. Set di dati Score

1. Valutare il modello

Ecco il grafico finale e completato della pipeline su cui lavoreremo. Verranno fornite le logiche per tutti i moduli, in modo da poter prendere decisioni simili.

[Grafico ![della pipeline](./media/how-to-ui-sample-text-classification/nlp-modules-overall.png)](./media/how-to-ui-sample-text-classification/nlp-modules-overall.png#lightbox)

## <a name="data"></a>Dati

In questa pipeline viene usato il set di dati di **Wikipedia SP 500** . Il set di dati è derivato da Wikipedia (https://www.wikipedia.org/) in base agli articoli di ogni società & P 500. Prima di caricare in Azure Machine Learning Designer, il set di dati è stato elaborato come segue:

- Estrazione del contenuto di testo per ogni specifica società
- Rimozione della formattazione wiki
- Rimozione dei caratteri non alfanumerici
- Conversione di tutto il testo in minuscolo
- Aggiunta delle categorie di società note

Non è stato possibile trovare articoli per alcune società, quindi il numero di record è inferiore a 500.

## <a name="pre-process-the-text-data"></a>Pre-elaborare i dati di testo

Il modulo di **testo pre-elaborazione** viene usato per pre-elaborare i dati di testo, tra cui rilevare le frasi, tokenize frasi e così via. Sono state trovate tutte le opzioni supportate nell'articolo [**pre-elaborazione del testo**](../algorithm-module-reference/preprocess-text.md) . Dopo aver pre-elaborato i dati Tex, viene usato il modulo **Split data** per dividere in modo casuale i dati di input in modo che il set di dati di training contenga il 50% dei dati originali e il set di dati di testing contenga il 50% dei dati originali.

## <a name="feature-engineering"></a>Progettazione delle caratteristiche
In questo esempio, si utilizzeranno due metodi che eseguono la progettazione delle funzionalità.

### <a name="feature-hashing"></a>Feature Hashing
È stato usato il modulo [**feature hashing**](../algorithm-module-reference/feature-hashing.md) per convertire il testo normale degli articoli in numeri interi e sono stati usati i valori integer come funzionalità di input per il modello. 

Il modulo **feature hashing** può essere usato per convertire documenti di testo a lunghezza variabile in vettori di funzioni numeriche di lunghezza uguale, usando il metodo di hashing murmurhash v3 a 32 bit fornito dalla libreria Wabbit Vowpal. L'obiettivo dell'utilizzo dell'hashing delle funzioni è la riduzione della dimensionalità. Inoltre, l'hashing delle funzionalità rende più veloce la ricerca dei pesi delle funzionalità al momento della classificazione, perché usa il confronto dei valori hash anziché il confronto tra stringhe.

Nella pipeline di esempio, si imposta il numero di bit di hashing su 14 e si imposta il numero di n-grammi su 2. Con queste impostazioni, la tabella hash può conservare 2 ^ 14 voci, in cui ogni funzionalità di hashing rappresenta una o più funzionalità n-Gram e il relativo valore rappresenta la frequenza di occorrenza di tale n-Gram nell'istanza di testo. Per molti problemi, una tabella hash di queste dimensioni è più che adeguata, ma in alcuni casi potrebbe essere necessario più spazio per evitare conflitti. Valutare le prestazioni della soluzione di apprendimento automatico usando un numero diverso di bit. 

### <a name="extract-n-gram-feature-from-text"></a>Estrai la funzionalità N-Gram dal testo

Un n-Gram è una sequenza contigua di n termini da una sequenza di testo specificata. Un n-grammo di dimensioni 1 viene definito unigramma; un n-grammo di dimensioni 2 è un bigramma; un n-grammo di dimensioni 3 è un trigramma. Ai n-grammi di dimensioni maggiori viene talvolta fatto riferimento dal valore di n, ad esempio "quattro grammi", "cinque grammi" e così via.

È stata usata l' [**estrazione della funzionalità N-Gram dal**](../algorithm-module-reference/extract-n-gram-features-from-text.md)modulo di testo come altra soluzione per la progettazione delle funzionalità. Questo modulo estrae prima di tutto il set di n-grammi, oltre ai n-grammi, il numero di documenti in cui ogni n-gramma viene visualizzato nel testo viene conteggiato (DF). In questo esempio viene usata la metrica TF-IDF per calcolare i valori delle funzionalità. Quindi converte i dati di testo non strutturati in vettori di funzioni numeriche di lunghezza uguale, in cui ogni funzionalità rappresenta il TF-IDF di un n-Gram in un'istanza di testo.

Dopo la conversione dei dati di testo in vettori di funzioni numeriche, viene usato un modulo **Select Column** per rimuovere i dati di testo dal set di dati. 

## <a name="train-the-model"></a>Eseguire il training del modello

La scelta dell'algoritmo dipende spesso dai requisiti del caso d'uso. Poiché l'obiettivo di questa pipeline è stimare la categoria della società, un modello di classificazione multiclasse rappresenta una scelta ottimale. Poiché il numero di funzionalità è elevato e queste funzionalità sono di tipo sparse, viene usato il modello di **regressione logistica multiclasse** per questa pipeline.

## <a name="test-evaluate-and-compare"></a>Test, valutazione e confronto

 Il set di dati viene suddiviso e vengono utilizzati set di dati diversi per eseguire il training e il test del modello, in modo da rendere più obiettivo la valutazione del modello.

Dopo aver eseguito il training del modello, si utilizzeranno i moduli **Score Model** e **Evaluate Model** per generare risultati stimati e valutare i modelli. Tuttavia, prima di usare il modulo **Score Model** , è necessario eseguire la progettazione di funzioni come quello che è stato fatto durante il training. 

Per il modulo **feature hashing** , è facile eseguire la funzionalità di assegnazione dei punteggi come flusso di training. Usare direttamente il modulo **feature hashing** per elaborare i dati di testo di input.

Per la **funzionalità Estrai N-Gram dal** modulo di testo, si connette l' **output del vocabolario dei risultati** dal flusso di dati di training al **vocabolario di input** nel flusso di dati di assegnazione dei punteggi e si imposta il parametro della **modalità vocabolario** su **ReadOnly** .
[Grafico ![del punteggio n-Gram](./media/how-to-ui-sample-text-classification/n-gram.png)](./media/how-to-ui-sample-text-classification/n-gram.png)

Al termine della fase di progettazione, è possibile utilizzare **Score Model** per generare stime per il set di dati di test utilizzando il modello sottoposto a training. Per controllare il risultato, selezionare la porta di output di **Score Model** e quindi selezionare **Visualize (Visualizza**).

Vengono quindi passati i punteggi al modulo **Evaluate Model** per generare le metriche di valutazione. Il **modello Evaluate** include due porte di input, in modo che sia possibile valutare e confrontare i set di dati con punteggio generati con metodi diversi. In questo esempio vengono confrontate le prestazioni del risultato generato con il metodo di hashing delle funzionalità e il metodo n-Gram.
Per controllare il risultato, selezionare la porta di output del **modello Evaluate** e quindi selezionare **Visualize (Visualizza**).

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

Esplorare gli altri esempi disponibili per la finestra di progettazione:
- [Esempio 1: regressione: stimare il prezzo di un'automobile](how-to-designer-sample-regression-automobile-price-basic.md)
- [Esempio 2: regressione: confrontare gli algoritmi per la stima del prezzo dell'automobile](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Esempio 3: classificazione con selezione delle caratteristiche: stima del reddito](how-to-designer-sample-classification-predict-income.md)
- [Esempio 4-Classificazione: stimare il rischio di credito (costo sensibile)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Esempio 5-classificazione: varianza di stima](how-to-designer-sample-classification-churn.md)
- [Esempio 6-Classificazione: stima dei ritardi dei voli](how-to-designer-sample-classification-flight-delay.md)
