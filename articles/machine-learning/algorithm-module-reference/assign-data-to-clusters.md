---
title: 'Assegnare i dati al cluster: riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo assign data to cluster nel servizio Azure Machine Learning per assegnare punteggi al modello di clustering.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ms.openlocfilehash: 3caf568590beb5b1f9d346d472c93f134a4f3a5f
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693221"
---
# <a name="module-assign-data-to-clusters"></a>Modulo: assegnare i dati ai cluster

Questo articolo descrive come usare il modulo *assign data to Clusters* nell'interfaccia visiva Azure Machine Learning. Il modulo genera stime tramite un modello di clustering di cui è stato eseguito il training con l'algoritmo *K-means clustering* .

Il modulo assign data to Clusters restituisce un set di dati che contiene le assegnazioni probabili per ogni nuovo punto dati. 


## <a name="how-to-use-assign-data-to-clusters"></a>Come usare assegna dati ai cluster
  
1. Nell'interfaccia visiva Azure Machine Learning individuare un modello di clustering precedentemente sottoposto a training. È possibile creare ed eseguire il training di un modello di clustering utilizzando uno dei metodi seguenti:  
  
    - Configurare l'algoritmo di clustering K-means usando il modulo di [clustering k-means](k-means-clustering.md) ed eseguire il training del modello usando un set di dati e il modulo Train clustering Model (questo articolo).  
  
    - È inoltre possibile aggiungere un modello di clustering con training esistente dal gruppo **modelli salvati** nell'area di lavoro.

2. Associare il modello sottoposto a training alla porta di input sinistra di **assegnare i dati ai cluster**.  

3. Alleghi un nuovo set di dati come input. 

   In questo set di dati le etichette sono facoltative. In genere, il clustering è un metodo di apprendimento non supervisionato. Non è prevista la conoscenza preliminare delle categorie. Tuttavia, le colonne di input devono essere uguali a quelle utilizzate per il training del modello di clustering o si verifica un errore.

    > [!TIP]
    > Per ridurre il numero di colonne scritte nell'interfaccia dalle stime del cluster, utilizzare [Seleziona colonne nel set di dati](select-columns-in-dataset.md)e selezionare un subset di colonne. 
    
4. Se si desidera che i risultati contengano il set di dati di input completo, includere una colonna in cui vengono visualizzati i risultati (assegnazioni cluster), lasciare selezionata la casella di controllo **Verifica Accodamento o deseleziona solo** risultati.
  
    Se si deseleziona questa casella di controllo, vengono restituiti solo i risultati. Questa opzione può essere utile quando si creano stime come parte di un servizio Web.
  
5.  Eseguire la pipeline.  
  
### <a name="results"></a>Risultati

+  Per visualizzare i valori nel set di dati, fare clic con il pulsante destro del mouse sul modulo, selezionare set di dati dei **risultati**e quindi selezionare **Visualizza**.

