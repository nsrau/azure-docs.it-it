---
title: Valutare le prestazioni del modello in Machine Learning | Documentazione Microsoft
description: Viene spiegato come valutare le prestazioni del modello in Azure Machine Learning.
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 5dc5348a-4488-4536-99eb-ff105be9b160
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: bradsev;garye
ms.openlocfilehash: 48ce4584f7270d78b1d09b848bfdd305d03012b9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-evaluate-model-performance-in-azure-machine-learning"></a>Come valutare le prestazioni del modello in Azure Machine Learning
Questo articolo illustra come valutare le prestazioni di un modello in Azure Machine Learning Studio e offre una breve spiegazione delle metriche disponibili per questa attività. L'argomento presenta inoltre tre scenari di apprendimento sorvegliato comuni: 

* Regressione
* Classificazione binaria 
* Classificazione multiclasse

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

La valutazione delle prestazioni di un modello è una delle fasi principali nel processo di analisi scientifica dei dati. Indica quanto è stato positivo il punteggio (stime) di un set di dati da un modello sottoposto a training. 

Azure Machine Learning supporta la valutazione del modello tramite due moduli di apprendimento automatico principali, ovvero [Evaluate Model][evaluate-model] e [Cross-Validate Model][cross-validate-model]. Questi moduli consentono all'utente di osservare le prestazioni del proprio modello in termini di una serie di metriche comunemente usate in Machine Learning e nella statistica.

## <a name="evaluation-vs-cross-validation"></a>Confronto tra la valutazione e la convalida incrociata
La valutazione e la convalida incrociata sono due modi standard di misurare le prestazioni del proprio modello. Entrambi generano metriche di valutazione che l'utente può usare per controllare o mettere a confronto quelle di altri modelli.

Il modulo [Evaluate Model][evaluate-model] presuppone l'esistenza di un set di dati con punteggio come input (oppure di due set di dati se si desidera confrontare le prestazioni di due modelli diversi). Ciò significa che, prima di valutare i risultati, è necessario eseguire il training del modello con il modulo [Train Model][train-model] ed eseguire stime su alcuni set di dati con il modulo [Score Model][score-model]. La valutazione è basata sulle probabilità/etichette con punteggio e sulle etichette vere, tutti elementi restituiti dal modulo [Score Model][score-model].

In alternativa, è possibile usare la convalida incrociata per eseguire una serie di operazioni di valutazione, punteggio e training (10 sezioni) in modo automatico su diversi subset di dati di input. I dati di input vengono suddivisi in dieci partizioni, di cui una riservata per la convalida e le rimanenti usate per eseguire il training. Tale processo si ripete per 10 volte e viene calcolata una media delle metriche di valutazione. Ciò consente di determinare come verrebbero generalizzati nuovi set di dati da un modello. Il modulo [Cross-Validate Model][cross-validate-model] riceve in input un modello non sottoposto a training e alcuni set di dati con etichetta e restituisce i risultati della valutazione di ognuna delle 10 sezioni, oltre alla media dei risultati.

Nelle sezioni seguenti verranno creati semplici modelli di regressione e classificazione e ne verranno valutate le prestazioni usando i moduli [Evaluate Model][evaluate-model] e [Cross-Validate Model][cross-validate-model].

## <a name="evaluating-a-regression-model"></a>Valutazione di un modello di regressione
Si supponga di voler stimare il prezzo di un'auto tramite alcune caratteristiche quali dimensioni, cavalli, specifiche del motore e così via. Si tratta di un tipico problema di regressione, in cui la variabile di destinazione (*price*) è un valore numerico continuo. È possibile preparare un modello di regressione lineare semplice che, dati i valori caratteristici di una determinata auto, sia in grado di fare una stima del prezzo di tale auto. Questo modello di regressione può essere usato per calcolare il punteggio dello stesso set di dati su cui si sta effettuando il training Una volta ottenuti i prezzi stimati per tutte le auto, è possibile valutare le prestazioni del modello osservando la differenza tra le stime e i prezzi reali in media. Per illustrare questo concetto, si usa il set di dati *Automobile price data (Raw)* disponibile nella sezione **Saved Datasets** (Set di dati salvati) in Azure Machine Learning Studio.

