---
title: 'Eseguire il training del modello: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il **Train Model** modulo nel servizio di Azure Machine Learning per addestrare un modello di classificazione o regressione.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 29d0f698456b83c1520a92bc7df47b26540325f4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65028111"
---
# <a name="train-model-module"></a>Modulo Train Model

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio di Azure Machine Learning.

Usare questo modulo per eseguire il training di un modello di classificazione o regressione. Corsi di formazione viene eseguita dopo avere definito un modello e impostare i relativi parametri e richiede i dati con tag. È anche possibile usare **Train Model** per ripetere il training di un modello esistente con nuovi dati. 

## <a name="how-the-training-process-works"></a>Come funziona il processo di training

In Azure Machine Learning, creazione e utilizzo di un modello di machine learning è in genere un processo in tre fasi. 

1. Si configura un modello, scegliendo un particolare tipo di algoritmo e la definizione di parametri o gli iperparametri. Scegliere uno qualsiasi dei tipi di modello seguente: 

    + **Classificazione** modelli, basati su reti neurali, alberi delle decisioni e foreste delle decisioni e altri algoritmi.
    + **Regressione** modelli, che può includere la regressione lineare standard, o che usano altri algoritmi, tra cui reti neurali e regressione Bayesiana.  

2. Fornire un set di dati è denominata e contiene dati compatibili con l'algoritmo. Connettere i dati sia nel modello per **Train Model**.

    Produce il training è un formato binario specifico di iLearner, che incapsula i modelli statistici apprese dai dati. Non è possibile direttamente modificare o leggere tale formato; Tuttavia, altri moduli è possono usare questo modello con training. 
    
    È anche possibile visualizzare le proprietà del modello. Per altre informazioni, vedere la sezione relativa ai risultati.

3. Dopo aver completato il training, usare il modello con Training con una delle [moduli di assegnazione dei punteggi](./score-model.md), per eseguire stime sui nuovi dati.

## <a name="how-to-use-train-model"></a>Come usare **Train Model**  
  
1.  In Azure Machine Learning, configurare un modello di classificazione o regressione.
    
2. Aggiungere il **Train Model** modulo all'esperimento.  È possibile trovare questo modulo sotto il **Machine Learning** categoria. Espandere **Train**, quindi trascinare il **Train Model** modulo nell'esperimento.
  
3.  L'input a sinistra, allegare la modalità non sottoposto a training. Collegare il set di dati di training per l'input di destra **Train Model**.

    Il set di dati di training deve contenere una colonna di etichetta. Tutte le righe senza le etichette vengono ignorate.
  
4.  Per la **colonna etichetta**, fare clic su **Avvia selettore di colonna**e scegliere una singola colonna che contiene i risultati possa usare il modello per il training.
  
    - Per problemi di classificazione, la colonna di etichetta deve contenere **categoriche** valori oppure **discreti** valori. Alcuni esempi potrebbero essere una classificazione di sì/no, un codice di classificazione di malattia o nome o un gruppo di reddito.  Se si seleziona una colonna non categorici, il modulo restituirà un errore durante il training.
  
    -   Per problemi di regressione, la colonna di etichetta deve contenere **numerico** dati che rappresenta la variabile di risposta. I dati numerici in una situazione ideale rappresentano una scala continua. 
    
    Esempi possono essere un punteggio di rischio di credito, il tempo previsto di errore per un disco rigido o il numero previsto di chiamate a un call center in un determinato giorno oppure ora.  Se non si sceglie una colonna numerica, si potrebbe ottenere un errore.
  
    -   Se non si specifica la colonna di etichetta da usare, Azure Machine Learning tenterà di dedurre la colonna di etichetta appropriata usando i metadati del set di dati. Se sceglie la colonna non corretta, usare il selettore di colonna per correggere l'errore.
  
    > [!TIP] 
    > Se si riscontrano problemi con il selettore di colonna, vedere l'articolo [Select Columns in Dataset](./select-columns-in-dataset.md) per suggerimenti. Vengono descritti alcuni scenari comuni e suggerimenti per l'uso di **con regole** e **BY NAME** opzioni.
  
5.  Eseguire l'esperimento. Se si dispone di una grande quantità di dati, l'operazione può richiedere un po' di tempo.

## <a name="bkmk_results"></a> Risultati

Dopo il modello viene eseguito il training:

+ Per visualizzare i parametri di modello e pesi delle funzionalità, fare doppio clic su di output e selezionare **Visualize**.
+ Per usare il modello in altri esperimenti, il modello e scegliere **salvataggio modello**. Digitare un nome per il modello. 

    Ciò consente di salvare il modello come uno snapshot che non viene aggiornato tramite esecuzioni ripetute dell'esperimento.
+ Per usare il modello nella stima di nuovi valori, connetterla al [Score Model](./score-model.md) modulo, insieme ai nuovi dati di input.


## <a name="next-steps"></a>Passaggi successivi

Vedere le [set di moduli disponibili](module-reference.md) al servizio Azure Machine Learning. 