---
title: Ottimizzazione degli iperparametri del modello
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Tune Model iperparametris nel servizio Azure Machine Learning per eseguire uno sweep di parametri in un modello per determinare le impostazioni ottimali dei parametri.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: 06adfe66bfe894d7b3c95e3d416da866c7d103b3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515655"
---
# <a name="tune-model-hyperparameters"></a>Ottimizzazione degli iperparametri del modello

Questo articolo descrive come usare il modulo [Tune Model iperparametri](tune-model-hyperparameters.md) in Azure Machine Learning Designer (Preview) per determinare gli iperparametri ottimali per un modello di apprendimento automatico specifico. Il modulo compila e testa più modelli, usando diverse combinazioni di impostazioni e confronta le metriche su tutti i modelli per ottenere la combinazione di impostazioni. 

Il *parametro* dei termini e l' *iperparametro* possono essere confusi. I *parametri* del modello sono quelli impostati nel riquadro proprietà. Fondamentalmente, questo modulo esegue uno *sweep di parametri* sulle impostazioni dei parametri specificati e apprende un set ottimale di _iperparametri_, che potrebbe essere diverso per ogni specifico albero delle decisioni, set di dati o metodo di regressione. Il processo di individuazione della configurazione ottimale viene talvolta definito *ottimizzazione*. 

Il modulo supporta il metodo seguente per trovare le impostazioni ottimali per un modello 

**Training e ottimizzazione integrati**: è possibile configurare un set di parametri da usare e quindi consentire al modulo di scorrere più combinazioni, misurando la precisione fino a trovare un modello "migliore". Con la maggior parte dei moduli di apprendimento è possibile scegliere quali parametri devono essere modificati durante il processo di training e quali devono rimanere corretti.

    Depending on how long you want the tuning process to run, you might decide to exhaustively test all combinations, or you could shorten the process by establishing a grid of parameter combinations and testing a randomized subset of the parameter grid.

 Questo metodo genera un modello sottoposto a training che è possibile salvare per il riutilizzo.  

### <a name="related-tasks"></a>Attività correlate

+ Prima di eseguire l'ottimizzazione, applicare la selezione delle funzioni per determinare le colonne o le variabili con il valore più elevato per le informazioni.

## <a name="how-to-configure-tune-model-hyperparameters"></a>Come configurare gli iperparametri del modello di ottimizzazione  

In generale, l'apprendimento degli iperparametri ottimali per un modello di apprendimento automatico specifico richiede una notevole pipeline.

### <a name="train-a-model-using-a-parameter-sweep"></a>Eseguire il training di un modello usando uno sweep di parametri  

Questa sezione descrive come eseguire uno sweep di parametri di base, che esegue il training di un modello usando il modulo [Tune Model iperparametri](tune-model-hyperparameters.md) .

1.  Aggiungere il modulo [Tune Model iperparametris](tune-model-hyperparameters.md) alla pipeline nella finestra di progettazione.

2.  Connettere un modello non sottoposto a training all'input più a sinistra. 

3. Impostare l'opzione **crea modalità trainer** su **intervallo parametri** e utilizzare il **Generatore di intervalli** per specificare un intervallo di valori da utilizzare nello sweep di parametri.  

    Quasi tutti i moduli di classificazione e regressione supportano uno sweep di parametri integrato. Per gli studenti che non supportano la configurazione di un intervallo di parametri, è possibile testare solo i valori dei parametri disponibili.

    È possibile impostare manualmente il valore per uno o più parametri, quindi eseguire lo sweep sui parametri rimanenti. Questo potrebbe risparmiare tempo.

4.  Aggiungere il set di dati che si vuole usare per il training e connetterlo all'input centrale degli [iperparametri del modello di ottimizzazione](tune-model-hyperparameters.md).  

    Facoltativamente, se si dispone di un set di dati con tag, è possibile connetterlo alla porta di input più a destra (**set di dati di convalida facoltativo**). In questo modo è possibile misurare l'accuratezza durante il training e l'ottimizzazione.

