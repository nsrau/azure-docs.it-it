---
title: 'Assegna i dati ai Cluster: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare i dati di assegnare al modulo di Cluster nel servizio Azure Machine Learning per calcolare il punteggio del modello di clustering.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: b370c6ef5be311ed9c8df2737fa1b01144d61011
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028771"
---
# <a name="assign-data-to-clusters"></a>Assegna i dati ai cluster

Questo articolo descrive come usare il [assegna dati ai cluster](assign-data-to-clusters.md) modulo nell'interfaccia visiva, per generare stime usando un modello di clustering che è stato eseguito il training usando l'algoritmo di clustering K-Means.

Il modulo restituisce un set di dati che contiene le assegnazioni di probabile per ogni nuovo punto dati. 


## <a name="how-to-use-assign-data-to-clusters"></a>Come usare i dati di assegnare ai cluster
  
1.  Nell'interfaccia visiva, individuare un modello di clustering con Training in precedenza. È possibile creare e addestrare un modello di clustering utilizzando uno di questi metodi:  
  
    - Configurare l'algoritmo K-means usando il [Clustering K-Means](k-means-clustering.md) modulo e quindi eseguire il training del modello usando un set di dati e il [Train Clustering Model](train-clustering-model.md) modulo.  
  
  
    È anche possibile aggiungere un modello di clustering con training esistente dal **modelli salvati** gruppo nell'area di lavoro.

2. Collegare il modello con training per la porta di input sinistra del [assegna dati ai cluster](assign-data-to-clusters.md).  

3. Collegare un nuovo set di dati come input. In questo set di dati, le etichette sono facoltative. In genere, il clustering è un metodo di apprendimento non supervisionato, in modo che non è previsto che si saprà categorie in anticipo.

    Tuttavia, le colonne di input devono essere quello utilizzato per le colonne che sono state usate nel training che del modello di clustering o di errore.

    > [!TIP]
    > Per ridurre il numero di colonne di output da stime di cluster, usare [Select Columns in Dataset](select-columns-in-dataset.md)e selezionare un subset delle colonne. 
    
4. Lasciare l'opzione **controllare che tipo solo accodamenti o deselezionare per solo risultato** selezionata se si desidera che i risultati contengono input set di dati completo, insieme a una colonna che indica i risultati (assegnazioni cluster).
  
    Se si deseleziona questa opzione, si ottengano solo i risultati. Ciò potrebbe essere utile durante la creazione di stime come parte di un servizio web.
  
5.  Eseguire l'esperimento.  
  
### <a name="results"></a>Risultati

 
+  Per visualizzare i valori nel set di dati, il pulsante destro del modulo, selezionare **generare set di dati**, fare clic su **Visualize**.

