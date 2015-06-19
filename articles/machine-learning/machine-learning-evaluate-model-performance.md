<properties 
	pageTitle="Come valutare le prestazioni del modello in Azure Machine Learning | Azure" 
	description="Viene spiegato come valutare le prestazioni del modello in Azure Machine Learning." 
	services="machine-learning"
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/11/2015" 
	ms.author="bradsev" />


# Come valutare le prestazioni del modello in Azure Machine Learning

In questo argomento viene illustrato come valutare le prestazioni di un modello in Azure Machine Learning Studio e viene fornita una breve spiegazione delle metriche disponibili per questa attività. Vengono presentati tre scenari di apprendimento sorvegliato comuni: 

* Regressione
* Classificazione binaria 
* Classificazione multiclasse


La valutazione delle prestazioni di un modello è una delle fasi principali nel processo di analisi scientifica dei dati. Indica quanto è stato positivo il punteggio (stime) di un set di dati da un modello sottoposto a training. 

Azure Machine Learning supporta la valutazione del modello attraverso due moduli principali di apprendimento automatico: **Valutazione modello** e **Convalida incrociata modello**. Questi moduli consentono all'utente di osservare le prestazioni del proprio modello in termini di una serie di metriche comunemente usate in Machine Learning e nella statistica.

##Confronto tra valutazione e convalida incrociata##
La valutazione e la convalida incrociata sono due modi standard di misurare le prestazioni del proprio modello. Entrambe generano metriche di valutazione che l'utente può utilizzare per controllare o mettere a confronto quelle di altri modelli.

**Valuta modello** presuppone un set di dati con punteggio come input (oppure 2 se si desidera confrontare le prestazioni di 2 modelli diversi). Ciò significa che, prima di poter valutare i risultati, è necessario eseguire il training del modello usando il modulo **Training modello** e fare delle stime su alcuni set di dati usando il modulo **Punteggio modello**. La valutazione è basata sulle probabilità/etichette con punteggio e sulle etichette vere, che sono tutte restituite dal modulo **Punteggio modello**.

In alternativa, è possibile utilizzare la convalida incrociata per eseguire una serie di operazioni di valutazione, punteggio e training (10 sezioni) in modo automatico su diversi subset di dati di input. I dati di input vengono suddivisi in 10 partizioni, di cui una riservata per la convalida e le rimanenti usate per eseguire il training. Tale processo si ripete per 10 volte e viene calcolata una media delle metriche di valutazione. Ciò consente di determinare come verrebbero generalizzati nuovi set di dati da un modello. Il modulo **Convalida incrociata modello** prende un modello non sottoposto a training e alcuni set di dati con etichetta e restituisce i risultati di valutazione di ognuna delle 10 sezioni, oltre alla media dei risultati.

Nelle seguenti sezioni verranno creati modelli semplici di classificazione e regressione e verranno valutate le relative prestazioni, usando il modulo **Valuta modello** e il modulo **Convalida incrociata modello**.

##Valutazione di un modello di regressione##
Si supponga di voler stimare il prezzo di un'auto tramite alcune caratteristiche quali dimensioni, cavalli, specifiche del motore e così via. Si tratta di un tipico problema di regressione, in cui la variabile target (*prezzo*) è un valore numerico continuo. È possibile preparare un modello di regressione lineare semplice che, dati i valori caratteristici di una determinata auto, sia in grado di fare una stima del prezzo di tale auto. Questo modello di regressione può essere utilizzato per calcolare il punteggio dello stesso set di dati su cui si sta effettuando il training Una volta ottenuti i prezzi stimati per tutte le auto, è possibile valutare le prestazioni del modello osservando la differenza tra le stime e i prezzi reali in media. Per illustrare questo concetto, si utilizza  *Automobile price data (Raw) dataset* disponibile nella sezione **Set di dati salvati** in Azure Machine Learning Studio.
 
###Creazione di un esperimento###
Aggiungere i seguenti moduli all'area di lavoro in Azure Machine Learning Studio:

- Dati relativi ai prezzi delle automobili (non elaborati)
- Regressione lineare
- Training modello
- Modello di punteggio
- Valuta modello


Connettere le porte come mostrato in basso nella figura 1 e impostare la colonna delle etichette del **modulo Training modello** su  *price*.
 
![Evaluating a Regression Model](media/machine-learning-evaluate-model-performance/1.png)

Figura 1. Valutazione di un modello di regressione.

###Controllo dei risultati di valutazione###
Dopo aver eseguito l'esperimento, è possibile fare clic sulla porta di output del modulo **Valuta modello** e selezionare  *Visualize* per visualizzare i risultati di valutazione. Le metriche di valutazione disponibili per i modelli di regressione sono: *Mean Absolute Error*, *Root Mean Absolute Error*, *Relative Absolute Error*, *Relative Squared Error* e  *Coefficient of Determination*.

