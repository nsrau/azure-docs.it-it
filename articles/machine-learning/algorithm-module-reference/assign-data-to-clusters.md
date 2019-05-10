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
ms.openlocfilehash: 1c2d2a02ecfb617551dd9174b87f363d57b151a8
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65467207"
---
# <a name="module-assign-data-to-clusters"></a>Modulo: Assegna i dati ai cluster

Questo articolo descrive come usare il *assegna dati ai cluster* modulo dell'interfaccia visiva grafica Azure Machine Learning. Il modulo Genera stime tramite un modello di clustering che è stato eseguito il training con il *clustering K-means* algoritmo.

I dati di assegnare al modulo cluster restituisce un set di dati che contiene le assegnazioni di probabile per ogni nuovo punto dati. 


## <a name="how-to-use-assign-data-to-clusters"></a>Come usare i dati di assegnare ai cluster
  
1. Nell'interfaccia visiva di Azure Machine Learning, individuare un modello di clustering con Training in precedenza. È possibile creare e addestrare un modello di clustering utilizzando uno dei metodi seguenti:  
  
    - Configurare l'algoritmo di clustering K-means usando il [Clustering K-Means](k-means-clustering.md) modulo e il modello usando il modulo Train Clustering Model (questo articolo) e un set di dati di training.  
  
    - È anche possibile aggiungere un modello di clustering con training esistente dal **modelli salvati** gruppo nell'area di lavoro.

2. Collegare il modello con training per la porta di input sinistra del **assegna dati ai cluster**.  

3. Collegare un nuovo set di dati come input. 

   In questo set di dati, le etichette sono facoltative. In genere, il clustering è un metodo di apprendimento non supervisionato. Non è necessario sapere in anticipo le categorie. Tuttavia, le colonne di input devono essere quello utilizzato per le colonne che sono state usate nel training che del modello di clustering o di errore.

    > [!TIP]
    > Per ridurre il numero di colonne che vengono scritte per l'interfaccia dalle stime di cluster, usare [selezionare le colonne nel set di dati](select-columns-in-dataset.md)e selezionare un subset delle colonne. 
    
4. Lasciare il **controllare che tipo solo accodamenti o deselezionare per solo risultato** casella di controllo selezionata se si desidera che i risultati contengono input set di dati completo, inclusa una colonna che visualizza i risultati (assegnazioni cluster).
  
    Se si deseleziona questa casella di controllo, vengono restituiti solo i risultati. Questa opzione potrebbe essere utile quando si creano stime come parte di un servizio web.
  
5.  Eseguire l'esperimento.  
  
### <a name="results"></a>Risultati

+  Per visualizzare i valori nel set di dati, il pulsante destro del modulo, selezionare **generare set di dati**, quindi selezionare **Visualize**.