### <a name="creating-the-experiment"></a>Creazione di un esperimento
Aggiungere i seguenti moduli all'area di lavoro in Azure Machine Learning Studio:

* Automobile price data (Raw)
* [Linear Regression][linear-regression]
* [Train Model][train-model]
* [Score Model][score-model]
* [Evaluate Model][evaluate-model]

Connettere le porte come indicato nella figura 1 seguente e impostare la colonna delle etichette nel modulo [Train Model][train-model] su *price*.

![Valutazione di un modello di regressione](./media/evaluate-model-performance/1.png)

Figura 1. Valutazione di un modello di regressione.

### <a name="inspecting-the-evaluation-results"></a>Controllo dei risultati di valutazione
Dopo aver eseguito l'esperimento, è possibile fare clic sulla porta di output del modulo [Evaluate Model][evaluate-model] e selezionare *Visualize* (Visualizza) per visualizzare i risultati della valutazione. Le metriche di valutazione disponibili per i modelli di regressione sono: *Mean Absolute Error* (Errore assoluto medio), *Root Mean Absolute Error* (Errore assoluto medio radice), *Relative Absolute Error* (Errore assoluto relativo), *Relative Squared Error* (Errore quadratico relativo) e *Coefficient of Determination* (Coefficiente di determinazione).

In questo caso, il termine "errore" rappresenta la differenza tra il valore stimato e il valore reale. Il valore assoluto o il quadrato di tale differenza solitamente vengono calcolati per ottenere il margine totale dell'errore in tutte le istanze, poiché la differenza tra il valore stimato e quello reale potrebbe essere negativa in alcuni casi. Le metriche di errore misurano le prestazioni predittive di un modello di regressione in termini di deviazione media delle stime rispetto ai valori reali. Più i valori di errore sono bassi, più il modello effettua stime precise. Una metrica di errore complessivo pari a 0 indica che il modello corrisponde perfettamente ai dati.

Il coefficiente di determinazione, altrimenti noto come "valore quadratico R", rappresenta, inoltre, un modo standard di misurazione della percentuale di idoneità del modello rispetto ai dati. Può essere definito come la percentuale di variazione esplicitata dal modello. Una percentuale più elevata è migliore nel caso in cui 1 indica un'idoneità perfetta.

![Metriche di valutazione della regressione lineare](./media/evaluate-model-performance/2.png)

Figura 2. Metriche di valutazione della regressione lineare.

### <a name="using-cross-validation"></a>Uso della convalida incrociata
Come accennato in precedenza, è possibile ripetere il training, l'assegnazione del punteggio e le valutazioni in modo automatico usando il modulo [Cross-Validate Model][cross-validate-model]. In questo caso occorrono semplicemente un set di dati, un modello non sottoposto a training e un modulo [Cross-Validate Model][cross-validate-model] (vedere la figura seguente). Si noti che è necessario impostare la colonna delle etichette su *price* nelle proprietà del modulo [Cross-Validate Model][cross-validate-model].

![Convalida incrociata di un modello di regressione](./media/evaluate-model-performance/3.png)

Figura 3. Esecuzione della convalida incrociata di un modello di regressione.

Dopo aver eseguito l'esperimento, è possibile fare clic sulla porta di output destra del modulo [Cross-Validate Model][cross-validate-model] per controllare i risultati della valutazione. In questo modo viene fornita una visualizzazione dettagliata delle metriche di ciascuna iterazione (sezione) e i risultati medi di ciascuna delle metriche (figura 4).

![Risultati della convalida incrociata di un modello di regressione](./media/evaluate-model-performance/4.png)

Figura 4. Risultati della convalida incrociata di un modello di regressione.

