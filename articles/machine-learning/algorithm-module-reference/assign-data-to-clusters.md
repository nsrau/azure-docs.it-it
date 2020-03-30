---
title: 'Assegnazione di dati al cluster: riferimento moduloAssign Data to Cluster: Module Reference'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Assegna dati al cluster in Azure Machine Learning per assegnare un punteggio al modello di clustering.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: 207172f10277589af2b22ae2f41b07234a0925b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477715"
---
# <a name="module-assign-data-to-clusters"></a>Modulo: Assegnazione di dati ai cluster

Questo articolo descrive come usare il modulo *Assegna dati ai cluster* nella finestra di progettazione di Azure Machine Learning (anteprima). Il modulo genera stime tramite un modello di clustering di cui è stato eseguito il training con l'algoritmo di *clustering K-means.*

Il modulo Assegna dati ai cluster restituisce un set di dati contenente le assegnazioni probabili per ogni nuovo punto dati. 

## <a name="how-to-use-assign-data-to-clusters"></a>Come utilizzare Assegna dati ai cluster
  
1. In Progettazione Azure Machine Learning individuare un modello di clustering con training precedente. È possibile creare e eseguire il training di un modello di clustering utilizzando uno dei metodi seguenti:You can create and train a clustering model by using either of the following methods:  
  
    - Configurare l'algoritmo di clustering K-means utilizzando il modulo [K-Means Clustering](k-means-clustering.md) ed eseguire il training del modello usando un set di dati e il modulo Train Clustering Model (questo articolo).  
  
    - È inoltre possibile aggiungere un modello di clustering con training esistente dal gruppo **Modelli salvati** nell'area di lavoro.

2. Collegare il modello sottoposto a training alla porta di input sinistra di **Assegna dati ai cluster**.  

3. Associare un nuovo set di dati come input. 

   In questo set di dati le etichette sono facoltative. In genere, il clustering è un metodo di apprendimento senza supervisione. Non ci si aspetta di conoscere le categorie in anticipo. Tuttavia, le colonne di input devono essere uguali alle colonne utilizzate per il training del modello di clustering oppure si verifica un errore.

    > [!TIP]
    > Per ridurre il numero di colonne scritte nella finestra di progettazione dalle stime del cluster, utilizzare [Seleziona colonne nel set di dati](select-columns-in-dataset.md)e selezionare un sottoinsieme delle colonne. 
    
4. Lasciare selezionata la casella di controllo **Controlla accodamento o deseleziona il risultato solo** se si desidera che i risultati contengano il set di dati di input completo, inclusa una colonna in cui vengono visualizzati i risultati (assegnazioni cluster).
  
    Se si deseleziona questa casella di controllo, vengono restituiti solo i risultati. Questa opzione può essere utile quando si creano stime come parte di un servizio Web.This option might be useful when you create predictions as part of a web service.
  
5.  Inviare la pipeline.  
  
### <a name="results"></a>Risultati

+  Per visualizzare i valori nel dataset, fare clic con il pulsante destro del mouse sul modulo, quindi scegliere **Visualizza**. Oppure Selezionare il modulo e passare alla scheda **Uscite** nel pannello di destra, fare clic sull'icona dell'istogramma nelle **uscite di** porta per visualizzare il risultato.

