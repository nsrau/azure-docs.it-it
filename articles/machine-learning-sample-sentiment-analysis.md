<properties title="Azure Machine Learning Sample: Sentiment analysis" pageTitle="Machine Learning Sample: Sentiment analysis | Azure" description="A sample Azure Machine Learning experiment that uses sentiment classification to predict product reviews." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye" />

# Esempio di Azure Machine Learning: Analisi dei sentimenti

*L'esperimento di esempio associato a questo modello in ML Studio è disponibile nella sezione **ESPERIMENTI** nella scheda **ESEMPI**. Il nome dell'esperimento è:*

	Sample Experiment - Sentiment Classification - Development

## Descrizione del problema
Prevedere una classificazione per la revisione del prodotto. Le classificazioni sono 1, 2, 3, 4, 5. Si tratta di un problema di regressione ordinale che può essere risolto come problema di regressione e come problema di classificazione a più classi.

## Dati
Revisioni di libri in Amazon, estratte dal sito Amazon dai ricercatori UPenn ([][]<http://www.cs.jhu.edu/~mdredze/datasets/sentiment/></a>). Il set di dati originale ha 975.000 revisioni con classificazioni 1, 2, 3, 4, 5. Per velocizzare l'esperimento, il set di dati è stato ridotto a 10.000 revisioni. Tutte le revisioni sono in inglese. Le revisioni sono state scritte tra il 1997 e il 2007.

## Modello
Per trasformare il testo inglese in funzionalità con valori Integer è stato usato un modulo di hashing della funzionalità. Sono stati confrontati tre modelli:

1. regressione lineare
2. regressione ordinale con regressione logistica a due classi come classificatore di base
3. classificazione a più classi con classificatore di regressione logistica a più classi

## Risultati

Algoritmo|                         Errore assoluto medio|           Errore radice quadrata media
---------|
Regressione ordinale  |              0,82|                                            1,41|
Regressione lineare  |              1,04|                                            1,36  |                                          
Classificazione a più classi  |    0,85  |                                          1,57

In base a questi risultati è stato scelto il modello di regressione ordinale ed è stato compilato un servizio Web basato sul modello.

<!-- Removed until this part is fixed ##API We have built a web service that takes a plain text review and predicts its rating. -->

  []: http://www.cs.jhu.edu/~mdredze/datasets/sentiment/