In questo caso, il termine "error" rappresenta la differenza tra il valore stimato e il valore reale. Il valore assoluto o il quadrato di tale differenza solitamente vengono calcolati per ottenere il margine totale dell'errore in tutte le istanze, poiché la differenza tra il valore stimato e quello reale potrebbe essere negativa in alcuni casi. Le metriche di errore misurano le prestazioni predittive di un modello di regressione in termini di deviazione media delle stime rispetto ai valori reali. Più i valori di errore sono bassi, più il modello effettua stime precise. Una metrica di errore complessivo pari a 0 indica che il modello corrisponde perfettamente ai dati.

Il coefficiente di determinazione, altrimenti noto come "valore quadratico R", rappresenta, inoltre, un modo standard di misurazione della percentuale di idoneità del modello rispetto ai dati. Può essere definito come la percentuale di variazione esplicitata dal modello. Una percentuale più elevata è migliore nel caso in cui 1 indica un'idoneità perfetta.
 
![Linear Regression Evaluation Metrics](media/machine-learning-evaluate-model-performance/2.png)

Figura 2. Metriche di valutazione della regressione lineare.

###Utilizzo della convalida incrociata###
Come accennato in precedenza, è possibile ripetere il training, il calcolo del punteggio e le valutazioni in modo automatico usando il modulo **Convalida incrociata modello**. In questo caso occorrono semplicemente un set di dati, un modello non sottoposto a training e un modulo **Convalida incrociata modello ** (vedere la figura in basso). Si tenga presente che è necessario impostare la colonna delle etichette su  *price* nelle proprietà del modulo **Convalida incrociata modello**.

![Cross-Validating a Regression Model](media/machine-learning-evaluate-model-performance/3.png)

Figura 3. Esecuzione della convalida incrociata di un modello di regressione.

Dopo aver eseguito l'esperimento, è possibile controllare i risultati di valutazione facendo clic sulla porta di output destra del modulo **Convalida incrociata modello**. In questo modo viene fornita una visualizzazione dettagliata delle metriche di ciascuna iterazione (sezione) e i risultati medi di ciascuna delle metriche (figura 4).
 
![Cross-Validation Results of a Regression Model](media/machine-learning-evaluate-model-performance/4.png)

Figura 4. Risultati della convalida incrociata di un modello di regressione.

##Valutazione di un modello di classificazione binaria##
In uno scenario di classificazione binaria la variabile target ha solo due risultati possibili, ad esempio: {0, 1} oppure {falso, vero}, {negativo, positivo}. Si presupponga di ricevere un set di dati di un dipendente adulto con alcune variabili demografiche e occupazionali e di dover stimare il livello di reddito, una variabile binaria con i valori {"<=50K", ">50K"}. In altri termini, la classe negativa rappresenta il caso in cui il dipendente realizza un valore inferiore o uguale a 50.000 l'anno,mentre la classe positiva rappresenta tutti gli altri dipendenti. Come nello scenario della regressione, verrà eseguito il training di un modello, verrà calcolato il punteggio di alcuni dati e verranno valutati i risultati. In questo caso la differenza principale consiste nella scelta degli output e dei calcoli delle metriche in Azure Machine Learning. Per illustrare lo scenario della stima del livello di reddito, verrà utilizzato il set di dati [Adulto](http://archive.ics.uci.edu/ml/datasets/Adult) per creare un esperimento in Azure Machine Learning e valutare le prestazioni di un modello di regressione logistica a due classi, un classificatore binario comunemente utilizzato.

###Creazione di un esperimento###
Aggiungere i seguenti moduli all'area di lavoro in Azure Machine Learning Studio:

- Set di dati relativo alla classificazione binaria del censimento del reddito di persone adulte
- Regressione logistica a due classi
- Training modello
- Modello di punteggio
- Valuta modello

Connettere le porte come mostrato in basso nella figura 5 e impostare la colonna delle etichette del modulo **Training modello** su  *income*.

![Evaluating a Binary Classification Model](media/machine-learning-evaluate-model-performance/5.png)

Figura 5. Valutazione di un modello di classificazione binaria.

###Controllo dei risultati di valutazione###
Dopo aver eseguito l'esperimento, è possibile fare clic sulla porta di output del modulo **Valutazione modello** e selezionare  *Visualize* per visualizzare i risultati di valutazione (figura 7). Le metriche di valutazione disponibili per i modelli di classificazione binaria sono: *Accuracy*, *Precision*, *Recall*, *F1 Score* e  *AUC*. Il modulo inoltre restituisce una matrice di confusione che consente di visualizzare il numero dei valori veri positivi, falsi negativi, falsi positivi e veri negativi, nonché  *ROC*, *Precision/Recall* e curve  *Lift*.

