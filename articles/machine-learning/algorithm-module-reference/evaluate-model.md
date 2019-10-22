---
title: 'Modello di valutazione: riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Evaluate Model nel servizio Azure Machine Learning per misurare l'accuratezza di un modello sottoposto a training.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ms.openlocfilehash: 0ad4ceedf9c1d65339c9e4aabebc0a47475ed568
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693811"
---
# <a name="evaluate-model-module"></a>Modulo Evaluate Model

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio Azure Machine Learning.

Utilizzare questo modulo per misurare l'accuratezza di un modello sottoposto a training. Si fornisce un set di dati contenente i punteggi generati da un modello e il modulo **Evaluate Model** calcola un set di metriche di valutazione standard del settore.
  
 Le metriche restituite dal **modello di valutazione** dipendono dal tipo di modello che si sta valutando:  
  
-   **Modelli di classificazione**    
-   **Modelli di regressione**    



> [!TIP]
> Se non si ha familiarità con la valutazione del modello, si consiglia la serie di video di Dr. Stephen Elston come parte del [corso di apprendimento automatico](https://blogs.technet.microsoft.com/machinelearning/2015/09/08/new-edx-course-data-science-machine-learning-essentials/) di EDX. 


Esistono tre modi per usare il modulo **Evaluate Model** :

+ Genera punteggi sui dati di training e valuta il modello in base a questi punteggi
+ Genera punteggi sul modello, ma confronta i punteggi con i punteggi in un set di testing riservato
+ Confrontare i punteggi per due modelli diversi, ma correlati, usando lo stesso set di dati

## <a name="use-the-training-data"></a>Usare i dati di training

Per valutare un modello, è necessario connettere un set di dati che contiene un set di colonne di input e punteggi.  Se non sono disponibili altri dati, è possibile usare il set di dati originale.

1. Connettere l'output del **set di dati con punteggio** del modello di [Punteggio](./score-model.md) all'input di **Evaluate Model**. 
2. Fare clic su **Evaluate Model** Module ed eseguire la pipeline per generare i punteggi di valutazione.

## <a name="use-testing-data"></a>Usare i dati di test

Uno scenario comune nell'apprendimento automatico consiste nel separare il set di dati originale in set di dati di training e di testing, usando il modulo [Split](./split-data.md) o il modulo [Partition and Sample](./partition-and-sample.md) . 

1. Connettere l'output del **set di dati con punteggio** del modello di [Punteggio](score-model.md) all'input di **Evaluate Model**. 
2. Connettere l'output del modulo Split data che contiene i dati di test all'input di destra di **Evaluate Model**.
2. Fare clic su modulo **Evaluate Model** e selezionare **Esegui selezione** per generare i punteggi di valutazione.

## <a name="compare-scores-from-two-models"></a>Confrontare i punteggi di due modelli

È anche possibile connettere un secondo set di punteggi per **valutare il modello**.  I punteggi potrebbero essere un set di valutazione condiviso con risultati noti o un set di risultati di un modello diverso per gli stessi dati.

Questa funzionalità è utile perché è possibile confrontare facilmente i risultati di due modelli diversi sugli stessi dati. In alternativa, è possibile confrontare i punteggi di due esecuzioni diverse sugli stessi dati con parametri diversi.

1. Connettere l'output del **set di dati con punteggio** del modello di [Punteggio](score-model.md) all'input di **Evaluate Model**. 
2. Connettere l'output del modulo Score Model per il secondo modello all'input di destra di **Evaluate Model**.
3. Fare clic con il pulsante destro del mouse su **Evaluate Model**e selezionare **Esegui selezione** per generare i punteggi di valutazione.

## <a name="results"></a>Risultati

Dopo aver eseguito **Evaluate Model**, fare clic con il pulsante destro del mouse sul modulo e selezionare **Risultati valutazione** per visualizzare i risultati. Puoi:

+ Salvare i risultati come set di dati, per semplificare l'analisi con altri strumenti
+ Generare una visualizzazione nell'interfaccia

Se si connettono i set di dati a entrambi gli input di **Evaluate Model**, i risultati conterranno le metriche per entrambi i set di dati o per entrambi i modelli.
Il modello o i dati collegati alla porta sinistra vengono presentati per primi nel report, seguiti dalle metriche per il set di dati o dal modello collegato sulla porta destra.  

Ad esempio, l'immagine seguente rappresenta un confronto dei risultati di due modelli di clustering basati sugli stessi dati, ma con parametri diversi.  

![Comparing2Models&#95;AML](media/module/aml-comparing2models.png "AML_Comparing2Models")  

Poiché si tratta di un modello di clustering, i risultati della valutazione sono diversi rispetto a quelli di due modelli di regressione oppure a confrontati due modelli di classificazione. Tuttavia, la presentazione complessiva è la stessa. 

## <a name="metrics"></a>Metriche

In questa sezione vengono descritte le metriche restituite per i tipi specifici di modelli supportati per l'utilizzo con **Evaluate Model**:

+ [modelli di classificazione](#bkmk_classification)
+ [modelli di regressione](#bkmk_regression)

###  <a name="bkmk_classification"></a>Metriche per i modelli di classificazione

Quando si valutano i modelli di classificazione, vengono restituite le metriche seguenti. Se si confrontano i modelli, vengono classificati in base alla metrica selezionata per la valutazione.  
  
-   L' **accuratezza** misura la bontà di un modello di classificazione come la percentuale di risultati reali al numero totale di case.  
  
-   La **precisione** è la percentuale di risultati reali rispetto a tutti i risultati positivi.  
  
-   **Richiama** è la frazione di tutti i risultati corretti restituiti dal modello.  
  
-   Il **Punteggio f** viene calcolato come media ponderata della precisione e richiama tra 0 e 1, dove il valore f-Score ideale è 1.  
  
-   **AUC** misura l'area sotto la curva tracciata con i veri positivi sull'asse y e i falsi positivi sull'asse x. Questa metrica è utile perché fornisce un singolo numero che consente di confrontare modelli di tipi diversi.  
  
- La **perdita di log media** è un singolo punteggio usato per esprimere la penalità per i risultati errati. Viene calcolata come differenza tra due distribuzioni di probabilità, quella vera e quella nel modello.  
  
- La **perdita dei log di training** è un singolo punteggio che rappresenta il vantaggio del classificatore su una stima casuale. La perdita di log misura l'incertezza del modello confrontando le probabilità che restituisce ai valori noti (verità) nelle etichette. Si desidera ridurre al minimo la perdita di log per il modello nel suo complesso.

##  <a name="bkmk_regression"></a>Metriche per i modelli di regressione
 
Le metriche restituite per i modelli di regressione sono in genere progettate per stimare la quantità di errori.  Un modello viene considerato adatto ai dati anche se la differenza tra i valori osservati e quelli stimati è ridotta. Tuttavia, l'analisi del modello dei residui (la differenza tra un punto stimato e il valore effettivo corrispondente) può indicare una grande quantità di potenziali distorsioni nel modello.  
  
 Per la valutazione dei modelli di regressione vengono restituite le metriche seguenti. Quando si confrontano i modelli, vengono classificati in base alla metrica selezionata per la valutazione.  
  
- **Errore assoluto medio (Mae)** consente di misurare la distanza tra le stime e i risultati effettivi; un punteggio più basso è quindi migliore.  
  
- **Radice errore quadratico medio (valori RMSE)** crea un singolo valore che riepiloga l'errore nel modello. Grazie alla quadratura della differenza, la metrica ignora la differenza tra la stima e la stima.  
  
- **Errore assoluto relativo (Rae)** è la differenza assoluta relativa tra i valori previsti ed effettivi; relativa perché la differenza media è divisa per la media aritmetica.  
  
- L'errore **quadratico relativo (RSE)** normalizza in modo analogo l'errore quadrato totale dei valori stimati dividendo per l'errore quadrato totale dei valori effettivi.  
  
- **Media zero un errore (MZOE)** indica se la stima è stata corretta o meno.  In altre parole: `ZeroOneLoss(x,y) = 1` quando `x!=y`; in caso contrario `0`.
  
- Il **coefficiente di determinazione**, spesso definito R<sup>2</sup>, rappresenta la potenza predittiva del modello come valore compreso tra 0 e 1. Zero indica che il modello è casuale (non spiega niente); 1 indica una soluzione perfetta. Tuttavia, è consigliabile usare l'attenzione per interpretare i valori di R<sup>2</sup> , in quanto i valori bassi possono essere completamente normali e i valori elevati possono essere sospetti.
  

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning servizio. 