5.  Nel riquadro **Proprietà** di [Tune Model iperparametri](tune-model-hyperparameters.md)scegliere un valore per la modalità di **sweep dei parametri**. Questa opzione consente di controllare la modalità di selezione dei parametri.

    - **Griglia intera**: quando si seleziona questa opzione, il modulo esegue il ciclo su una griglia predefinita dal sistema, per provare combinazioni diverse e identificare il migliore discente. Questa opzione è utile per i casi in cui non si conoscono le impostazioni dei parametri migliori e si vuole provare tutte le possibili combinazioni di valori.

    - **Sweep casuale**: quando si seleziona questa opzione, il modulo selezionerà i valori dei parametri in modo casuale su un intervallo definito dal sistema. È necessario specificare il numero massimo di esecuzioni che si desidera venga eseguito dal modulo. Questa opzione è utile per i casi in cui si vuole aumentare le prestazioni del modello usando le metriche di propria scelta, conservando comunque le risorse di calcolo.

    Se si sceglie una sweep casuale, è possibile specificare il **numero massimo di esecuzioni nello Sweep casuale**, ovvero il numero di volte in cui il modello deve essere sottoposto a training, usando una combinazione casuale di valori di parametro.

6.  Per **colonna etichetta**, avviare il selettore di colonna per scegliere una singola colonna etichetta.

7.  **Numero massimo di esecuzioni nello Sweep casuale**: se si sceglie una sweep casuale, è possibile specificare il numero di volte in cui il modello deve essere sottoposto a training, usando una combinazione casuale di valori di parametro.

    **Numero massimo di esecuzioni su griglia casuale**: questa opzione controlla anche il numero di iterazioni su un campionamento casuale di valori di parametro, ma i valori non vengono generati in modo casuale dall'intervallo specificato. viene invece creata una matrice di tutte le possibili combinazioni di valori dei parametri e un campionamento casuale viene preso attraverso la matrice. Questo metodo è più efficiente e meno soggetto a sovracampionamento o sottocampionamento a livello di area.

8.  Scegliere una singola metrica da usare quando si **classificano** i modelli.

    Quando si esegue uno sweep di parametri, tutte le metriche applicabili per il tipo di modello vengono calcolate e vengono restituite nel rapporto **risultati Sweep** . Metriche separate vengono usate per i modelli di regressione e classificazione.

    Tuttavia, la metrica scelta determina il modo in cui vengono classificati i modelli. Solo il modello superiore, classificato in base alla metrica scelta, viene restituito come modello sottoposto a training da usare per l'assegnazione dei punteggi.

9.  Per il valore di **inizializzazione casuale**, digitare un numero da utilizzare durante l'inizializzazione dello sweep del parametro. 

10. Eseguire la pipeline.

## <a name="results-of-hyperparameter-tuning"></a>Risultati dell'ottimizzazione degli iperparametri

Al termine del training:

+ Per visualizzare un set di metriche di accuratezza per il modello migliore, fare clic con il pulsante destro del mouse sul modulo, scegliere **Sweep risultati**, quindi selezionare **Visualizza**.

    Tutte le metriche di accuratezza applicabili al tipo di modello vengono restituite, ma la metrica selezionata per la classificazione determina quale modello viene considerato "migliore".

+ Per usare il modello per l'assegnazione dei punteggi in altre pipeline, senza dover ripetere il processo di ottimizzazione, fare clic con il pulsante destro del mouse sull'output del modello e selezionare **Salva come modello con training**. 


## <a name="technical-notes"></a>Note tecniche

Questa sezione contiene informazioni dettagliate sull'implementazione, suggerimenti e risposte alle domande più frequenti.

### <a name="how-a-parameter-sweep-works"></a>Funzionamento di uno sweep di parametri

In questa sezione viene descritto il funzionamento generale dello sweep di parametri e il modo in cui le opzioni del modulo interagiscono.

Quando si configura uno sweep di parametri, si definisce l'ambito della ricerca, per usare un numero finito di parametri selezionati in modo casuale o una ricerca completa su uno spazio dei parametri definito.