L'accuratezza è semplicemente la percentuale delle istanze classificate correttamente. In genere è la prima metrica che viene osservata quando si valuta un classificatore. Tuttavia, quando i dati di test non sono bilanciati (se la maggior parte delle istanze appartiene a una delle classi) o se l'utente è più interessato alle prestazioni di una classe, l'accuratezza non mostra realmente l'efficacia di un classificatore. Nello scenario di classificazione del livello di reddito, si supponga di eseguire il test di alcuni dati in cui il 99% delle istanze rappresenta le persone che guadagnano una cifra inferiore o uguale a 50.000 l'anno. È possibile ottenere un'accuratezza pari a 0,99 facendo una stima della classe "<=50K" per tutte le istanze. In questo caso sembra che il classificatore svolga un buon lavoro in linea generale, ma in realtà non è in grado di classificare correttamente gli individui con un reddito superiore (il restante 1%).

Per questo motivo è utile calcolare metriche aggiuntive che raccolgano aspetti più specifici della valutazione. Prima di entrare nei dettagli di tali metriche, è importante comprendere la matrice di confusione della valutazione di una classificazione binaria. Le etichette delle classi nel set di training possono assumere solo 2 valori possibili, cui faremo riferimento con positivo o negativo. Le istanze positive e negative stimate correttamente da un classificatore si definiscono rispettivamente valori veri positivi (VP) e veri negativi (VN). Analogamente, le istanze classificate in modo errato si definiscono valori falsi positivi (FP) e falsi negativi (FN). La matrice di confusione è semplicemente una tabella che mostra il numero di istanze all'interno di ognuna di queste 4 categorie. Azure Machine Learning stabilisce automaticamente quale delle due classi nel set di dati è quella positiva. Se le etichette delle classi sono valori booleani o interi, le istanze con etichetta "true" o "1" vengono assegnate alla classe positiva. Se si tratta di stringhe, come nel caso del set di dati sul reddito, le etichette vengono ordinate alfabeticamente: il primo livello sarà la classe negativa, mentre il secondo livello quella positiva.

![Binary Classification Confusion Matrix](media/machine-learning-evaluate-model-performance/6a.png)

Figura 6. Matrice di confusione di classificazione binaria.

Tornando al problema della classificazione del reddito, di seguito vengono fornite alcune domande sulla valutazione, utili a comprendere le prestazioni del classificatore utilizzato. Una domanda molto naturale da porsi è la seguente: "Delle persone con un modello di guadagno stimato >50.000 (VP+FP), quante sono state classificate correttamente (VP)?" È possibile rispondere a questa domanda osservando la **precisione** del modello, che è la percentuale dei valori positivi classificati correttamente: VP/(VP+FP). Un'altra domanda comune è la seguente: "Di tutti i dipendenti con un reddito >50.000 (VP+FN), quanti sono stati classificati correttamente dal classificatore (VP)?". Questo è il **richiamo** o tasso vero positivo: VP/(VP+FN) del classificatore. Come si può notare, vi è un chiaro compromesso tra precisione e richiamo. Ad esempio, in presenza di un set di dati relativamente bilanciato, un classificatore che stima istanze soprattutto positive avrà un richiamo elevato ma una precisione più bassa, poiché molte delle istanze negative verranno classificate in modo errato dando come risultato una serie di falsi positivi. Per vedere un tracciato delle variazioni di queste due metriche, è possibile fare clic sulla curva "PRECISIONE/RICHIAMO" nella pagina di output dei risultati di valutazione (la parte superiore sinistra della figura 7).

![Binary Classification Evaluation Results](media/machine-learning-evaluate-model-performance/7.png)

Figura 7. Risultati della valutazione della classificazione binaria.

Un'altra metrica correlata spesso utilizzata è **Punteggio F1**, che prende in considerazione precisione e richiamo. Si tratta della media armonica di queste 2 metriche ed è calcolata nel modo seguente: F1 = 2 (precisione x richiamo) / (precisione + richiamo). Il punteggio F1 è un buon modo per riassumere la valutazione in un unico numero, ma si dovrebbe sempre tenere conto di precisione e richiamo parallelamente per comprendere meglio il comportamento di un classificatore.

Inoltre, è possibile confrontare il tasso vero positivo e il tasso falso positivo nella curva **ROC (Receiver Operating Characteristic)** e il valore corrispondente dell'area sotto la curva **AUC (Area Under the Curve)**). Più questa curva è vicina all'angolo superiore sinistro, migliori sono le prestazioni del classificatore (vale a dire portando al massimo il tasso vero positivo e riducendo al minimo il tasso falso positivo). Le curve che si trovano vicino alla diagonale del tracciato risultano dai classificatori che tendono a fare delle stime al limite della casualità.