## <a name="evaluating-a-binary-classification-model"></a>Valutazione di un modello di classificazione binaria
In uno scenario di classificazione binaria la variabile di destinazione ha solo due risultati possibili, ad esempio: {0, 1} o {false, true}, {negative, positive}. Si presupponga di ricevere un set di dati di un dipendente adulto con alcune variabili demografiche e occupazionali e di dover stimare il livello di reddito, una variabile binaria con i valori {"<=50.000", ">50.000"}. In altri termini, la classe negativa rappresenta il caso in cui il dipendente realizza un valore inferiore o uguale a 50.000 l'anno, mentre la classe positiva rappresenta tutti gli altri dipendenti. Come nello scenario della regressione, verrà eseguito il training di un modello, verrà calcolato il punteggio di alcuni dati e verranno valutati i risultati. In questo caso la differenza principale consiste nella scelta degli output e dei calcoli delle metriche in Azure Machine Learning. Per illustrare lo scenario della stima del livello di reddito, verrà usato il set di dati [Adult](http://archive.ics.uci.edu/ml/datasets/Adult) per creare un esperimento in Azure Machine Learning e valutare le prestazioni di un modello di regressione logistica a due classi, un classificatore binario comunemente usato.

### <a name="creating-the-experiment"></a>Creazione di un esperimento
Aggiungere i seguenti moduli all'area di lavoro in Azure Machine Learning Studio:

* Adult Census Income Binary Classification dataset
* [Two-Class Logistic Regression][two-class-logistic-regression]
* [Train Model][train-model]
* [Score Model][score-model]
* [Evaluate Model][evaluate-model]

Connettere le porte come indicato nella figura 5 seguente e impostare la colonna delle etichette del modulo [Train Model][train-model] su *income*.

![Valutazione di un modello di classificazione binaria](./media/evaluate-model-performance/5.png)

Figura 5. Valutazione di un modello di classificazione binaria.

### <a name="inspecting-the-evaluation-results"></a>Controllo dei risultati di valutazione
Dopo aver eseguito l'esperimento, è possibile fare clic sulla porta di output del modulo [Evaluate Model][evaluate-model] e selezionare *Visualize* (Visualizza) per visualizzare i risultati della valutazione (Figura 7). Le metriche di valutazione disponibili per i modelli di classificazione binaria sono: *Accuracy* (Accuratezza), *Precision* (Precisione), *Recall* (Richiamo), *F1 Score* (Punteggio F1) e *AUC*. Il modulo restituisce anche una matrice di confusione che mostra il numero di veri positivi, falsi negativi, falsi positivi e veri negativi, nonché le curve *ROC*, *Precision/Recall* (Precisione/Richiamo) e *Lift* (Accuratezza).

L'accuratezza è semplicemente la percentuale delle istanze classificate correttamente. In genere è la prima metrica che viene osservata quando si valuta un classificatore. Tuttavia, quando i dati di test non sono bilanciati (se la maggior parte delle istanze appartiene a una delle classi) o se l'utente è più interessato alle prestazioni di una classe, l'accuratezza non mostra realmente l'efficacia di un classificatore. Nello scenario di classificazione del livello di reddito, si supponga di eseguire il test di alcuni dati in cui il 99% delle istanze rappresenta le persone che guadagnano una cifra inferiore o uguale a 50.000 l'anno. È possibile ottenere un'accuratezza pari a 0,99 facendo una stima della classe “<=50.000” per tutte le istanze. In questo caso sembra che il classificatore svolga un buon lavoro in linea generale, ma in realtà non è in grado di classificare correttamente gli individui con un reddito superiore (il restante 1%).

Per questo motivo è utile calcolare metriche aggiuntive che raccolgano aspetti più specifici della valutazione. Prima di entrare nei dettagli di tali metriche, è importante comprendere la matrice di confusione della valutazione di una classificazione binaria. Le etichette delle classi nel set di training possono assumere solo 2 valori possibili, cui faremo riferimento con positivo o negativo. Le istanze positive e negative stimate correttamente da un classificatore si definiscono rispettivamente valori veri positivi (VP) e veri negativi (VN). Analogamente, le istanze classificate in modo errato si definiscono valori falsi positivi (FP) e falsi negativi (FN). La matrice di confusione è semplicemente una tabella che mostra il numero di istanze all'interno di ognuna di queste 4 categorie. Azure Machine Learning stabilisce automaticamente quale delle due classi nel set di dati è quella positiva. Se le etichette delle classi sono valori booleani o interi, le istanze con etichetta "true" o "1" vengono assegnate alla classe positiva. Se si tratta di stringhe, come nel caso del set di dati sul reddito, le etichette vengono ordinate alfabeticamente: il primo livello sarà la classe negativa, mentre il secondo livello quella positiva.

