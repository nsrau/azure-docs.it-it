---
title: Tune Model Hyperparameters
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Sintonizzare gli iperparametri del modello in Azure Machine Learning per eseguire una sweep dei parametri in un modello per determinare le impostazioni ottimali dei parametri.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: ff0ccbf201f2b83dd446859d8054d115a70f402e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064167"
---
# <a name="tune-model-hyperparameters"></a>Tune Model Hyperparameters

Questo articolo descrive come usare il modulo Sintonizzare gli iperparametri del modello nella finestra di progettazione di Azure Machine Learning (anteprima). L'obiettivo è determinare gli iperparametri ottimali per un modello di apprendimento automatico. Il modulo compila e testa più modelli utilizzando diverse combinazioni di impostazioni. Confronta le metriche su tutti i modelli per ottenere le combinazioni di impostazioni. 

Il *parametro* terms e *l'hyperparameter* possono creare confusione. I *parametri* del modello sono quelli impostati nel riquadro destro del modulo. Fondamentalmente, questo modulo esegue una sweep di *parametro* sulle impostazioni dei parametri specificati. Viene illustrato un set ottimale di _iperparametri_, che potrebbe essere diverso per ogni albero delle decisioni, set di dati o metodo di regressione specifico. Il processo di ricerca della configurazione ottimale è talvolta chiamato *tuning*. 

Il modulo supporta il seguente metodo per trovare le impostazioni ottimali per un modello: *treno integrato e sintonia.* In questo metodo viene configurato un set di parametri da utilizzare. Si consente quindi al modulo di eseguire l'iterazione su più combinazioni. Il modulo misura la precisione fino a trovare un modello "migliore". Con la maggior parte dei moduli degli allievi, puoi scegliere quali parametri devono essere modificati durante il processo di formazione e quali devono rimanere fissi.

A seconda di quanto tempo si desidera eseguire il processo di ottimizzazione, è possibile decidere di testare in modo esaustivo tutte le combinazioni. In alternativa, è possibile abbreviare il processo stabilendo una griglia di combinazioni di parametri e testando un sottoinsieme randomizzato della griglia di parametri.

Questo metodo genera un modello sottoposto a training che è possibile salvare per il riutilizzo.  

> [!TIP] 
> È possibile eseguire un'attività correlata. Prima di iniziare l'ottimizzazione, applicare la selezione delle funzionalità per determinare le colonne o le variabili con il valore di informazioni più alto.

## <a name="how-to-configure-tune-model-hyperparameters"></a>Come configurare le iperparametri del modello di ottimizzazioneHow to configure Tune Model Hyperparameters  

L'apprendimento degli iperparametri ottimali per un modello di apprendimento automatico richiede un uso considerevole delle pipeline.

### <a name="train-a-model-by-using-a-parameter-sweep"></a>Eseguire il training di un modello utilizzando una sweep di parametro  

In questa sezione viene descritto come eseguire una sweep dei parametri di base, che esegue il training di un modello utilizzando il modulo Sintonizzare gli iperparametri del modello.

1.  Aggiungere il modulo Sintonizzare gli iperparametri del modello alla pipeline nella finestra di progettazione.

2.  Collegare un modello non sottoposto a training all'input più a sinistra. 



4.  Aggiungere il set di dati che si vuole usare per il training e connetterlo all'input centrale di Ottimizzazione dei parametri modello.  

    Facoltativamente, se si dispone di un set di dati con tag, è possibile connetterlo alla porta di input più a destra ( Set di dati di**convalida facoltativo).** Ciò consente di misurare la precisione durante l'allenamento e l'ottimizzazione.

5.  Nel pannello a destra di Sintonizzare iperparametri modello, scegliete un valore per **Modalità sweep parametro**. Questa opzione controlla la modalità di selezione dei parametri.

    - **Intera griglia**: Quando si seleziona questa opzione, il modulo esegue un ciclo su una griglia predefinita dal sistema, per provare diverse combinazioni e identificare l'allievo migliore. Questa opzione è utile quando non si conoscono le impostazioni dei parametri migliori e si desidera provare tutte le possibili combinazioni di valori.

    - **Sweep casuale**: Quando si seleziona questa opzione, il modulo selezionerà casualmente i valori dei parametri in un intervallo definito dal sistema. È necessario specificare il numero massimo di esecuzioni che si desidera vengano eseguite dal modulo. Questa opzione è utile quando si desidera aumentare le prestazioni del modello utilizzando le metriche desiderate ma conservare comunque le risorse di elaborazione.    

6.  Per **Colonna etichetta**, aprire il selettore di colonna per scegliere una singola colonna etichetta.

7.  Scegliere il numero di esecuzioni:

    1. **Numero massimo di condutture su sweep casuale**: se si sceglie una sweep casuale, è possibile specificare quante volte il modello deve essere sottoposto a training, utilizzando una combinazione casuale di valori di parametro.

8.  Per **Classificazione**, scegliere una singola metrica da utilizzare per la classificazione dei modelli.

    Quando si esegue una sweep di parametro, il modulo calcola tutte le metriche applicabili per il tipo di modello e le restituisce nel report dei risultati di **Sweep.** Il modulo utilizza metriche separate per i modelli di regressione e classificazione.

    Tuttavia, la metrica scelta determina la modalità di classificazione dei modelli. Solo il modello superiore, classificato in base alla metrica scelta, viene restituito come modello sottoposto a training da utilizzare per il punteggio.

9.  In **Valore di serie casuale**, immettere un numero da utilizzare per avviare la sweep dei parametri. 

