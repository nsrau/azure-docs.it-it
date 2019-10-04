---
title: 'Train Model: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo **Train Model** nel servizio Azure Machine Learning per eseguire il training di un modello di classificazione o di regressione.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 08b551e766632949db350478fa8d3725906c8af8
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128439"
---
# <a name="train-model-module"></a>Modulo Train Model

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio Azure Machine Learning.

Usare questo modulo per eseguire il training di un modello di classificazione o di regressione. Il training viene eseguito dopo aver definito un modello e avere impostato i relativi parametri e richiede i dati con tag. È inoltre possibile utilizzare **Train Model** per ripetere il training di un modello esistente con nuovi dati. 

## <a name="how-the-training-process-works"></a>Funzionamento del processo di training

In Azure Machine Learning la creazione e l'uso di un modello di apprendimento automatico è in genere un processo in tre passaggi. 

1. È possibile configurare un modello, scegliendo un particolare tipo di algoritmo e definendone i parametri o gli iperparametri. Scegliere uno dei tipi di modello seguenti: 

    + Modelli di **classificazione** , basati su reti neurali, alberi delle decisioni e foreste delle decisioni e altri algoritmi.
    + Modelli di regressione, che possono includere la regressione lineare standard o che usano altri algoritmi, incluse le reti neurali e la regressione Bayes.  

2. Fornire un set di dati con etichetta e con i dati compatibili con l'algoritmo. Connettere sia i dati che il modello per eseguire il **training del modello**.

    Il training generato è un formato binario specifico, iLearner, che incapsula i modelli statistici appresi dai dati. Non è possibile modificare o leggere direttamente questo formato; Tuttavia, altri moduli possono usare questo modello sottoposto a training. 
    
    È inoltre possibile visualizzare le proprietà del modello. Per ulteriori informazioni, vedere la sezione risultati.

3. Al termine del training, usare il modello con training con uno dei [moduli di Punteggio](./score-model.md)per eseguire stime sui nuovi dati.

## <a name="how-to-use-train-model"></a>Come usare **Train Model**  
  
1.  In Azure Machine Learning configurare un modello di classificazione o un modello di regressione.
    
2. Aggiungere il modulo **Train Model** all'esperimento.  Questo modulo è reperibile nella categoria **Machine Learning** . Espandere **Train**, quindi trascinare il modulo **Train Model** nell'esperimento.
  
3.  Nell'input di sinistra allineare la modalità non trainata. Allineare il set di dati di training all'input di destra del **modello Train**.

    Il set di dati di training deve contenere una colonna di etichetta. Tutte le righe senza etichette vengono ignorate.
  
4.  Per **colonna etichetta**fare clic su **Avvia selettore colonne**e scegliere una singola colonna contenente i risultati che il modello può utilizzare per il training.
  
    - Per i problemi di classificazione, la colonna Label deve contenere valori categorici o valori **discreti** . Alcuni esempi potrebbero essere una classificazione Sì/No, un nome o un codice di classificazione di malattia oppure un gruppo di reddito.  Se si seleziona una colonna non categorica, il modulo restituirà un errore durante il training.
  
    -   Per i problemi di regressione, la colonna Label deve contenere dati numerici che rappresentano la variabile di risposta. Idealmente, i dati numerici rappresentano una scala continua. 
    
    Esempi possono essere un punteggio di rischio di credito, il tempo previsto per l'errore per un disco rigido o il numero previsto di chiamate a un Call Center in un giorno o in un momento specifico.  Se non si sceglie una colonna numerica, è possibile che venga ricevuto un errore.
  
    -   Se non si specifica quale colonna di etichetta usare, Azure Machine Learning tenterà di dedurre la colonna di etichetta appropriata, usando i metadati del set di dati. Se seleziona la colonna errata, usare il selettore di colonna per correggerla.
  
    > [!TIP] 
    > In caso di problemi con il selettore di colonna, vedere l'articolo [selezionare le colonne nel set di dati](./select-columns-in-dataset.md) per i suggerimenti. Vengono descritti alcuni scenari comuni e suggerimenti per l'utilizzo delle opzioni **with Rules** e **by Name** .
  
5.  Eseguire l'esperimento. Se si dispone di una grande quantità di dati, l'operazione può richiedere alcuni minuti.

## <a name="bkmk_results"></a>Risultati

Dopo il training del modello:

+ Per visualizzare i parametri del modello e i pesi delle funzionalità, fare clic con il pulsante destro del mouse sull'output e selezionare **Visualizza**.
+ Per utilizzare il modello in altri esperimenti, fare clic con il pulsante destro del mouse sul modello e selezionare **Salva modello**. Digitare un nome per il modello. 

    Questo consente di salvare il modello come snapshot non aggiornato da esecuzioni ripetute dell'esperimento.
+ Per usare il modello per stimare nuovi valori, connetterlo al modulo [Score Model](./score-model.md) , insieme ai nuovi dati di input.


## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning servizio. 