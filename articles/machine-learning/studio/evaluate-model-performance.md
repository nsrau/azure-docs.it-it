---
title: Valutare le prestazioni dei modelli
titleSuffix: ML Studio (classic) - Azure
description: Informazioni su come valutare le prestazioni del modello in Azure Machine Learning Studio (classico) e sulle metriche disponibili per questa attività.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18, previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: b37844ff93ed1cfb631c2d8da12d0729f61f44ed
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837646"
---
# <a name="how-to-evaluate-model-performance-in-azure-machine-learning-studio-classic"></a>Come valutare le prestazioni del modello in Azure Machine Learning Studio (classico)

In questo articolo viene illustrato come valutare le prestazioni di un modello in Azure Machine Learning Studio (classico) e viene fornita una breve spiegazione delle metriche disponibili per questa attività. L'argomento presenta inoltre tre scenari di apprendimento sorvegliato comuni: 

* regressione
* Classificazione binaria 
* Classificazione multiclasse



La valutazione delle prestazioni di un modello è una delle fasi principali nel processo di analisi scientifica dei dati. Indica quanto è stato positivo il punteggio (stime) di un set di dati da un modello sottoposto a training. 

Azure Machine Learning Studio (classico) supporta la valutazione del modello tramite due moduli di apprendimento automatico principali: [Evaluate Model][evaluate-model] e [Cross-Validate Model][cross-validate-model]. Questi moduli consentono all'utente di osservare le prestazioni del proprio modello in termini di una serie di metriche comunemente usate in Machine Learning e nella statistica.

## <a name="evaluation-vs-cross-validation"></a>Confronto tra la valutazione e la convalida incrociata
La valutazione e la convalida incrociata sono due modi standard di misurare le prestazioni del proprio modello. Entrambi generano metriche di valutazione che l'utente può usare per controllare o mettere a confronto quelle di altri modelli.

Il [modello di valutazione][evaluate-model] prevede un set di dati con punteggio come input (oppure due nel caso in cui si desideri confrontare le prestazioni di due modelli diversi). Pertanto, è necessario eseguire il training del modello usando il modulo [Train Model][train-model] ed eseguire stime su alcuni set di dati usando il modulo [Score Model][score-model] prima di poter valutare i risultati. La valutazione è basata sulle probabilità/etichette con punteggio e sulle etichette vere, tutte restituite dal modulo [Score Model][score-model] .

In alternativa, è possibile usare la convalida incrociata per eseguire una serie di operazioni di valutazione, punteggio e training (10 sezioni) in modo automatico su diversi subset di dati di input. I dati di input vengono suddivisi in dieci partizioni, di cui una riservata per la convalida e le rimanenti usate per eseguire il training. Tale processo si ripete per 10 volte e viene calcolata una media delle metriche di valutazione. Ciò consente di determinare come verrebbero generalizzati nuovi set di dati da un modello. Il modulo [Cross-Validate Model][cross-validate-model] accetta un modello senza training e un set di dati con etichetta e restituisce i risultati della valutazione di ognuna delle 10 riduzioni, oltre ai risultati medi.

Nelle sezioni seguenti verranno compilati modelli di regressione e classificazione semplici e ne verranno valutate le prestazioni, usando i moduli [Evaluate Model][evaluate-model] e [Cross-Validate Model][cross-validate-model] .

## <a name="evaluating-a-regression-model"></a>Valutazione di un modello di regressione
Si supponga di voler stimare il prezzo di un'automobile usando funzionalità come dimensioni, potenza, specifiche del motore e così via. Si tratta di un tipico problema di regressione, in cui la variabile di destinazione (*price*) è un valore numerico continuo. È possibile adattare un modello di regressione lineare che, dati i valori delle funzionalità di una determinata automobile, può prevedere il prezzo di tale automobile. Questo modello di regressione può essere usato per calcolare il punteggio dello stesso set di dati su cui si sta effettuando il training Una volta ottenuto il prezzo stimato per le automobili, è possibile valutare le prestazioni del modello osservando la differenza tra le stime e i prezzi effettivi. Per illustrare questo problema, viene usato il *set di dati automobile price data (RAW)* disponibile nella sezione set di dati **salvati** in ml Studio (classico).