![Matrice di confusione di classificazione binaria](./media/evaluate-model-performance/6a.png)

Figura 6. Matrice di confusione di classificazione binaria.

Tornando al problema della classificazione del reddito, di seguito vengono fornite alcune domande sulla valutazione, utili a comprendere le prestazioni del classificatore usato. Una domanda da porsi è: "Delle persone con un modello di guadagno stimato >50.000 (VP+FP), quante sono state classificate correttamente (VP)?" È possibile rispondere a questa domanda osservando la **precisione** del modello, che è la percentuale di positivi classificati correttamente: VP/(VP+FP). Un'altra domanda comune è la seguente: "Di tutti i dipendenti con un reddito >50.000 (VP+FN), quanti sono stati classificati correttamente dal classificatore (VP)?". Questo è il **richiamo** o tasso di veri positivi, VP/(VP+FN), del classificatore. Come si può notare, vi è un chiaro compromesso tra precisione e richiamo. Ad esempio, in presenza di un set di dati relativamente bilanciato, un classificatore che stima istanze soprattutto positive avrà un richiamo elevato ma una precisione più bassa, poiché molte delle istanze negative verranno classificate in modo errato dando come risultato una serie di falsi positivi. Per vedere un tracciato delle variazioni di queste due metriche, è possibile fare clic sulla curva "PRECISIONE/RICHIAMO" nella pagina di output dei risultati di valutazione (la parte superiore sinistra della figura 7).

![Risultati della valutazione della classificazione binaria](./media/evaluate-model-performance/7.png)

Figura 7. Risultati della valutazione della classificazione binaria.

Un'altra metrica correlata spesso usata è **Punteggio F1**, che prende in considerazione precisione e richiamo. Si tratta della media armonica di queste 2 metriche ed è calcolata nel modo seguente: F1 = 2 (precisione x richiamo) / (precisione + richiamo). Il punteggio F1 è un buon modo per riassumere la valutazione in un unico numero, ma si dovrebbe sempre tenere conto di precisione e richiamo parallelamente per comprendere meglio il comportamento di un classificatore.

È anche possibile confrontare il tasso di veri positivi e il tasso di falsi positivi nella curva **ROC (Receiver Operating Characteristic)** e il valore **AUC (Area Under the Curve)** corrispondente. Più questa curva è vicina all'angolo superiore sinistro, migliori sono le prestazioni del classificatore (vale a dire portando al massimo il tasso vero positivo e riducendo al minimo il tasso falso positivo). Le curve che si trovano vicino alla diagonale del tracciato risultano dai classificatori che tendono a fare delle stime al limite della casualità.

### <a name="using-cross-validation"></a>Uso della convalida incrociata
Come nell'esempio della regressione, è possibile eseguire la convalida incrociata per ripetere il training, calcolare il punteggio e valutare diversi subset di dati in modo automatico. Analogamente, è possibile usare il modulo [Cross-Validate Model][cross-validate-model], un modello di regressione logistica non sottoposto a training, e un set di dati. La colonna delle etichette deve essere impostata su *income* nelle proprietà del modulo [Cross-Validate Model][cross-validate-model]. Dopo aver eseguito l'esperimento e selezionato la porta di output destra del modulo [Cross-Validate Model][cross-validate-model], è possibile visualizzare i valori metrici per ogni sezione, oltre alla deviazione media e standard di ognuna di esse. 

![Convalida incrociata di un modello di classificazione binaria](./media/evaluate-model-performance/8.png)