+ **Sweep casuale**: questa opzione consente di trainare un modello usando un numero impostato di iterazioni. 

     È possibile specificare un intervallo di valori di cui eseguire l'iterazione e il modulo usa un subset di tali valori scelto in modo casuale.  I valori vengono scelti con la sostituzione, ovvero i numeri scelti in modo casuale non vengono rimossi dal pool di numeri disponibili. Quindi, la probabilità di qualsiasi valore selezionato rimane invariata in tutti i passaggi.  

+ **Griglia intera**: l'opzione per l'uso dell'intera griglia significa solo che: ogni combinazione viene testata. Questa opzione può essere considerata la più completa, ma richiede la maggior parte del tempo. 

### <a name="controlling-the-length-and-complexity-of-training"></a>Controllo della lunghezza e della complessità del training

L'iterazione su molte combinazioni di impostazioni può richiedere molto tempo, in modo che il modulo fornisca diversi modi per vincolare il processo:

+ Limitare il numero di iterazioni utilizzate per il test di un modello
+ Limitare lo spazio dei parametri
+ Limitare sia il numero di iterazioni che lo spazio dei parametri

È consigliabile eseguire la pipeline con le impostazioni per determinare il metodo più efficiente di training su un set di dati e un modello specifici.

### <a name="choosing-an-evaluation-metric"></a>Scelta di una metrica di valutazione

Un report che contiene l'accuratezza per ogni modello viene visualizzato alla fine, in modo da poter esaminare i risultati della metrica. Un set uniforme di metriche viene usato per tutti i modelli di classificazione binaria, l'accuratezza viene usata per tutti i modelli di classificazione multiclasse e viene usato un set di metriche diverso per i modelli di regressione. Tuttavia, durante il training, è necessario scegliere una **singola** metrica da usare per classificare i modelli generati durante il processo di ottimizzazione. Si potrebbe notare che la metrica migliore varia a seconda del problema aziendale e del costo di falsi positivi e falsi negativi.

#### <a name="metrics-used-for-binary-classification"></a>Metriche usate per la classificazione binaria

-   **Accuratezza** La proporzione dei risultati reali al numero totale di case.  

-   **Precisione** Percentuale di risultati reali a risultati positivi.  

-   **Richiama** Frazione di tutti i risultati corretti rispetto a tutti i risultati.  

-   **Punteggio F** Misura che bilancia la precisione e il richiamo.  

-   **AUC** Valore che rappresenta l'area sotto la curva quando vengono tracciati falsi positivi sull'asse x e i veri positivi sono tracciati sull'asse y.  

-   **Perdita logaritmica media** Differenza tra due distribuzioni di probabilità, ovvero la vera e l'altra nel modello.  

#### <a name="metrics-used-for-regression"></a>Metriche usate per la regressione

-   Media **Absolute Error** equivale a tutti gli errori nel modello, dove Error indica la distanza del valore stimato rispetto al valore true. Spesso abbreviato come **Mae**.  

-   La **radice dell'errore quadratico medio** misura la media dei quadrati degli errori e quindi prende la radice di tale valore. Spesso abbreviato come **valori RMSE**  

-   **Errore assoluto relativo** rappresenta l'errore come percentuale del valore true.  

-   L' **errore quadratico relativo** normalizza il totale degli errori quadratici dividendo per l'errore quadrato totale dei valori stimati.  

-   **Coefficiente di determinazione** Un numero singolo che indica il modo in cui i dati si adattano a un modello. Il valore 1 indica che il modello corrisponde esattamente ai dati. un valore pari a zero indica che i dati sono casuali, altrimenti non possono essere adattati al modello. Spesso definito r **<sup>2</sup>** , **r<sup>2</sup>** o **r-squared**.  

### <a name="modules-that-do-not-support-a-parameter-sweep"></a>Moduli che non supportano lo sweep di parametri

Quasi tutti gli studenti in Azure Machine Learning supportano la convalida incrociata con uno sweep di parametri integrato, che consente di scegliere i parametri con cui eseguire la pipeline. Se lo Learner non supporta l'impostazione di un intervallo di valori, è comunque possibile utilizzarlo nella convalida incrociata. In questo caso, per lo sweep viene selezionato un intervallo di valori consentiti. 


## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning servizio. 

