---
title: 'Valutazione del modello: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Evaluate Model nel servizio Azure Machine Learning per misurare l'accuratezza di un modello con training.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 40a8247c22da1f7a057e222565ffb2ec4c6b7fb3
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028741"
---
# <a name="evaluate-model-module"></a>Valutare il modulo di modello

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio di Azure Machine Learning.

Usare questo modulo per misurare l'accuratezza di un modello con training. Si forniscono un set di dati contenente i punteggi generati da un modello e il **Evaluate Model** modulo Calcola un set di metriche di valutazione standard del settore.
  
 Le metriche restituite da **Evaluate Model** variano a seconda del tipo di modello che si sta valutando:  
  
-   **Modelli di classificazione**    
-   **Modelli di regressione**    



> [!TIP]
> Se ha esperienza di valutazione del modello, è consigliabile la serie di video dal ripristino di emergenza. Stephen Elston, durante la [corso di machine learning](https://blogs.technet.microsoft.com/machinelearning/2015/09/08/new-edx-course-data-science-machine-learning-essentials/) da EdX. 


Esistono tre modi per usare la **Evaluate Model** modulo:

+ Generare i punteggi sui dati di training e valutare il modello basato su questi punteggi
+ Generare punteggi per il modello, ma confrontare i punteggi di punteggi in un set di testing riservato
+ Confrontare i punteggi per due modelli di diversi ma correlati, usando lo stesso set di dati

## <a name="use-the-training-data"></a>Usare i dati di training

Per valutare un modello, è necessario connettere un set di dati che contiene un set di colonne di input e punteggi.  Se nessun altro dato è disponibile, è possibile usare il set di dati originale.

1. Connettere il **Scored dataset** di output delle [Score Model](./score-model.md) all'input di **Evaluate Model**. 
2. Fare clic su **Evaluate Model** modulo ed eseguire l'esperimento per generare i punteggi di valutazione.

## <a name="use-testing-data"></a>Usare i dati di testing

Uno scenario comune di apprendimento automatico consiste nel separare il set di dati originale in training e testing di set di dati, tramite il [suddivisione](./split-data.md) modulo, o il [Partition and Sample](./partition-and-sample.md) modulo. 

1. Connettere il **Scored dataset** di output delle [Score Model](score-model.md) all'input di **Evaluate Model**. 
2. Connettere l'output del modulo Split Data che contiene i dati di test per l'input di destra **Evaluate Model**.
2. Fare clic su **Evaluate Model** modulo e selezionare **Esegui selezionati** per generare i punteggi di valutazione.

## <a name="compare-scores-from-two-models"></a>Confrontare i punteggi di due modelli

È anche possibile collegare un secondo set di punteggi a **Evaluate Model**.  I punteggi potrebbero essere un set di valutazione condivisa presenta noti i risultati o un set di risultati da un modello diverso per gli stessi dati.

Questa funzionalità è utile perché è possibile confrontare facilmente i risultati di due modelli diversi sugli stessi dati. In alternativa, è possibile confrontare i punteggi di due esecuzioni diverse sugli stessi dati con parametri diversi.

1. Connettere il **Scored dataset** di output delle [Score Model](score-model.md) all'input di **Evaluate Model**. 
2. Connettere l'output del modulo Score Model per il secondo modello all'input di destra **Evaluate Model**.
3. Fare doppio clic su **Evaluate Model**e selezionare **Esegui selezionati** per generare i punteggi di valutazione.

## <a name="results"></a>Risultati

Dopo aver eseguito **Evaluate Model**, fare clic sul modulo e selezionare **i risultati della valutazione** per visualizzare i risultati. È possibile:

+ Salvare i risultati come set di dati, per semplificarne l'analisi con altri strumenti
+ Generare una visualizzazione nell'interfaccia

Se i set di dati si connette a entrambi gli input della **Evaluate Model**, i risultati conterrà le metriche per entrambi i set di dati, o entrambi i modelli.
Il modello o i dati allegati alla porta di sinistra viene visualizzati prima di tutto nel report, aggiungendo le metriche per il set di dati o nel modello collegato nella porta di destra.  

Ad esempio, l'immagine seguente rappresenta un confronto dei risultati di due modelli di clustering che sono stato sviluppati sugli stessi dati, ma con parametri diversi.  

![AML&#95;Comparing2Models](media/module/aml-comparing2models.png "AML_Comparing2Models")  

Poiché si tratta di un modello di clustering, i risultati della valutazione sono diversi rispetto a se confrontare i punteggi di due modelli di regressione o confrontare due modelli di classificazione. Tuttavia, la presentazione generale è lo stesso. 

## <a name="metrics"></a>Metriche

In questa sezione illustra le metriche restituite per i tipi specifici di modelli supportati per l'uso con **Evaluate Model**:

+ [modelli di classificazione](#bkmk_classification)
+ [modelli di regressione](#bkmk_regression)

###  <a name="bkmk_classification"></a> Metriche per i modelli di classificazione

Le metriche seguenti vengono segnalate durante la valutazione di modelli di classificazione. Se si confrontano modelli, essi vengono classificate in base alla metrica che si seleziona per la valutazione.  
  
-   **Accuratezza** misura l'adeguatezza del modello di classificazione come la percentuale di risultati totale dei case.  
  
-   **Precisione** è la percentuale dei risultati true su tutti i risultati positivi.  
  
-   **È importante ricordare** indica la frazione di tutti i risultati corretti restituiti dal modello.  
  
-   **F-score** viene calcolato come media ponderata di precisione e richiamo compreso tra 0 e 1, dove il valore F-score ideale è 1.  
  
-   **Area sottesa alla curva** misure l'area sottesa alla curva tracciata con veri positivi su di y dell'asse e false positivi sull'asse x. Questa metrica è utile perché offre un singolo numero che consente di confrontare i modelli di tipi diversi.  
  
- **Perdita logaritmica Media** corrisponde a un punteggio singolo usato per esprimere la penalità per i risultati errati. Viene calcolato come la differenza tra due distribuzioni di probabilità: quella reale e quella nel modello.  
  
- **Perdita logaritmica di training** corrisponde a un singolo punteggio che rappresenta il vantaggio del classificatore su una stima casuale. La perdita logaritmica misura l'incertezza del modello tramite il confronto tra le probabilità che restituisce come output per i valori noti (ground truth) nelle etichette. Si vuole ridurre al minimo la perdita di log per il modello nel suo complesso.

##  <a name="bkmk_regression"></a> Metriche per i modelli di regressione
 
Le metriche restituite per i modelli di regressione sono in genere progettate per stimare la quantità di errori.  Un modello viene considerato come adattare i dati anche se la differenza tra i valori osservati e previsti è ridotto. Tuttavia, osservando il modello dei residui (la differenza tra un determinato stimato e il valore effettivo corrispondente) può fornire informazioni molto su potenziali distorsione del modello.  
  
 Le metriche seguenti vengono indicate per la valutazione dei modelli di regressione. Quando si confrontano modelli, essi vengono classificate in base alla metrica che si seleziona per la valutazione.  
  
- **Errore assoluto medio (MAE)** misura la prossimità le stime sono per i risultati effettivi; pertanto, è preferibile un punteggio basso.  
  
- **Radice errore quadratico medio (RMSE) significa che** crea un valore singolo che riepiloga l'errore nel modello. Dall'elevazione al quadrato la differenza, la metrica non considera la differenza tra stima in eccesso e correggerà stima.  
  
- **Errore assoluto relativo (RAE)** è la relativa differenza assoluta tra i valori previsti ed effettivi; relativo perché la differenza media viene diviso per la media aritmetica.  
  
- **Quadratico relativo (RSE) errore** Analogamente Normalizza l'errore quadratico totale dei valori stimati proporzionalmente dividendoli per l'errore quadratico totale dei valori effettivi.  
  
- **Significa Zero un errore (MZOE)** indica se è stata corretta la stima o meno.  In altre parole: `ZeroOneLoss(x,y) = 1` quando si `x!=y`; in caso contrario `0`.
  
- **Coefficiente di determinazione**, spesso detta R<sup>2</sup>, rappresenta la capacità predittiva del modello come un valore compreso tra 0 e 1. Zero indica che il modello è casuale (spiega nothing); 1 indica che è ideale. Tuttavia, prestare attenzione nell'interpretazione R<sup>2</sup> valori, come valori bassi possono essere interamente normali e i valori elevati possono essere sospetto.
  

## <a name="next-steps"></a>Passaggi successivi

Vedere le [set di moduli disponibili](module-reference.md) al servizio Azure Machine Learning. 