### <a name="creating-the-experiment"></a>Creazione di un esperimento
Aggiungere i moduli seguenti all'area di lavoro nella versione classica di Azure Machine Learning Studio:

* Automobile price data (Raw)
* [Regressione lineare][linear-regression]
* [Train Model][train-model]
* [Modello di Punteggio][score-model]
* [Valuta modello][evaluate-model]

Connettere le porte come illustrato di seguito nella figura 1 e impostare la colonna Label del modulo [Train Model][train-model] su *Price*.

![Valutazione di un modello di regressione](./media/evaluate-model-performance/1.png)

Figura 1. Valutazione di un modello di regressione.

### <a name="inspecting-the-evaluation-results"></a>Controllo dei risultati di valutazione
Dopo aver eseguito l'esperimento, è possibile fare clic sulla porta di output del modulo [Evaluate Model][evaluate-model] e selezionare *Visualize (Visualizza* ) per visualizzare i risultati della valutazione. Le metriche di valutazione disponibili per i modelli di regressione sono: *Mean Absolute Error* (Errore assoluto medio), *Root Mean Absolute Error* (Errore assoluto medio radice), *Relative Absolute Error* (Errore assoluto relativo), *Relative Squared Error* (Errore quadratico relativo) e *Coefficient of Determination* (Coefficiente di determinazione).

In questo caso, il termine "errore" rappresenta la differenza tra il valore stimato e il valore reale. Il valore assoluto o il quadrato di questa differenza viene calcolato in genere per ottenere il margine totale dell'errore in tutte le istanze, poiché la differenza tra il valore stimato e quello reale potrebbe essere negativa in alcuni casi. Le metriche di errore misurano le prestazioni predittive di un modello di regressione in termini di deviazione media delle stime rispetto ai valori reali. Più i valori di errore sono bassi, più il modello effettua stime precise. Una metrica di errore complessivo pari a 0 indica che il modello corrisponde perfettamente ai dati.

Il coefficiente di determinazione, altrimenti noto come "valore quadratico R", rappresenta, inoltre, un modo standard di misurazione della percentuale di idoneità del modello rispetto ai dati. Può essere definito come la percentuale di variazione esplicitata dal modello. Una percentuale più elevata è migliore nel caso in cui 1 indica un'idoneità perfetta.

![Metriche di valutazione della regressione lineare](./media/evaluate-model-performance/2.png)

Figura 2. Metriche di valutazione della regressione lineare.

### <a name="using-cross-validation"></a>Uso della convalida incrociata
Come indicato in precedenza, è possibile eseguire il training, il punteggio e le valutazioni ripetute automaticamente usando il modulo [Cross-Validate Model][cross-validate-model] . In questo caso è sufficiente un set di dati, un modello senza training e un modulo [Cross-Validate Model][cross-validate-model] (vedere la figura seguente). È necessario impostare la colonna Label su *Price* nelle proprietà del modulo [Cross-Validate Model][cross-validate-model] .

![Convalida incrociata di un modello di regressione](./media/evaluate-model-performance/3.png)

Figura 3. Esecuzione della convalida incrociata di un modello di regressione.

Dopo aver eseguito l'esperimento, è possibile esaminare i risultati della valutazione facendo clic sulla porta di output destra del modulo [Cross-Validate Model][cross-validate-model] . In questo modo viene fornita una visualizzazione dettagliata delle metriche di ciascuna iterazione (sezione) e i risultati medi di ciascuna delle metriche (figura 4).

![Risultati della convalida incrociata di un modello di regressione](./media/evaluate-model-performance/4.png)

Figura 4. Risultati della convalida incrociata di un modello di regressione.

