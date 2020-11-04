---
title: 'ML Studio (classico): valutazione & Cross-Validate Models-Azure'
description: Informazioni sulle metriche che è possibile usare per monitorare le prestazioni del modello in Azure Machine Learning Studio (classico).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18, previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: ca369f8a3e680a4d2aae49df83dda0cdd3dc4075
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93310146"
---
# <a name="evaluate-model-performance-in-azure-machine-learning-studio-classic"></a>Valutare le prestazioni del modello in Azure Machine Learning Studio (classico)

**SI APPLICA A:**  ![Si applica a.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (versione classica) ![Non si applica a. ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


Questo articolo illustra le metriche che è possibile usare per monitorare le prestazioni del modello in Azure Machine Learning Studio (classico).  La valutazione delle prestazioni di un modello è una delle fasi principali nel processo di analisi scientifica dei dati. Indica quanto è stato positivo il punteggio (stime) di un set di dati da un modello sottoposto a training. Azure Machine Learning Studio (classico) supporta la valutazione del modello tramite due dei moduli principali di Machine Learning: 
+ [Evaluate Model][evaluate-model] (Valuta modello) 
+ [Cross-Validate Model][cross-validate-model]

Questi moduli consentono all'utente di osservare le prestazioni del proprio modello in termini di una serie di metriche comunemente usate in Machine Learning e nella statistica.

La valutazione dei modelli deve essere considerata insieme a:
+ [Ottimizzazione dei parametri per gli algoritmi](algorithm-parameters-optimize.md)
+ [Interpretabilità dei modelli](interpret-model-results.md)

L'argomento presenta inoltre tre scenari di apprendimento sorvegliato comuni: 
* Regressione
* Classificazione binaria 
* Classificazione multiclasse


## <a name="evaluation-vs-cross-validation"></a>Confronto tra la valutazione e la convalida incrociata
La valutazione e la convalida incrociata sono due modi standard di misurare le prestazioni del proprio modello. Entrambi generano metriche di valutazione che l'utente può usare per controllare o mettere a confronto quelle di altri modelli.

Il [modello di valutazione][evaluate-model] prevede un set di dati con punteggio come input (oppure due nel caso in cui si desideri confrontare le prestazioni di due modelli diversi). Pertanto, è necessario eseguire il training del modello usando il modulo [Train Model][train-model] ed eseguire stime su alcuni set di dati usando il modulo [Score Model][score-model] prima di poter valutare i risultati. La valutazione è basata sulle probabilità/etichette con punteggio e sulle etichette vere, tutti elementi restituiti dal modulo [Score Model][score-model].

In alternativa, è possibile usare la convalida incrociata per eseguire una serie di operazioni di valutazione, punteggio e training (10 sezioni) in modo automatico su diversi subset di dati di input. I dati di input vengono suddivisi in dieci partizioni, di cui una riservata per la convalida e le rimanenti usate per eseguire il training. Tale processo si ripete per 10 volte e viene calcolata una media delle metriche di valutazione. Ciò consente di determinare come verrebbero generalizzati nuovi set di dati da un modello. Il modulo [Cross-Validate Model][cross-validate-model] riceve in input un modello non sottoposto a training e alcuni set di dati con etichetta e restituisce i risultati della valutazione di ognuna delle 10 sezioni, oltre alla media dei risultati.

Nelle sezioni seguenti verranno creati semplici modelli di regressione e classificazione e ne verranno valutate le prestazioni usando i moduli [Evaluate Model][evaluate-model] e [Cross-Validate Model][cross-validate-model].

## <a name="evaluating-a-regression-model"></a>Valutazione di un modello di regressione
Si supponga di voler stimare il prezzo di un'automobile usando funzionalità come dimensioni, potenza, specifiche del motore e così via. Si tratta di un tipico problema di regressione, in cui la variabile di destinazione ( *price* ) è un valore numerico continuo. È possibile adattare un modello di regressione lineare che, dati i valori delle funzionalità di una determinata automobile, può prevedere il prezzo di tale automobile. Questo modello di regressione può essere usato per calcolare il punteggio dello stesso set di dati su cui si sta effettuando il training Una volta ottenuto il prezzo stimato per le automobili, è possibile valutare le prestazioni del modello osservando la differenza tra le stime e i prezzi effettivi. Per illustrare questo problema, viene usato il *set di dati automobile price data (RAW)* disponibile nella sezione set di dati **salvati** in Machine Learning Studio (classico).

### <a name="creating-the-experiment"></a>Creazione di un esperimento
Aggiungere i moduli seguenti all'area di lavoro in Azure Machine Learning Studio (versione classica):

* Automobile price data (Raw)
* [Linear Regression][linear-regression] (Regressione lineare)
* [Eseguire il training del modello][train-model]
* [Score Model][score-model] (Punteggio modello)
* [Evaluate Model][evaluate-model] (Valuta modello)

Connettere le porte come indicato nella figura 1 seguente e impostare la colonna delle etichette nel modulo [Train Model][train-model] su *price*.

![Valutazione di un modello di regressione](./media/evaluate-model-performance/1.png)

Figura 1. Valutazione di un modello di regressione.

### <a name="inspecting-the-evaluation-results"></a>Controllo dei risultati di valutazione
Dopo aver eseguito l'esperimento, è possibile fare clic sulla porta di output del modulo [Evaluate Model][evaluate-model] e selezionare *Visualize* (Visualizza) per visualizzare i risultati della valutazione. Le metriche di valutazione disponibili per i modelli di regressione sono: *Mean Absolute Error* (Errore assoluto medio), *Root Mean Absolute Error* (Errore assoluto medio radice), *Relative Absolute Error* (Errore assoluto relativo), *Relative Squared Error* (Errore quadratico relativo) e *Coefficient of Determination* (Coefficiente di determinazione).

In questo caso, il termine "errore" rappresenta la differenza tra il valore stimato e il valore reale. Il valore assoluto o il quadrato di questa differenza viene calcolato in genere per ottenere il margine totale dell'errore in tutte le istanze, poiché la differenza tra il valore stimato e quello reale potrebbe essere negativa in alcuni casi. Le metriche di errore misurano le prestazioni predittive di un modello di regressione in termini di deviazione media delle stime rispetto ai valori reali. Più i valori di errore sono bassi, più il modello effettua stime precise. Una metrica di errore complessivo pari a 0 indica che il modello corrisponde perfettamente ai dati.

Il coefficiente di determinazione, altrimenti noto come "valore quadratico R", rappresenta, inoltre, un modo standard di misurazione della percentuale di idoneità del modello rispetto ai dati. Può essere definito come la percentuale di variazione esplicitata dal modello. Una percentuale più elevata è migliore nel caso in cui 1 indica un'idoneità perfetta.

![Metriche di valutazione della regressione lineare](./media/evaluate-model-performance/2.png)

Figura 2. Metriche di valutazione della regressione lineare.

### <a name="using-cross-validation"></a>Uso della convalida incrociata
Come indicato in precedenza, è possibile eseguire il training, il punteggio e le valutazioni ripetute automaticamente usando il modulo [Cross-Validate Model][cross-validate-model] . In questo caso occorrono semplicemente un set di dati, un modello non sottoposto a training e un modulo [Cross-Validate Model][cross-validate-model] (vedere la figura seguente). È necessario impostare la colonna Label su *Price* nelle proprietà del modulo [Cross-Validate Model][cross-validate-model] .

![Convalida incrociata di un modello di regressione](./media/evaluate-model-performance/3.png)

Figura 3. Esecuzione della convalida incrociata di un modello di regressione.

Dopo aver eseguito l'esperimento, è possibile fare clic sulla porta di output destra del modulo [Cross-Validate Model][cross-validate-model] per controllare i risultati della valutazione. In questo modo viene fornita una visualizzazione dettagliata delle metriche di ciascuna iterazione (sezione) e i risultati medi di ciascuna delle metriche (figura 4).

![Risultati della convalida incrociata di un modello di regressione](./media/evaluate-model-performance/4.png)

Figura 4. Risultati della convalida incrociata di un modello di regressione.

## <a name="evaluating-a-binary-classification-model"></a>Valutazione di un modello di classificazione binaria
In uno scenario di classificazione binaria la variabile di destinazione ha solo due risultati possibili, ad esempio: {0, 1} o {false, true}, {negative, positive}. Si supponga di disporre di un set di dati di dipendenti adulti con alcune variabili demografiche e di impiego e che venga richiesto di stimare il livello di reddito, una variabile binaria con i valori {"<= 50 K", ">50 K"}. In altri termini, la classe negativa rappresenta il caso in cui il dipendente realizza un valore inferiore o uguale a 50.000 l'anno, mentre la classe positiva rappresenta tutti gli altri dipendenti. Come nello scenario della regressione, verrà eseguito il training di un modello, verrà calcolato il punteggio di alcuni dati e verranno valutati i risultati. La differenza principale è la scelta delle metriche Azure Machine Learning Studio (classiche) di calcolo e output. Per illustrare lo scenario di stima del livello di reddito, il set di dati per [adulti](https://archive.ics.uci.edu/ml/datasets/Adult) viene usato per creare un esperimento di studio (classico) e valutare le prestazioni di un modello di regressione logistica a due classi, un classificatore binario di uso comune.

### <a name="creating-the-experiment"></a>Creazione di un esperimento
Aggiungere i moduli seguenti all'area di lavoro in Azure Machine Learning Studio (versione classica):

* Adult Census Income Binary Classification dataset
* [Regressione logistica a due classi][two-class-logistic-regression]
* [Eseguire il training del modello][train-model]
* [Score Model][score-model] (Punteggio modello)
* [Evaluate Model][evaluate-model] (Valuta modello)

Connettere le porte come indicato nella figura 5 seguente e impostare la colonna delle etichette del modulo [Train Model][train-model] su *income*.

![Valutazione di un modello di classificazione binaria](./media/evaluate-model-performance/5.png)

Figura 5. Valutazione di un modello di classificazione binaria.

### <a name="inspecting-the-evaluation-results"></a>Controllo dei risultati di valutazione
Dopo aver eseguito l'esperimento, è possibile fare clic sulla porta di output del modulo [Evaluate Model][evaluate-model] e selezionare *Visualize* (Visualizza) per visualizzare i risultati della valutazione (Figura 7). Le metriche di valutazione disponibili per i modelli di classificazione binaria sono: *Accuracy* (Accuratezza), *Precision* (Precisione), *Recall* (Richiamo), *F1 Score* (Punteggio F1) e *AUC*. Il modulo restituisce anche una matrice di confusione che mostra il numero di veri positivi, falsi negativi, falsi positivi e veri negativi, nonché le curve *ROC* , *Precision/Recall* (Precisione/Richiamo) e *Lift* (Accuratezza).

L'accuratezza è semplicemente la percentuale delle istanze classificate correttamente. In genere è la prima metrica che viene osservata quando si valuta un classificatore. Tuttavia, quando i dati di test non sono bilanciati (dove la maggior parte delle istanze appartiene a una delle classi) o si è più interessati alle prestazioni in una delle classi, l'accuratezza non acquisisce effettivamente l'efficacia di un classificatore. Nello scenario di classificazione del livello di reddito, si supponga di eseguire il test di alcuni dati in cui il 99% delle istanze rappresenta le persone che guadagnano una cifra inferiore o uguale a 50.000 l'anno. È possibile ottenere un'accuratezza di 0,99 stimando la classe "<= 50.000" per tutte le istanze. In questo caso sembra che il classificatore svolga un buon lavoro in linea generale, ma in realtà non è in grado di classificare correttamente gli individui con un reddito superiore (il restante 1%).

Per questo motivo è utile calcolare metriche aggiuntive che raccolgano aspetti più specifici della valutazione. Prima di entrare nei dettagli di tali metriche, è importante comprendere la matrice di confusione della valutazione di una classificazione binaria. Le etichette di classe nel set di training possono assumere solo due valori possibili, che in genere fanno riferimento a positivo o negativo. Le istanze positive e negative stimate correttamente da un classificatore si definiscono rispettivamente valori veri positivi (VP) e veri negativi (VN). Analogamente, le istanze classificate in modo errato si definiscono valori falsi positivi (FP) e falsi negativi (FN). La matrice di confusione è semplicemente una tabella che mostra il numero di istanze che rientrano in ognuna delle quattro categorie. Azure Machine Learning Studio (classico) stabilisce automaticamente quale delle due classi nel set di dati è la classe positiva. Se le etichette di classe sono booleane o Integer, alle istanze con etichetta ' true ' o ' 1' viene assegnata la classe positiva. Se le etichette sono stringhe, ad esempio con il set di dati del reddito, le etichette vengono ordinate alfabeticamente e il primo livello viene scelto come classe negativa mentre il secondo livello è la classe positiva.

![Matrice di confusione di classificazione binaria](./media/evaluate-model-performance/6a.png)

Figura 6. Matrice di confusione di classificazione binaria.

Tornando al problema della classificazione del reddito, di seguito vengono fornite alcune domande sulla valutazione, utili a comprendere le prestazioni del classificatore usato. Una domanda naturale è:' out of the individuals that the Model ha stimato di ottenere >50 K (TP + FP), quanti sono stati classificati correttamente (TP)?' È possibile rispondere a questa domanda osservando la **precisione** del modello, che è la percentuale di positivi classificati correttamente: VP/(VP+FP). Un'altra domanda comune è "da tutti i dipendenti con reddito elevato con reddito >50.000 (TP + FN), il numero di classificazioni corrette (TP) per il classificatore. Questo è il **richiamo** o tasso di veri positivi, VP/(VP+FN), del classificatore. Come si può notare, vi è un chiaro compromesso tra precisione e richiamo. Ad esempio, in presenza di un set di dati relativamente bilanciato, un classificatore che stima istanze soprattutto positive avrà un richiamo elevato ma una precisione più bassa, poiché molte delle istanze negative verranno classificate in modo errato dando come risultato una serie di falsi positivi. Per vedere un tracciato delle variazioni di queste due metriche, è possibile fare clic sulla curva **PRECISIONE/RICHIAMO** nella pagina di output dei risultati di valutazione (la parte superiore sinistra della figura 7).

![Risultati della valutazione della classificazione binaria](./media/evaluate-model-performance/7.png)

 Figura 7. Risultati della valutazione della classificazione binaria.

Un'altra metrica correlata spesso usata è **Punteggio F1** , che prende in considerazione precisione e richiamo. Si tratta della media armonica di queste due metriche e viene calcolata come tale: F1 = 2 (precisione x richiamo)/(precisione + richiamo). Il Punteggio F1 è un modo efficace per riepilogare la valutazione in un singolo numero, ma è sempre consigliabile esaminare sia la precisione che la richiamata per comprendere meglio il comportamento di un classificatore.

È anche possibile confrontare il tasso di veri positivi e il tasso di falsi positivi nella curva **ROC (Receiver Operating Characteristic)** e il valore **AUC (Area Under the Curve)** corrispondente. La curva più vicina è l'angolo superiore sinistro, migliori sono le prestazioni del classificatore, ovvero massimizzando il vero tasso positivo, riducendo al minimo il tasso di falsi positivi. Le curve che si trovano vicino alla diagonale del tracciato risultano dai classificatori che tendono a fare delle stime al limite della casualità.

### <a name="using-cross-validation"></a>Uso della convalida incrociata
Come nell'esempio di regressione, è possibile eseguire la convalida incrociata per eseguire ripetutamente il training, il punteggio e la valutazione di subset diversi di dati automaticamente. Analogamente, è possibile usare il modulo [Cross-Validate Model][cross-validate-model], un modello di regressione logistica non sottoposto a training, e un set di dati. La colonna Label deve essere impostata su *Income* nelle proprietà del modulo [Cross-Validate Model][cross-validate-model] . Dopo aver eseguito l'esperimento e selezionato la porta di output destra del modulo [Cross-Validate Model][cross-validate-model], è possibile visualizzare i valori metrici per ogni sezione, oltre alla deviazione media e standard di ognuna di esse. 

![Convalida incrociata di un modello di classificazione binaria](./media/evaluate-model-performance/8.png)

Figura 8. Convalida incrociata di un modello di classificazione binaria.

![Risultati della convalida incrociata di un classificatore binario.](./media/evaluate-model-performance/9.png)

Figura 9. Risultati della convalida incrociata di un classificatore binario.

## <a name="evaluating-a-multiclass-classification-model"></a>Valutazione di un modello di classificazione multiclasse
In questo esperimento verrà usato il popolare set di dati [Iris](https://archive.ics.uci.edu/ml/datasets/Iris "Iris") , che contiene le istanze di tre tipi diversi (classi) del Plant Iris. Sono disponibili quattro valori di funzionalità (lunghezza/larghezza SEPA e lunghezza/larghezza petalo) per ogni istanza. Negli esperimenti precedenti è stato eseguito il training e il testing dei modelli usando gli stessi set di impostazioni. Qui viene usato il modulo [Split data][split] per creare due subset dei dati, eseguire il training sul primo e assegnare un punteggio e valutare il secondo. Il set di dati Iris è disponibile pubblicamente nel [repository di Machine Learning UCI](https://archive.ics.uci.edu/ml/index.html) e può essere scaricato usando un modulo [Import Data][import-data].

### <a name="creating-the-experiment"></a>Creazione di un esperimento
Aggiungere i moduli seguenti all'area di lavoro in Azure Machine Learning Studio (versione classica):

* [Importazione dei dati][import-data]
* [Foresta delle decisioni multiclasse][multiclass-decision-forest]
* [Dividere dati][split]
* [Eseguire il training del modello][train-model]
* [Score Model][score-model] (Punteggio modello)
* [Evaluate Model][evaluate-model] (Valuta modello)

Connettere le porte come mostrato in basso nella figura 10.

Impostare l'indice della colonna delle etichette del modulo [Train Model][train-model] su 5. Il set di dati non dispone di una riga di intestazione ma, com'è noto, le etichette delle classi si trovano nella quinta colonna.

Fare clic sul modulo [Import Data][import-data] (Importazione dati) e impostare la proprietà *Data source* (Origine dati) su *Web URL via HTTP* (URL Web tramite HTTP) e *URL* su http://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data.

Impostare la frazione delle istanze da usare per il training nel modulo [Split Data][split], ad esempio 0,7.

![Valutazione di un classificatore multiclasse](./media/evaluate-model-performance/10.png)

Figura 10. Valutazione di un classificatore multiclasse

### <a name="inspecting-the-evaluation-results"></a>Controllo dei risultati di valutazione
Eseguire l'esperimento e fare clic sulla porta di output di [Evaluate Model][evaluate-model]. In questo caso, i risultati di valutazione sono presentati nel formato di una matrice di confusione. La matrice Mostra le istanze effettive di vs. stimate per tutte e tre le classi.

![Risultati della valutazione della classificazione multiclasse](./media/evaluate-model-performance/11.png)

Figura 11. Risultati di valutazione della classificazione a più classi.

### <a name="using-cross-validation"></a>Uso della convalida incrociata
Come indicato in precedenza, è possibile eseguire il training, il punteggio e le valutazioni ripetute automaticamente usando il modulo [Cross-Validate Model][cross-validate-model] . Saranno necessari un set di dati, un modello non sottoposto a training e un modulo [Cross-Validate Model][cross-validate-model] (vedere la figura seguente). È nuovamente necessario impostare la colonna delle etichette del modulo [Cross-Validate Model][cross-validate-model] (in questo caso, indice della colonna 5). Dopo aver eseguito l'esperimento e selezionato la porta destra di output del modulo [Cross-Validate Model][cross-validate-model], è possibile controllare i valori metrici per ogni sezione, nonché la deviazione media e standard. Le metriche visualizzate sono simili a quelle illustrate nel caso della classificazione binaria. Tuttavia, nella classificazione multiclasse, il calcolo dei veri positivi/negativi e dei falsi positivi/negativi viene eseguito tenendo conto delle singole classi, in quanto non esiste alcuna classe globale positiva o negativa. Ad esempio, quando si calcola la precisione o il richiamo della classe ' Iris-setosa ', si presuppone che questa sia la classe positiva e tutte le altre come negative.

![Convalida incrociata di un modello di classificazione multiclasse](./media/evaluate-model-performance/12.png)

Figura 12. Convalida incrociata di un modello di classificazione a più classi.

![Risultati della convalida incrociata di un modello di classificazione multiclasse](./media/evaluate-model-performance/13.png)

Figura 13. Risultati della convalida incrociata di un modello di classificazione multiclasse.

<!-- Module References -->
[cross-validate-model]: /azure/machine-learning/studio-module-reference/cross-validate-model
[evaluate-model]: /azure/machine-learning/studio-module-reference/evaluate-model
[linear-regression]: /azure/machine-learning/studio-module-reference/linear-regression
[multiclass-decision-forest]: /azure/machine-learning/studio-module-reference/multiclass-decision-forest
[import-data]: /azure/machine-learning/studio-module-reference/import-data
[score-model]: /azure/machine-learning/studio-module-reference/score-model
[split]: /azure/machine-learning/studio-module-reference/split-data
[train-model]: /azure/machine-learning/studio-module-reference/train-model
[two-class-logistic-regression]: /azure/machine-learning/studio-module-reference/two-class-logistic-regression