Figura 8. Convalida incrociata di un modello di classificazione binaria.

![Risultati della convalida incrociata di un classificatore binario.](./media/evaluate-model-performance/9.png)

Figura 9. Risultati della convalida incrociata di un classificatore binario.

## <a name="evaluating-a-multiclass-classification-model"></a>Valutazione di un modello di classificazione multiclasse
In questo esperimento verrà usato il comune set di dati [Iris](http://archive.ics.uci.edu/ml/datasets/Iris "Iris"), contenente istanze di 3 diversi tipi (classi) di iris. Esistono 4 valori caratteristici (lunghezza/larghezza sepalo e lunghezza/larghezza petalo) per ogni istanza. Negli esperimenti precedenti è stato eseguito il training e il test di modelli che usano gli stessi set di dati. In questo caso, viene usato il modulo [Split Data][split] per creare due subset di dati, eseguire il training sul primo e classificare e valutare il secondo. Il set di dati Iris è disponibile pubblicamente nel [repository di Machine Learning UCI](http://archive.ics.uci.edu/ml/index.html) e può essere scaricato usando un modulo [Import Data][import-data].

### <a name="creating-the-experiment"></a>Creazione di un esperimento
Aggiungere i seguenti moduli all'area di lavoro in Azure Machine Learning Studio:

* [Import Data][import-data]
* [Multiclass Decision Forest][multiclass-decision-forest]
* [Split Data][split]
* [Train Model][train-model]
* [Score Model][score-model]
* [Evaluate Model][evaluate-model]

Connettere le porte come mostrato in basso nella figura 10.

Impostare l'indice della colonna delle etichette del modulo [Train Model][train-model] su 5. Il set di dati non dispone di una riga di intestazione ma, com'è noto, le etichette delle classi si trovano nella quinta colonna.

Fare clic sul modulo [Import Data][import-data] (Impora dati) e impostare la proprietà *Data source* (Origine dati) su *Web URL via HTTP* (URL Web via HTTP) e *URL* su http://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data.

Impostare la frazione delle istanze da usare per il training nel modulo [Split Data][split], ad esempio 0,7.

![Valutazione di un classificatore multiclasse](./media/evaluate-model-performance/10.png)

Figura 10. Valutazione di un classificatore multiclasse

### <a name="inspecting-the-evaluation-results"></a>Controllo dei risultati di valutazione
Eseguire l'esperimento e fare clic sulla porta di output di [Evaluate Model][evaluate-model]. In questo caso, i risultati di valutazione sono presentati nel formato di una matrice di confusione. Nella matrice sono riportate le istanze stimate e reali per le 3 classi.

![Risultati della valutazione della classificazione multiclasse](./media/evaluate-model-performance/11.png)

Figura 11. Risultati di valutazione della classificazione a più classi.

### <a name="using-cross-validation"></a>Uso della convalida incrociata
Come accennato in precedenza, è possibile ripetere il training, l'assegnazione del punteggio e le valutazioni in modo automatico usando il modulo [Cross-Validate Model][cross-validate-model]. Saranno necessari un set di dati, un modello non sottoposto a training e un modulo [Cross-Validate Model][cross-validate-model] (vedere la figura seguente). È nuovamente necessario impostare la colonna delle etichette del modulo [Cross-Validate Model][cross-validate-model] (in questo caso, indice della colonna 5). Dopo aver eseguito l'esperimento e selezionato la porta destra di output del modulo [Cross-Validate Model][cross-validate-model], è possibile controllare i valori metrici per ogni sezione, nonché la deviazione media e standard. Le metriche visualizzate sono simili a quelle illustrate nel caso della classificazione binaria. Tuttavia, si tenga presente che, nella classificazione a più classi, il calcolo dei veri positivi/negativi e dei falsi positivi/negativi è effettuato con un conteggio su una base specifica per ogni classe, poiché non esiste una classe positiva o negativa complessiva. Ad esempio, quando si esegue il calcolo della precisione o del richiamo della classe "Iris-setosa", si presuppone che questa sia la classe positiva e le altre quelle negative.

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

