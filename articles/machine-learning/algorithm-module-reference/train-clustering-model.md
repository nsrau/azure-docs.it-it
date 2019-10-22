---
title: 'Modello di clustering di training: riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come utilizzare il modulo Train clustering Model nel servizio Azure Machine Learning per eseguire il training di modelli di clustering.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ms.openlocfilehash: fc6d889fb9be40c98e1a2a0de6fddb29939f3b5d
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693657"
---
# <a name="train-clustering-model"></a>Eseguire il training del modello di clustering

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio Azure Machine Learning.

Utilizzare questo modulo per eseguire il training di un modello di clustering.

Il modulo accetta un modello di clustering senza training che è già stato configurato usando il modulo di [clustering K-means](k-means-clustering.md) ed esegue il training del modello usando un set di dati con etichetta o senza etichetta. Il modulo consente di creare un modello sottoposto a training che è possibile utilizzare per la stima e un set di assegnazioni di cluster per ogni case nei dati di training.

> [!NOTE]
> È stato eseguito il training di un modello di clustering Impossibile usando il modulo [Train Model](train-model.md) , che è il modulo generico per il training dei modelli di machine learning. Questo perché il [modello di training](train-model.md) funziona solo con gli algoritmi di apprendimento supervisionato. K-means e altri algoritmi di clustering consentono l'apprendimento non supervisionato, ovvero l'algoritmo può apprendere da dati senza etichetta.  
  
## <a name="how-to-use-train-clustering-model"></a>Come utilizzare il modello di clustering di Train  
  
1.  Aggiungere il modulo **Train clustering Model** alla pipeline in studio. È possibile trovare il modulo in **Machine Learning Modules**, nella categoria **Train** .  
  
2. Aggiungere il modulo di [clustering K-means](k-means-clustering.md) o un altro modulo personalizzato che crea un modello di clustering compatibile e impostare i parametri del modello di clustering.  
    
3.  Alleghi un set di dati di training all'input di destra del **modello di clustering di Train**.
  
5.  In **Column set**selezionare le colonne del set di dati da utilizzare per la compilazione dei cluster. Assicurarsi di selezionare le colonne che rendono valide le funzionalità: ad esempio, evitare di usare ID o altre colonne con valori univoci o colonne con tutti gli stessi valori.

    Se è disponibile un'etichetta, è possibile usarla come funzionalità o lasciarla invariata.  
  
6. Selezionare l'opzione, **verifica l'accodamento o deseleziona solo il risultato**, se si desidera restituire i dati di training insieme all'etichetta del nuovo cluster.

    Se si deseleziona questa opzione, vengono restituite solo le assegnazioni del cluster. 

7. Eseguire la pipeline oppure fare clic sul modulo **Train clustering Model** e selezionare **Esegui selezione**.  
  
### <a name="results"></a>Risultati

Al termine del training:


+  Per visualizzare i valori nel set di dati, fare clic con il pulsante destro del mouse sul modulo, selezionare set di dati dei **risultati**e fare clic su **Visualizza**.

+ Per salvare il modello sottoposto a training per riutilizzarlo in un secondo momento, fare clic con il pulsante destro del mouse sul modulo, scegliere **modello con training**, quindi fare clic su **Salva come modello**

+ Per generare punteggi dal modello, usare [Assegna dati ai cluster](assign-data-to-clusters.md).



## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning servizio. 