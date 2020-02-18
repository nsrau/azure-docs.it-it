---
title: 'Finestra di progettazione: esempio di classificazione di recensioni di libri'
titleSuffix: Azure Machine Learning
description: Creare un classificatore di regressione logistica per prevedere la categoria aziendale con il set di dati SP 500 di Wikipedia usando la finestra di progettazione di Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 02/11/2020
ms.openlocfilehash: f15f50e372d0bfe58018b16ebfa5d5d85644ae1a
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137776"
---
# <a name="build-a-classifier-to-predict-company-category-using-azure-machine-learning-designer"></a>Creare un classificatore per prevedere la categoria aziendale usando la finestra di progettazione di Azure Machine Learning.

**Finestra di progettazione (anteprima) - Esempio 7**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Questo esempio illustra come usare i moduli di analisi del testo per creare una pipeline di classificazione del testo nella finestra di progettazione di Azure Machine Learning (anteprima).

L'obiettivo della classificazione del testo è di assegnare un testo a una o più classi o categorie predefinite. Il testo può essere un documento, un articolo di notizie, una query di ricerca, un messaggio di posta elettronica, un tweet, un ticket di supporto, il feedback dei clienti, una recensione di prodotti e così via. Le applicazioni di classificazione del testo includono la categorizzazione in argomenti di articoli di giornali e contenuti di newswire, l'organizzazione di pagine Web in categorie gerarchiche, l'applicazione di filtri alla posta indesiderata, l'analisi del sentiment, la previsione di finalità utente da query di ricerca, l'instradamento dei ticket di supporto e l'analisi del feedback dei clienti. 

Questa pipeline esegue il training di un **classificatore di regressione logistica multiclasse** per prevedere la categoria aziendale con il **set di dati SP 500 di Wikipedia derivato da Wikipedia**.  

I passaggi fondamentali di un modello di Machine Learning di training con dati di testo sono:

1. Ottenere i dati

1. Pre-elaborare i dati di testo

1. Progettazione delle caratteristiche

   Convertire la caratteristica di testo nella caratteristica numerica con il modulo di estrazione di caratteristiche, ad esempio hashing, ed estrarre la caratteristica n-gramma dai dati di testo.

1. Eseguire il training del modello

1. Assegnare un punteggio al set di dati

1. Valutare il modello

Ecco il grafo finale completato della pipeline creata. Questo articolo fornisce i fondamenti logici per tutti i moduli, in modo da poter prendere decisioni simili in autonomia.