## <a name="evaluating-a-binary-classification-model"></a>Valutazione di un modello di classificazione binaria
In uno scenario di classificazione binaria la variabile di destinazione ha solo due risultati possibili, ad esempio: {0, 1} o {false, true}, {negative, positive}. Si presupponga di ricevere un set di dati di un dipendente adulto con alcune variabili demografiche e occupazionali e di dover stimare il livello di reddito, una variabile binaria con i valori {"<=50.000", ">50.000"}. In altri termini, la classe negativa rappresenta il caso in cui il dipendente realizza un valore inferiore o uguale a 50.000 l'anno, mentre la classe positiva rappresenta tutti gli altri dipendenti. Come nello scenario della regressione, verrà eseguito il training di un modello, verrà calcolato il punteggio di alcuni dati e verranno valutati i risultati. La differenza principale è la scelta delle metriche per la versione classica di Azure Machine Learning Studio calcoli e output. Per illustrare lo scenario di stima del livello di reddito, il set di dati per [adulti](https://archive.ics.uci.edu/ml/datasets/Adult) viene usato per creare un esperimento di studio (classico) e valutare le prestazioni di un modello di regressione logistica a due classi, un classificatore binario di uso comune.

### <a name="creating-the-experiment"></a>Creazione di un esperimento
Aggiungere i moduli seguenti all'area di lavoro nella versione classica di Azure Machine Learning Studio:

* Adult Census Income Binary Classification dataset
* [Regressione logistica a due classi][two-class-logistic-regression]
* [Train Model][train-model]
* [Modello di Punteggio][score-model]
* [Valuta modello][evaluate-model]

Connettere le porte come illustrato di seguito nella figura 5 e impostare la colonna Label del modulo [Train Model][train-model] su *Income*.

![Valutazione di un modello di classificazione binaria](./media/evaluate-model-performance/5.png)

Figura 5. Valutazione di un modello di classificazione binaria.

### <a name="inspecting-the-evaluation-results"></a>Controllo dei risultati di valutazione
Dopo aver eseguito l'esperimento, è possibile fare clic sulla porta di output del modulo [Evaluate Model][evaluate-model] e selezionare *Visualize (Visualizza* ) per visualizzare i risultati della valutazione (Figura 7). Le metriche di valutazione disponibili per i modelli di classificazione binaria sono: *Accuracy* (Accuratezza), *Precision* (Precisione), *Recall* (Richiamo), *F1 Score* (Punteggio F1) e *AUC*. Il modulo restituisce anche una matrice di confusione che mostra il numero di veri positivi, falsi negativi, falsi positivi e veri negativi, nonché le curve *ROC*, *Precision/Recall* (Precisione/Richiamo) e *Lift* (Accuratezza).

L'accuratezza è semplicemente la percentuale delle istanze classificate correttamente. In genere è la prima metrica che viene osservata quando si valuta un classificatore. Tuttavia, quando i dati di test non sono bilanciati (se la maggior parte delle istanze appartiene a una delle classi) o se l'utente è più interessato alle prestazioni di una classe, l'accuratezza non mostra realmente l'efficacia di un classificatore. Nello scenario di classificazione del livello di reddito, si supponga di eseguire il test di alcuni dati in cui il 99% delle istanze rappresenta le persone che guadagnano una cifra inferiore o uguale a 50.000 l'anno. È possibile ottenere un'accuratezza pari a 0,99 facendo una stima della classe “<=50.000” per tutte le istanze. In questo caso sembra che il classificatore svolga un buon lavoro in linea generale, ma in realtà non è in grado di classificare correttamente gli individui con un reddito superiore (il restante 1%).

Per questo motivo è utile calcolare metriche aggiuntive che raccolgano aspetti più specifici della valutazione. Prima di entrare nei dettagli di tali metriche, è importante comprendere la matrice di confusione della valutazione di una classificazione binaria. Le etichette di classe nel set di training possono assumere solo due valori possibili, che in genere fanno riferimento a positivo o negativo. Le istanze positive e negative stimate correttamente da un classificatore si definiscono rispettivamente valori veri positivi (VP) e veri negativi (VN). Analogamente, le istanze classificate in modo errato si definiscono valori falsi positivi (FP) e falsi negativi (FN). La matrice di confusione è semplicemente una tabella che mostra il numero di istanze che rientrano in ognuna delle quattro categorie. La versione classica di Azure Machine Learning Studio decide automaticamente quale delle due classi nel set di dati è la classe positiva. Se le etichette delle classi sono valori booleani o interi, le istanze con etichetta "true" o "1" vengono assegnate alla classe positiva. Se le etichette sono stringhe, ad esempio con il set di dati del reddito, le etichette vengono ordinate alfabeticamente e il primo livello viene scelto come classe negativa mentre il secondo livello è la classe positiva.

