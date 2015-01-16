<properties title="Azure Machine Learning Sample: Sentiment analysis" pageTitle="Esempio di Machine Learning: Analisi del sentiment | Azure" description="Un esperimento di Azure Machine Learning di esempio che usa una classificazione del sentimento per stimare le valutazioni di un prodotto." metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />
 

# Esempio di Machine Learning di Azure: Analisi del sentiment

>[AZURE.NOTE]
>L'[esperimento di esempio] e il [set di dati di esempio] associati a questo modello sono disponibili in ML Studio. Per informazioni dettagliate, vedere di seguito.
[Esperimento di esempio]: #sample-experiment
[Set di dati di esempio]: #sample-dataset


##Descrizione del problema
Prevedere una valutazione per la recensione di prodotti. Le valutazioni sono 1, 2, 3, 4, 5. Si tratta di un problema di regressione ordinale che può essere risolto come problema di regressione e come problema di classificazione a più classi.
 
##Dati
Recensioni di libri in Amazon, estratte dal sito Amazon dai ricercatori UPenn ([http://www.cs.jhu.edu/~mdredze/datasets/sentiment/](http://www.cs.jhu.edu/~mdredze/datasets/sentiment/)). Il set di dati originale ha 975.000 recensioni con classificazioni 1, 2, 3, 4, 5. Per velocizzare l'esperimento, il set di dati è stato ridotto a 10.000 revisioni. Tutte le recensioni sono in inglese. Le recensioni sono state scritte tra il 1997 e il 2007. 
 
##Modello
Per trasformare il testo inglese in funzionalità con valori Integer è stato usato un modulo di hashing della funzionalità. Sono stati confrontati tre modelli:  
 
1. regressione lineare   
2. regressione ordinale con regressione logistica a due classi come classificatore di base
3. classificazione a più classi con classificatore di regressione logistica a più classi
 
##Risultati

Algoritmo                 | Errore assoluto medio | Radice dell'errore quadratico medio
:---------                | :-----------------: | :--------------------:
Regressione ordinale        | 0,82                | 1,41
Regressione lineare         | 1,04                | 1,36
Classificazione multiclass | 0,85                | 1,57
 
In base a questi risultati è stato scelto il modello di regressione ordinale ed è stato compilato un servizio Web basato sul modello.
 
<!-- Removed until this part is fixed
##API
We have built a web service that takes a plain text review and predicts its rating.
-->


## Esperimento di esempio

L'esperimento di esempio seguente associato a questo modello è disponibile in ML Studio nella sezione **ESPERIMENTI** della scheda **ESEMPI**.

> **Esperimento di esempio - Classificazione del sentiment - Sviluppo**


## Set di dati di esempio

L'esempio di set di dati seguente usato per questo esperimento è disponibile in ML Studio nella tavolozza dei moduli in **Set di dati salvati**.

###Recensioni di libri da Amazon
[AZURE.INCLUDE [machine-learning-sample-dataset-book-reviews-from-amazon](../includes/machine-learning-sample-dataset-book-reviews-from-amazon.md)]