[![Grafo della pipeline](./media/how-to-designer-sample-text-classification/nlp-modules-overall.png)](./media/how-to-designer-sample-text-classification/nlp-modules-overall.png#lightbox)

## <a name="data"></a>Data

In questa pipeline viene usato il set di dati **SP 500 di Wikipedia**. Il set di dati deriva da Wikipedia (https://www.wikipedia.org/) ) in base agli articoli di ogni azienda S&P 500. Prima di caricarlo nella finestra di progettazione di Azure Machine Learning, il set di dati è stato elaborato come segue:

- Estrazione del contenuto di testo per ogni specifica società
- Rimozione della formattazione wiki
- Rimozione dei caratteri non alfanumerici
- Conversione di tutto il testo in minuscolo
- Aggiunta delle categorie di società note

Per alcune aziende non sono stati trovati articoli, quindi il numero di record è inferiore a 500.

## <a name="pre-process-the-text-data"></a>Pre-elaborare i dati di testo

Per pre-elaborare i dati di testo viene usato il modulo **Preprocess Text** (Preelabora il testo), che include il rilevamento e la divisione in token delle frasi. Tutte le opzioni supportate sono disponibili nell'articolo [**Preprocess Text**](algorithm-module-reference/preprocess-text.md). Dopo la pre-elaborazione dei dati di testo, si usa il modulo **Split data** (Dividi dati) per dividere in modo casuale i dati di input in modo che il set di dati di training contenga il 50% dei dati originali e il set di dati di test il 50% dei dati originali.

## <a name="feature-engineering"></a>Progettazione delle caratteristiche
In questo esempio si useranno due metodi per eseguire la progettazione delle caratteristiche.

### <a name="feature-hashing"></a>Feature Hashing
È stato usato il modulo [**Feature Hashing**](algorithm-module-reference/feature-hashing.md) (Hashing delle caratteristiche) per convertire il testo normale degli articoli in valori interi, che sono stati usati come caratteristiche di input per il modello. 

Il modulo **Feature Hashing** (Hashing delle caratteristiche) può essere usato per convertire documenti di testo di lunghezza variabile in vettori di caratteristiche numeriche di lunghezza uguale, usando il metodo di hashing murmurhash v3 a 32 bit fornito dalla libreria Vowpal Wabbit. L'obiettivo dell'hashing di caratteristiche è la riduzione delle caratteristiche. Inoltre, questo metodo velocizza la ricerca di pesi delle caratteristiche in fase di classificazione perché usa il confronto di valori hash invece che di stringhe.

Nella pipeline di esempio si imposta il numero di bit di hashing su 14 e il numero di n-grammi su 2. Con queste impostazioni, la tabella hash può contenere 2^14 voci, in cui ogni caratteristica di hashing rappresenta una o più caratteristiche n-gramma e il relativo valore rappresenta la frequenza di occorrenza di tale n-gramma nell'istanza di testo. Per molti problemi, una tabella hash di queste dimensioni è più che adeguata, ma in alcuni casi potrebbe essere necessario più spazio per evitare conflitti. Valutare le prestazioni della soluzione di Machine Learning usando un numero diverso di bit. 

### <a name="extract-n-gram-feature-from-text"></a>Estrarre le caratteristiche n-gramma dal testo

Un n-gramma è una sequenza contigua di n termini prelevati da una sequenza di testo specificata. Un n-gramma di dimensioni 1 viene definito unigramma; un n-gramma di dimensioni 2 è un bigramma; un n-gramma di dimensioni 3 è un trigramma. Gli n-grammi di dimensioni più grandi vengono identificati dal valore n, ad esempio 4-gramma, 5-gramma e così via.

È stato usato il modulo [**Extract N-Gram Feature from Text**](algorithm-module-reference/extract-n-gram-features-from-text.md) (Estrai la caratteristica n-gramma dal testo) come altra soluzione per la progettazione delle caratteristiche. Questo modulo estrae prima di tutto il set di n-grammi. In aggiunta agli n-grammi, viene conteggiato il numero di documenti in cui ognuno compare nel testo (DF). In questo esempio viene usata la metrica TF-IDF per calcolare i valori delle caratteristiche. Quindi i dati di testo non strutturati vengono convertiti in vettori di caratteristiche numeriche di lunghezza uguale, in cui ogni caratteristica rappresenta l'algoritmo TF-IDF di un n-gramma in un'istanza di testo.

Dopo la conversione dei dati di testo in vettori di caratteristiche numeriche, viene usato il modulo **Select Column** (Seleziona colonna) per rimuovere i dati di testo dal set di dati. 

## <a name="train-the-model"></a>Eseguire il training del modello

La scelta dell'algoritmo dipende spesso dai requisiti del caso d'uso. Poiché l'obiettivo di questa pipeline è prevedere la categoria dell'azienda, un modello di classificazione multiclasse rappresenta una scelta valida. Considerando che il numero di caratteristiche è elevato e che tali caratteristiche sono sparse, per questa pipeline viene usato il modello di **regressione logistica multiclasse**.

## <a name="test-evaluate-and-compare"></a>Testare, valutare e confrontare

 Il set di dati viene diviso e vengono usati set di dati diversi per eseguire il training e il test del modello in modo da renderne più oggettiva la valutazione.

Una volta eseguito il training del modello, verranno usati i moduli **Score Model** (Assegna un punteggio al modello) e **Evaluate Model** (Valuta modello) per generare i risultati previsti e valutare i modelli. Tuttavia, prima di usare il modulo **Score Model** (Assegna un punteggio al modello), è necessario eseguire la progettazione delle caratteristiche come è stato fatto durante il training. 

Per il modulo **Feature Hashing** (Hashing delle caratteristiche), è facile eseguire la progettazione delle caratteristiche con il flusso di assegnazione di punteggi come flusso di training. Per elaborare i dati di testo di input, usare direttamente il modulo **Feature Hashing** (Hashing delle caratteristiche).

Per il modulo **Extract N-Gram Feature from Text** (Estrai la caratteristica n-gramma dal testo), è necessario connettere l'output di **Result Vocabulary** (Vocabolario dei risultati) del flusso di dati di training alla porta di **Input Vocabulary** (Vocabolario di input) nel flusso di dati di assegnazione dei punteggi e impostare il parametro **Vocabulary mode** (Modalità vocabolario) su **ReadOnly**.
[![Grafo del punteggio di n-grammi](./media/how-to-designer-sample-text-classification/n-gram.png)](./media/how-to-designer-sample-text-classification/n-gram.png)

Al termine della fase di progettazione, è possibile usare il modulo **Score Model** (Assegna un punteggio al modello) per generare previsioni per il set di dati usando il modello sottoposto a training. Per controllare il risultato, selezionare la porta di output di **Score Model** (Assegna un punteggio al modello) e quindi selezionare **Visualize** (Visualizza).

Passare quindi i punteggi al modulo **Evaluate Model** (Valuta modello) per generare le metriche di valutazione. **Evaluate Model** (Valuta modello) ha due porte di input, quindi è possibile valutare e confrontare i set di dati con punteggio generati con metodi diversi. In questo esempio vengono confrontate le prestazioni del risultato generato con il metodo di hashing delle caratteristiche e il metodo degli n-grammi.
Per controllare il risultato, selezionare la porta di output di **Evaluate Model** (Valuta modello) e quindi selezionare **Visualize** (Visualizza).

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

Esplorare gli altri esempi disponibili per la finestra di progettazione:
- [Esempio 1 - Regressione: Prevedere il prezzo di un'automobile](how-to-designer-sample-regression-automobile-price-basic.md)
- [Esempio 2 - Regressione: Confrontare gli algoritmi per la previsione del prezzo delle automobili](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Esempio 3 - Classificazione con selezione delle caratteristiche: Previsione del reddito](how-to-designer-sample-classification-predict-income.md)
- [Esempio 4 - Classificazione: Prevedere il rischio di credito (sensibile al costo)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Esempio 5 - Classificazione: Prevedere la varianza](how-to-designer-sample-classification-churn.md)
- [Esempio 6 - Classificazione: Prevedere i ritardi dei voli](how-to-designer-sample-classification-flight-delay.md)