![Matrice di confusione di classificazione binaria](./media/evaluate-model-performance/6a.png)

Figura 6. Matrice di confusione di classificazione binaria.

Tornando al problema della classificazione del reddito, di seguito vengono fornite alcune domande sulla valutazione, utili a comprendere le prestazioni del classificatore usato. Una domanda naturale è:' out of the individuals that the Model ha stimato di guadagnare > 50 K (TP + FP), quanti sono stati classificati correttamente (TP)?' È possibile rispondere a questa domanda osservando la **precisione** del modello, che è la percentuale di positivi classificati correttamente: VP/(VP+FP). Un'altra domanda comune è la seguente: "Di tutti i dipendenti con un reddito >50.000 (VP+FN), quanti sono stati classificati correttamente dal classificatore (VP)?". Questo è il **richiamo** o tasso di veri positivi, VP/(VP+FN), del classificatore. Come si può notare, vi è un chiaro compromesso tra precisione e richiamo. Ad esempio, in presenza di un set di dati relativamente bilanciato, un classificatore che stima istanze soprattutto positive avrà un richiamo elevato ma una precisione più bassa, poiché molte delle istanze negative verranno classificate in modo errato dando come risultato una serie di falsi positivi. Per vedere un tracciato delle variazioni di queste due metriche, è possibile fare clic sulla curva **PRECISIONE/RICHIAMO** nella pagina di output dei risultati di valutazione (la parte superiore sinistra della figura 7).

![Risultati della valutazione della classificazione binaria](./media/evaluate-model-performance/7.png)

Figura 7. Risultati della valutazione della classificazione binaria.

Un'altra metrica correlata spesso usata è **Punteggio F1**, che prende in considerazione precisione e richiamo. Si tratta della media armonica di queste due metriche e viene calcolata come tale: F1 = 2 (precisione x richiamo)/(precisione + richiamo). Il punteggio F1 è un buon modo per riassumere la valutazione in un unico numero, ma si dovrebbe sempre tenere conto di precisione e richiamo parallelamente per comprendere meglio il comportamento di un classificatore.

È anche possibile confrontare il tasso di veri positivi e il tasso di falsi positivi nella curva **ROC (Receiver Operating Characteristic)** e il valore **AUC (Area Under the Curve)** corrispondente. Più questa curva è vicina all'angolo superiore sinistro, migliori sono le prestazioni del classificatore (vale a dire portando al massimo il tasso vero positivo e riducendo al minimo il tasso falso positivo). Le curve che si trovano vicino alla diagonale del tracciato risultano dai classificatori che tendono a fare delle stime al limite della casualità.

### <a name="using-cross-validation"></a>Uso della convalida incrociata
Come nell'esempio di regressione, è possibile eseguire la convalida incrociata per eseguire ripetutamente il training, il punteggio e la valutazione di subset diversi di dati automaticamente. Analogamente, è possibile usare il modulo [Cross-Validate Model][cross-validate-model] , un modello di regressione logistica non sottoposto a training e un set di dati. La colonna Label deve essere impostata su *Income* nelle proprietà del modulo [Cross-Validate Model][cross-validate-model] . Dopo aver eseguito l'esperimento e aver fatto clic sulla porta di output destra del modulo [Cross-Validate Model][cross-validate-model] , è possibile visualizzare i valori delle metriche di classificazione binaria per ogni sezione, oltre alla deviazione media e standard di ognuna. 

![Convalida incrociata di un modello di classificazione binaria](./media/evaluate-model-performance/8.png)

Figura 8. Convalida incrociata di un modello di classificazione binaria.

![Risultati della convalida incrociata di un classificatore binario.](./media/evaluate-model-performance/9.png)

Figura 9. Risultati della convalida incrociata di un classificatore binario.

