---
title: 'Eseguire il training modello di Clustering: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Train Clustering Model nel servizio Azure Machine Learning per il training di modelli di clustering.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 41cdec1d7f1c3932b17da6f9b1de518071f3f542
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65028081"
---
# <a name="train-clustering-model"></a>Eseguire il training del modello di clustering

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio di Azure Machine Learning.

Usare questo modulo per eseguire il training di un modello di clustering.

Il modulo accetta un modello di clustering senza training che già stato configurato usando le [Clustering K-Means](k-means-clustering.md) modulo ed esegue il training modello utilizzando un set di dati senza etichetta o etichettato. Il modulo Crea sia un modello con training che è possibile usare per la stima e un set di assegnazioni del cluster per ogni case nei dati di training.

> [!NOTE]
> Un cluster non di modello possono essere sottoposti a training usando il [Train Model](train-model.md) modulo, che è il modulo generico per il training di modelli di machine learning. Infatti [Train Model](train-model.md) funziona solo con gli algoritmi di apprendimento supervisionato. K-means e altri algoritmi di clustering consentano un apprendimento non supervisionato, vale a dire che l'algoritmo può apprendere dai dati senza etichetta.  
  
## <a name="how-to-use-train-clustering-model"></a>Come usare Train Clustering Model  
  
1.  Aggiungere il **Train Clustering Model** modulo nell'esperimento in Studio. È possibile trovare il modulo sotto **moduli di Machine Learning**, nella **Train** categoria.  
  
2. Aggiungere il [Clustering K-Means](k-means-clustering.md) modulo o un altro modulo personalizzato che crea un servizio compatibile con cluster del modello e impostare i parametri del modello di clustering.  
    
3.  Collegare un set di dati di training per l'input di destra **Train Clustering Model**.
  
5.  Nelle **Set di colonne**, selezionare le colonne dal set di dati da utilizzare nella creazione di cluster. Assicurarsi di selezionare le colonne che rendono le funzionalità di buona: ad esempio, evitare di usare gli ID o altre colonne che contengono valori univoci o le colonne che presentano gli stessi valori.

    Se un'etichetta è disponibile, è possibile usarla come una funzionalità o ometterlo.  
  
6. Selezionare l'opzione **controllare che tipo solo accodamenti o deselezionare per solo risultato**, se si desidera restituire i dati di training con la nuova etichetta di cluster.

    Se si deseleziona questa opzione, solo le assegnazioni del cluster vengono visualizzati. 

7. Eseguire l'esperimento oppure scegliere il **Train Clustering Model** modulo e selezionare **Esegui selezione**.  
  
### <a name="results"></a>Risultati

Al termine di training:


+  Per visualizzare i valori nel set di dati, il pulsante destro del modulo, selezionare **generare set di dati**, fare clic su **Visualize**.

+ Per salvare il modello con training per un riutilizzo successivo, il pulsante destro del modulo, selezionare **Trained model**, fare clic su **Salva come modello sottoposto a training**.

+ Per generare punteggi dal modello, usare [assegna dati ai cluster](assign-data-to-clusters.md).



## <a name="next-steps"></a>Passaggi successivi

Vedere le [set di moduli disponibili](module-reference.md) al servizio Azure Machine Learning. 