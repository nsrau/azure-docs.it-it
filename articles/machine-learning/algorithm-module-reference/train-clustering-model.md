---
title: 'Train Clustering Model: Riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Train Clustering Model in Azure Machine Learning per eseguire il training dei modelli di clustering.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: c29baf3cdda998a2ab78c84f3311b84d37086bcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477426"
---
# <a name="train-clustering-model"></a>Eseguire il training del modello di clustering

Questo articolo descrive un modulo nella finestra di progettazione di Azure Machine Learning (anteprima).

Usare questo modulo per eseguire il training di un modello di clustering.

Il modulo accetta un modello di clustering non sottoposto a training già configurato utilizzando il modulo [K-Means Clustering](k-means-clustering.md) ed esegue il training del modello utilizzando un set di dati con etichetta o senza etichetta. Il modulo crea sia un modello sottoposto a training che è possibile usare per la stima e un set di assegnazioni del cluster per ogni caso nei dati di training.

> [!NOTE]
> Non è possibile eseguire il training di un modello di clustering usando il modulo [Train Model,](train-model.md) che è il modulo generico per il training dei modelli di apprendimento automatico. Questo perché [Train Model](train-model.md) funziona solo con algoritmi di apprendimento supervisionati. I K-mezzi e altri algoritmi di clustering consentono l'apprendimento senza supervisione, il che significa che l'algoritmo può imparare da dati senza etichetta.  
  
## <a name="how-to-use-train-clustering-model"></a>Come usare Train Clustering Model  

1.  Aggiungere il modulo **Train Clustering Model** alla pipeline nella finestra di progettazione. È possibile trovare il modulo in **Moduli di Machine Learning**, nella categoria **Treno.**  
  
2. Aggiungere il modulo [K-Means Clustering](k-means-clustering.md) o un altro modulo personalizzato che crea un modello di clustering compatibile e impostare i parametri del modello di clustering.  
    
3.  Collegare un set di dati di training all'input a destra di **Train Clustering Model**.
  
5.  In **Set di**colonne selezionare le colonne del set di dati da utilizzare nella creazione di cluster. Assicurarsi di selezionare le colonne che presentano buone caratteristiche: ad esempio, evitare di usare ID o altre colonne con valori univoci o colonne con tutti gli stessi valori.

    Se è disponibile un'etichetta, è possibile utilizzarla come funzione o lasciarla fuori.  
  
6. Selezionare l'opzione Controlla se si **aggiunge o deseleziona solo il risultato**se si desidera generare i dati di training insieme alla nuova etichetta del cluster.

    Se si deseleziona questa opzione, vengono restituite solo le assegnazioni del cluster. 

7. Inviare la pipeline oppure fare clic sul modulo **Train Clustering Model** e selezionare **Esegui selezionati**.  
  
### <a name="results"></a>Risultati

Dopo che l'allenamento è stato completato:

+ Per salvare un'istantanea del modello sottoposto a training, selezionare la scheda **Uscite** nel pannello destro del modulo **Modulo del modello.** Selezionare l'icona **Registra set di dati** per salvare il modello come modulo riutilizzabile.

+ Per generare punteggi dal modello, utilizzare [Assegna dati ai cluster](assign-data-to-clusters.md).

## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 