###Utilizzo della convalida incrociata###
Come nell'esempio della regressione, è possibile eseguire la convalida incrociata per ripetere il training, calcolare il punteggio e valutare diversi subset di dati in modo automatico. Analogamente, è possibile utilizzare il modulo **Convalida incrociata modello**, un modello di regressione logistica non sottoposto a training e un set di dati. La colonna delle etichette deve essere impostata su  *income* nelle proprietà del modulo **Convalida incrociata modello**. Dopo aver eseguito l'esperimento e selezionato la porta destra di output del modulo **Convalida incrociata modello**, è possibile visualizzare i valori metrici di classificazione binaria per ogni sezione, oltre alla deviazione media e standard di ciascuna di esse. 
 
![Cross-Validating a Binary Classification Model](media/machine-learning-evaluate-model-performance/8.png)

Figura 8. Convalida incrociata di un modello di classificazione binaria.

![Cross-Validation Results of a Binary Classifier](media/machine-learning-evaluate-model-performance/9.png)

Figura 9. Risultati della convalida incrociata di un classificatore binario.

##Valutazione di un modello di classificazione multiclasse##
In questo esperimento verrà utilizzato il noto set di dati [Iris](http://archive.ics.uci.edu/ml/datasets/Iris "Iris") che contiene istanze di 3 tipi diversi (classi) dell'iris. Esistono 4 valori caratteristici (lunghezza/larghezza sepalo e lunghezza/larghezza petalo) per ogni istanza. Negli esperimenti precedenti è stato eseguito il training e il test di modelli che usano gli stessi set di dati. In questo caso, verrà utilizzato il modulo Dividi per creare 2 subset di dati, eseguire il training sul primo e classificare e valutare il secondo. 
Il set di dati Iris è disponibile nel [repository di Machine Learning UCI](http://archive.ics.uci.edu/ml/index.html) e può essere scaricato usando il modulo **Lettore**.

###Creazione di un esperimento###
Aggiungere i seguenti moduli all'area di lavoro in Azure Machine Learning Studio:

- Reader
- Foresta di decisioni multiclasse
- Dividi
- Training modello
- Modello di punteggio
- Valuta modello

Connettere le porte come mostrato in basso nella figura 10.

Impostare l'indice della colonna delle etichette del modulo Training modello su 5. Il set di dati non dispone di una riga di intestazione ma, com'è noto, le etichette delle classi si trovano nella quinta colonna.

Fare clic sul modulo **Lettore** e impostare la proprietà  *Data source* su  *Web URL via HTTP* e  *URL* su http://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data.

Impostare la frazione delle istanze da utilizzare per il training nel modulo **Dividi** (ad esempio 0,7).
 
![Evaluating a Multiclass Classifier](media/machine-learning-evaluate-model-performance/10.png)

Figura 10. Valutazione di un classificatore multiclasse

###Controllo dei risultati di valutazione###
Eseguire l'esperimento e fare clic sulla porta di output di **Valutazione modello**. In questo caso, i risultati di valutazione sono presentati nel formato di una matrice di confusione. Nella matrice sono riportate le istanze stimate e reali per le 3 classi.
 
![Multiclass Classification Evaluation Results](media/machine-learning-evaluate-model-performance/11.png)

Figura 11. Risultati di valutazione della classificazione a più classi.

###Utilizzo della convalida incrociata###
Come accennato in precedenza, è possibile ripetere il training, il calcolo del punteggio e le valutazioni in modo automatico usando il modulo **Convalida incrociata modello**. Sarà necessario disporre di un set di dati, un modello non sottoposto a training e un modulo **Convalida incrociata modello** (vedere la figura in basso). Anche in questo caso è necessario impostare la colonna delle etichette del modulo **Valutazione incrociata modello** (indice della colonna 5 in questo caso). Dopo aver eseguito l'esperimento e selezionato la porta destra di output del modulo **Convalida incrociata modello**, è possibile controllare i valori metrici per ogni sezione, nonché la deviazione media e standard. Le metriche visualizzate sono simili a quelle illustrate nel caso della classificazione binaria. Tuttavia, si tenga presente che, nella classificazione a più classi, il calcolo dei veri positivi/negativi e dei falsi positivi/negativi è effettuato con un conteggio su una base specifica per ogni classe, poiché non esiste una classe positiva o negativa complessiva. Ad esempio, quando si esegue il calcolo della precisione o del richiamo della classe "Iris-setosa", si presuppone che questa sia la classe positiva e le altre quelle negative.
 
![Cross-Validating a Multiclass Classification Model](media/machine-learning-evaluate-model-performance/12.png)

Figura 12. Convalida incrociata di un modello di classificazione a più classi.


![Cross-Validation Results of a Multiclass Classification Model](media/machine-learning-evaluate-model-performance/13.png)

Figura 13. Risultati della convalida incrociata di un modello di classificazione multiclasse.

<!--HONumber=49--> 