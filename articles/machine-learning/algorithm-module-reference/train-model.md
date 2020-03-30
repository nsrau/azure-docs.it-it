---
title: 'Train Model: Riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo **Train Model** in Azure Machine Learning per eseguire il training di un modello di classificazione o regressione.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 0a9728e05aee27e74054a77e2c9be7dc08968207
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455860"
---
# <a name="train-model-module"></a>Modulo Train Model

Questo articolo descrive un modulo nella finestra di progettazione di Azure Machine Learning (anteprima).

Utilizzare questo modulo per eseguire il training di un modello di classificazione o regressione. Il training viene eseguito dopo aver definito un modello e averne impostato i parametri e aver richiesto dati con tag. È inoltre possibile utilizzare **il modello** di training per eseguire nuovamente il training di un modello esistente con nuovi dati. 

## <a name="how-the-training-process-works"></a>Come funziona il processo di formazione

In Azure Machine Learning la creazione e l'uso di un modello di apprendimento automatico è in genere un processo in tre passaggi. 

1. È possibile configurare un modello scegliendo un particolare tipo di algoritmo e definendone i parametri o gli iperparametri. Scegliere uno dei seguenti tipi di modello: 

    + **Modelli di classificazione,** basati su reti neurali, alberi delle decisioni e foreste decisionali e altri algoritmi.
    + Modelli di **regressione,** che possono includere la regressione lineare standard o che utilizzano altri algoritmi, incluse le reti neurali e la regressione bayesiana.  

2. Fornire un set di dati etichettato e con dati compatibili con l'algoritmo. Collegare sia i dati che il modello al modello di **training**.

    Ciò che la formazione produce è un formato binario specifico, l'iLearner, che incapsula i modelli statistici appresi dai dati. Non è possibile modificare o leggere direttamente questo formato; Tuttavia, altri moduli possono utilizzare questo modello sottoposto a training. 
    
    È inoltre possibile visualizzare le proprietà del modello. Per altre informazioni, vedere la sezione Risultati.For more information, see the Results section.

3. Al termine del training, usare il modello sottoposto a training con uno dei moduli di [punteggio](./score-model.md)per eseguire stime sui nuovi dati.

## <a name="how-to-use-train-model"></a>Come usare il modello di training 
  
1.  In Azure Machine Learning configurare un modello di classificazione o un modello di regressione.
    
2. Aggiungere il modulo **Train Model** alla pipeline.  È possibile trovare questo modulo nella categoria **Machine Learning.You can** find this module under the Machine Learning category. Espandere **Treno**, quindi trascinare il modulo **Train Model** nella pipeline.
  
3.  Sull'ingresso sinistro, collegare la modalità non addestrata. Collegare il set di dati di training all'input a destra di **Train Model**.

    Il set di dati di training deve contenere una colonna di etichette. Tutte le righe senza etichette vengono ignorate.
  
4.  Per **la colonna Etichetta**, fare clic su Modifica **colonna** nel riquadro destro del modulo e scegliere una singola colonna contenente i risultati che il modello può utilizzare per il training.
  
    - Per i problemi di classificazione, la colonna label deve contenere valori **di categoria** o valori **discreti.** Alcuni esempi potrebbero essere una classificazione sì/no, un codice o un nome di classificazione delle malattie o un gruppo di reddito.  Se si sceglie una colonna non categorica, il modulo restituirà un errore durante il training.
  
    -   Per i problemi di regressione, la colonna label deve contenere dati **numerici** che rappresentano la variabile di risposta. Idealmente i dati numerici rappresentano una scala continua. 
    
    Alcuni esempi possono essere il punteggio di rischio di credito, il tempo previsto per il fallimento di un disco rigido o il numero previsto di chiamate a un call center in un determinato giorno o ora.  Se non si sceglie una colonna numerica, è possibile che venga visualizzato un errore.
  
    -   Se non si specifica la colonna di etichetta da usare, Azure Machine Learning tenterà di dedurre la colonna di etichetta appropriata usando i metadati del set di dati. Se seleziona la colonna errata, utilizzare il selettore di colonna per correggerla.
  
    > [!TIP] 
    > In caso di problemi nell'uso del selettore di colonna, vedere l'articolo [Selezione di colonne nel set di dati](./select-columns-in-dataset.md) per suggerimenti. Vengono descritti alcuni scenari comuni e suggerimenti per l'utilizzo delle opzioni **WITH RULES** e **BY NAME.**
  
5.  Inviare la pipeline. Se si dispone di molti dati, questa operazione può richiedere un po' di tempo.

## <a name="results"></a><a name="bkmk_results"></a> Risultati

Dopo il training del modello:


+ Per usare il modello in altre pipeline, selezionare il modulo e selezionare l'icona **Registra set di dati** nella scheda **Output** nel riquadro di destra. È possibile accedere **ai**modelli salvati nella tavolozza dei moduli in Set di dati .

+ Per usare il modello nella stima di nuovi valori, collegarlo al modulo Modello di [punteggio,](./score-model.md) insieme ai nuovi dati di input.


## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 