## <a name="evaluating-a-multiclass-classification-model"></a>Valutazione di un modello di classificazione multiclasse
In questo esperimento verrà usato il popolare set di dati [Iris](https://archive.ics.uci.edu/ml/datasets/Iris "Iris") , che contiene le istanze di tre tipi diversi (classi) del Plant Iris. Sono disponibili quattro valori di funzionalità (lunghezza/larghezza SEPA e lunghezza/larghezza petalo) per ogni istanza. Negli esperimenti precedenti è stato eseguito il training e il testing dei modelli usando gli stessi set di impostazioni. Qui viene usato il modulo [Split data][split] per creare due subset dei dati, eseguire il training sul primo e assegnare un punteggio e valutare il secondo. Il set di dati Iris è disponibile pubblicamente nel [repository UCI Machine Learning](https://archive.ics.uci.edu/ml/index.html)e può essere scaricato usando un modulo [Import Data][import-data] .

### <a name="creating-the-experiment"></a>Creazione di un esperimento
Aggiungere i moduli seguenti all'area di lavoro nella versione classica di Azure Machine Learning Studio:

* [Importazione dei dati][import-data]
* [Foresta delle decisioni multiclasse][multiclass-decision-forest]
* [Suddividere i dati][split]
* [Train Model][train-model]
* [Modello di Punteggio][score-model]
* [Valuta modello][evaluate-model]

Connettere le porte come mostrato in basso nella figura 10.

Impostare l'indice della colonna Label del modulo [Train Model][train-model] su 5. Il set di dati non dispone di una riga di intestazione ma, com'è noto, le etichette delle classi si trovano nella quinta colonna.

Fare clic sul modulo [Import Data (Importa dati][import-data] ) e impostare la proprietà dell' *origine dati* su *URL Web tramite http*e l' *URL* http://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data.

Impostare la frazione delle istanze da usare per il training nel modulo [Split data][split] (ad esempio, 0,7).

![Valutazione di un classificatore multiclasse](./media/evaluate-model-performance/10.png)

Figura 10. Valutazione di un classificatore multiclasse

### <a name="inspecting-the-evaluation-results"></a>Controllo dei risultati di valutazione
Eseguire l'esperimento e fare clic sulla porta di output di [Evaluate Model][evaluate-model]. In questo caso, i risultati di valutazione sono presentati nel formato di una matrice di confusione. La matrice Mostra le istanze effettive di vs. stimate per tutte e tre le classi.

![Risultati della valutazione della classificazione multiclasse](./media/evaluate-model-performance/11.png)

Figura 11. Risultati di valutazione della classificazione a più classi.

### <a name="using-cross-validation"></a>Uso della convalida incrociata
Come indicato in precedenza, è possibile eseguire il training, il punteggio e le valutazioni ripetute automaticamente usando il modulo [Cross-Validate Model][cross-validate-model] . Sono necessari un set di dati, un modello senza training e un modulo [Cross-Validate Model][cross-validate-model] (vedere la figura seguente). Anche in questo caso è necessario impostare la colonna Label del modulo [Cross-Validate Model][cross-validate-model] (column index 5). Dopo aver eseguito l'esperimento e aver fatto clic sulla porta di output destra del [modello Cross-Validate Model][cross-validate-model], è possibile esaminare i valori delle metriche per ogni riduzioni, nonché la deviazione media e standard. Le metriche visualizzate sono simili a quelle illustrate nel caso della classificazione binaria. Tuttavia, nella classificazione multiclasse, il calcolo dei veri positivi/negativi e dei falsi positivi/negativi viene eseguito tenendo conto delle singole classi, in quanto non esiste alcuna classe globale positiva o negativa. Ad esempio, quando si esegue il calcolo della precisione o del richiamo della classe "Iris-setosa", si presuppone che questa sia la classe positiva e le altre quelle negative.

![Convalida incrociata di un modello di classificazione multiclasse](./media/evaluate-model-performance/12.png)

Figura 12. Convalida incrociata di un modello di classificazione a più classi.

![Risultati della convalida incrociata di un modello di classificazione multiclasse](./media/evaluate-model-performance/13.png)

Figura 13. Risultati della convalida incrociata di un modello di classificazione multiclasse.

<!-- Module References -->
[cross-validate-model]: https://msdn.microsoft.com/library/azure/75fb875d-6b86-4d46-8bcc-74261ade5826/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[multiclass-decision-forest]: https://msdn.microsoft.com/library/azure/5e70108d-2e44-45d9-86e8-94f37c68fe86/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-logistic-regression]: https://msdn.microsoft.com/library/azure/b0fd7660-eeed-43c5-9487-20d9cc79ed5d/
