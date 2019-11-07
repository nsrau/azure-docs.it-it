---
title: 'Selezione colonne trasformazione: riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come utilizzare il modulo Select Columns Transform nel servizio Azure Machine Learning per creare una trasformazione che selezioni lo stesso subset di colonne del set di dati specificato.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 383adeb2bd081893d4202239cb97c12bf9a5a170
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73717042"
---
# <a name="select-columns-transform"></a>Selezione trasformazione colonne

Questo articolo descrive come usare il modulo Select Columns Transform in Azure Machine Learning Designer (Preview). Lo scopo del modulo Select Columns Transform è quello di garantire che un set di colonne prevedibile e coerente venga usato nelle operazioni di Machine Learning downstream.

Questo modulo è utile per attività quali l'assegnazione di punteggi, che richiedono colonne specifiche. Le modifiche apportate alle colonne disponibili potrebbero interrompere la pipeline o modificare i risultati.

Utilizzare la trasformazione Seleziona colonne per creare e salvare un set di colonne. Usare quindi il modulo [Apply Transformation](apply-transformation.md) per applicare queste selezioni ai nuovi dati.

## <a name="how-to-use-select-columns-transform"></a>Come utilizzare la trasformazione Seleziona colonne

In questo scenario si presuppone che si desideri utilizzare la selezione delle caratteristiche per generare un set dinamico di colonne che verranno utilizzate per il training di un modello. Per assicurarsi che le selezioni di colonna siano le stesse per il processo di assegnazione dei punteggi, usare il modulo Select Columns Transform per acquisire le selezioni di colonna e applicarle altrove nella pipeline.

1. Aggiungere un set di dati di input alla pipeline nella finestra di progettazione.

2. Aggiungere un'istanza di [selezione di funzioni basata su filtri](filter-based-feature-selection.md).

3. Connettere i moduli e configurare il modulo di selezione delle funzionalità per individuare automaticamente una serie di funzionalità migliori nel set di dati di input.

4. Aggiungere un'istanza di [Train Model](train-model.md) e utilizzare l'output della [selezione di funzioni basata su filtro](filter-based-feature-selection.md) come input per il training.

    > [!IMPORTANT]
    > Poiché l'importanza della funzionalità è basata sui valori della colonna, non è possibile stabilire in anticipo quali colonne possono essere disponibili per l'input per il [training del modello](train-model.md).  
5. Alleghi un'istanza del modulo Select Columns Transform. 

    Questo passaggio genera una selezione di colonna come trasformazione che può essere salvata o applicata ad altri set di impostazioni. Questo passaggio garantisce che le colonne identificate in Selezione funzionalità vengano salvate per il riutilizzo da parte di altri moduli.

6. Aggiungere il modulo [Score Model](score-model.md) . 

   *Non connettere il set di dati di input.* Aggiungere invece il modulo [Apply Transformation](apply-transformation.md) e connettere l'output della trasformazione Selezione funzionalità.

   > [!IMPORTANT]
   > Non è possibile prevedere di applicare la [selezione delle funzioni basata su filtro](filter-based-feature-selection.md) al set di dati di assegnazione dei punteggi e ottenere gli stessi risultati. Poiché la selezione delle caratteristiche è basata sui valori, è possibile che scelga un set di colonne diverso, causando l'esito negativo dell'operazione di assegnazione dei punteggi.
7. Eseguire la pipeline.

Questo processo di salvataggio e applicazione di una selezione di colonna garantisce che lo stesso schema di dati sia disponibile per il training e l'assegnazione dei punteggi.


## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per il servizio Azure Machine Learning. 