10. Inviare la pipeline.

## <a name="results-of-hyperparameter-tuning"></a>Risultati dell'ottimizzazione degli iperparametri

Quando l'allenamento è completo:

+ Per visualizzare un set di metriche di accuratezza per il modello migliore, fare clic con il pulsante destro del mouse sul modulo, quindi selezionare **Visualizza**.

    L'output include tutte le metriche di accuratezza che si applicano al tipo di modello, ma la metrica selezionata per la classificazione determina quale modello è considerato "migliore".

+ Per salvare un'istantanea del modello sottoposto a training, selezionare la scheda **Uscite** nel pannello destro del modulo **Modulo del modello.** Selezionare l'icona **Registra set di dati** per salvare il modello come modulo riutilizzabile.


## <a name="technical-notes"></a>Note tecniche

Questa sezione contiene i dettagli e i suggerimenti per l'implementazione.

### <a name="how-a-parameter-sweep-works"></a>Funzionamento di uno sweep di parametro

Quando si imposta una sweep di parametro, si definisce l'ambito della ricerca. La ricerca potrebbe utilizzare un numero finito di parametri selezionati in modo casuale. Oppure potrebbe trattarsi di una ricerca esaustiva su uno spazio di parametri definito dall'utente.

+ **Sweep casuale**: Questa opzione consente di addestrare un modello utilizzando un numero impostato di iterazioni. 

  Si specifica un intervallo di valori su cui eseguire l'iterazione e il modulo utilizza un sottoinsieme scelto casualmente di tali valori. I valori vengono scelti con la sostituzione, il che significa che i numeri scelti a caso non vengono rimossi dal pool di numeri disponibili. Pertanto, la possibilità che qualsiasi valore venga selezionato rimane invariata in tutte le passate.  

+ **Intera griglia**: L'opzione per utilizzare l'intera griglia significa che ogni combinazione viene testata. Questa opzione è la più accurata, ma richiede la maggior parte del tempo. 

### <a name="controlling-the-length-and-complexity-of-training"></a>Controllo della lunghezza e della complessità della formazione

L'iterazione su molte combinazioni di impostazioni può richiedere molto tempo, pertanto il modulo offre diversi modi per vincolare il processo:

+ Limitare il numero di iterazioni utilizzate per testare un modello.
+ Limitare lo spazio dei parametri.
+ Limitare sia il numero di iterazioni che lo spazio dei parametri.

È consigliabile eseguire la pipeline con le impostazioni per determinare il metodo più efficiente di training su un set di dati e un modello specifici.

### <a name="choosing-an-evaluation-metric"></a>Scelta di una metrica di valutazione

Al termine del test, il modello presenta un report che contiene l'accuratezza per ogni modello in modo che sia possibile esaminare i risultati della metrica:

- Un set uniforme di metriche viene utilizzato per tutti i modelli di classificazione binaria.
- L'accuratezza viene utilizzata per tutti i modelli di classificazione multiclasse.
- Per i modelli di regressione viene utilizzato un set di metriche diverso. 

Tuttavia, durante il training, è necessario scegliere una *singola* metrica da usare per classificare i modelli generati durante il processo di ottimizzazione. Potresti scoprire che la metrica migliore varia a seconda del problema aziendale e del costo di falsi positivi e falsi negativi.

#### <a name="metrics-used-for-binary-classification"></a>Metriche utilizzate per la classificazione binaria

-   **L'accuratezza** è la proporzione dei risultati reali rispetto ai casi totali.  

-   **La precisione** è la proporzione di risultati veri rispetto a risultati positivi.  

-   **Richiamo** è la frazione di tutti i risultati corretti su tutti i risultati.  

-   **F-score** è una misura che bilancia precisione e richiamo.  

-   **L'AUC** è un valore che rappresenta l'area sotto la curva quando i falsi positivi vengono tracciati sull'asse x e i veri positivi vengono tracciati sull'asse y.  

-   **Perdita media log** è la differenza tra due distribuzioni di probabilità: quella vera e quella nel modello.  

#### <a name="metrics-used-for-regression"></a>Metriche utilizzate per la regressione

-   **Media errore assoluto** calcola la media di tutti gli errori nel modello, dove *error* indica la distanza del valore stimato dal valore reale. E 'spesso abbreviato come *MAE*.  

-   **Radice di errore medio al quadrato** misura la media dei quadrati degli errori, e quindi prende la radice di quel valore. È spesso abbreviato come *RMSE*.  

-   **Errore assoluto relativo** rappresenta l'errore come percentuale del valore reale.  

-   **Errore al quadrato relativo** normalizza l'errore totale al quadrato dividendo per l'errore al quadrato totale dei valori previsti.  

-   **Coefficiente di determinazione** è un singolo numero che indica quanto bene i dati si adattano a un modello. Un valore pari a uno indica che il modello corrisponde esattamente ai dati. Un valore pari a zero indica che i dati sono casuali o in caso contrario non possono essere adattati al modello. È spesso chiamato *r<sup>2</sup>*, *R<sup>2</sup>*, o *r-quadrato*.  

### <a name="modules-that-dont-support-a-parameter-sweep"></a>Moduli che non supportano una sweep di parametri

Quasi tutti gli Allievi in Azure Machine Learning supportano la convalida incrociata con uno sweep di parametri integrato, che consente di scegliere i parametri con cui eseguire la pipeline. Se lo studente non supporta l'impostazione di un intervallo di valori, puoi comunque utilizzarlo nella convalida incrociata. In questo caso, viene selezionato un intervallo di valori consentiti per la sweep. 


## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 

